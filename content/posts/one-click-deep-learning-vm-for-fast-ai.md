---
title: One-Click Deep Learning VM for fast.ai
date: '2019-03-01T10:00:11+01:00'
tags:
  - vm
  - fastai
  - jupyter
type: posts
---
In this post I will present the steps I used to set up a one-click deep-learning VMvirtual machine with Google Cloud Platform.

## Creating the VM

Following the fast.ai course [recommendations](https://course.fast.ai/start_gcp.html), the VM is based on the `n1-highmem-8` machine type with a Nvidia Tesla P4 GPU. This GPU is not available in all regions -- I personally went with the region `us-central1-a`.

The boot disk size is set to 200 GB and the "Deep Learning Image" is installed: this Debian-based image includes Anaconda, PyTorch and fastai. In the Management options, preemptibility is switched on.

All commands below are sent after accessing the VM via SSH.

## Jupyter Notebook Configuration

First, a SSL certificate is generated to access the Notebook via HTTPS:

```
mkdir certs
cd certs
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

A password is then created with IPython:

```
ipython
from IPython.lib import passwd
passwd()
```

This command takes a password and returns a cryptographic hash that will be saved in the Jupyter Notebook configuration. This configuration file is generated and edited with the commands:

```
jupyter notebook --generate-config
nano ~/.jupyter/jupyter_notebook_config.py
```

The following lines need to be uncommented or added in the configuration file:

```
c.NotebookApp.certfile = '/home/username/certs/mycert.pem'
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.open_browser = False
c.NotebookApp.password = 'sha1:123456...789'
```

Where `username` is the username and `'sha1:123456...789'` is the hash returned by the `passwd()` command above.

## Systemd Configuration

To access the notebook directly from the VM's IP address, traffic needs to be redirected from the default Jupyter Notebook port to 443. A first systemd service is therefore created:

```
sudo nano /etc/systemd/system/redirect-https.service
```

This oneshot service adds an iptable rule to redirect all TCP traffic on port 443 to Jupyter Notebook's default port. It is edited as follows:

```
[Unit]
Description=Redirect TCP from Port 443 to Port 8888

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/sbin/iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8888

[Install]
WantedBy=multi-user.target
```

To run Jupyter Notebook on startup and with the generated configuration, a second systemd service is created:

```
sudo nano /etc/systemd/system/jupyter.service
```

This simple service starts Jupyter Notebook with the previously generated configuration in the user home directory. It is edited as follows:

```
[Unit]
Description=Jupyter Notebook

[Service]
Type=simple

User=username
Group=username

ExecStart=/opt/anaconda3/bin/jupyter-notebook --config=/home/username/.jupyter/jupyter_notebook_config.py
WorkingDirectory=/home/username

Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Finally, these two services need to be activated:

```
sudo systemctl enable redirect-https.service
sudo systemctl enable jupyter.service
```

That's it! The VM can be started in one click from the Google Cloud Platform and Jupyter Notebook will be available at the VM's external IP address.
