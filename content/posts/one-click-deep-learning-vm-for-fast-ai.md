---
title: One-Click Deep Learning VM for fast.ai
date: '2019-03-01T10:00:11+01:00'
type: posts
---
In this post I will present the steps I used to set up a one-click deep-learning VM with Google Cloud Platform.

## Creating the VM

Following the [recommendations](https://course.fast.ai/start_gcp.html) from the fast.ai course, the VM is based on the `n1-highmem-8` machine type with a Nvidia Tesla P4 GPU. This GPU is not available in all regions -- I personally went with the region `us-central1-a`.

The boot disk size is set to 200 GB and the "Deep Learning Image" is installed. This Debian-based image includes Anaconda, PyTorch and fastai. In the Management options, preemptibility is switched on.

All commands below are sent after accessing the VM through SSH.

## Jupyter Notebook Configuration

First, a certificate is created with openssl to access the Notebook via HTTPS:

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

This command takes a password and returns a cryptographic hash that will be saved in the Jupyter Notebook configuration. The configuration file is generated and edited:

```
jupyter notebook --generate-config
nano ~/.jupyter/jupyter_notebook_config.py
```

The following lines need to be uncommented and set in the configuration file:

```
c.NotebookApp.certfile = '/home/username/certs/mycert.pem'
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.open_browser = False
c.NotebookApp.password = 'sha1:123456...789'
```

## Systemd Configuration

Finally, to be able to access the notebook directly via the VM's IP address, it needs to be launched on the startup and its traffic need to be redirected from the default port to 443. For this two systemd services are created.

A first file is created:

```
sudo nano /etc/systemd/system/redirect-https.service
```

This oneshot service adds an iptable rule to redirect all incoming TCP traffic on port 443 to Jupyter Notebook's default port. Its content is as follows:

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

A second file is created:

```
sudo nano /etc/systemd/system/jupyter.service
```

This simple service starts Jupyter Notebook with the previously generated config in the user home directory. Its content is the following:

```
[Unit]
Description=Jupyter Notebook

[Service]
Type=simple

User=username
Group=username

ExecStart=/opt/leo/anaconda3/bin/jupyter-notebook --config=/home/username/.jupyter/jupyter_notebook_config.py
WorkingDirectory=/home/username

Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

AA
