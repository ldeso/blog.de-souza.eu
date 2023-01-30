---
title: One-Click Deep Learning VM for fast.ai
date: '2019-03-01T10:00:11+01:00'
tags:
  - VM
  - Jupyter
type: posts
---
In this post I will present the steps I used to set up a deep learning virtual machine, ready to use in one click with Google Cloud Platform.

# Creating the VM

Following the fast.ai course [recommendations](https://course.fast.ai/start_gcp.html), the VM is based on the `n1-highmem-8` machine type with a Nvidia Tesla P4 GPU. This GPU is not available in all regions -- I went with the region `us-central1-a`.

The boot disk size is set to 200 GB and the "Deep Learning Image" is installed (this image includes Anaconda, PyTorch and fastai). In the Management options, preemptibility is switched on.

All commands below are sent to the VM via SSH.

# Jupyter Notebook Configuration

First, a SSL certificate is generated to access the Notebook via HTTPS:

```bash
mkdir certs
cd certs
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

Next, a password is created for Jupyter Notebook with IPython:

```python
ipython
from IPython.lib import passwd
passwd()
```

This command takes a password and returns a cryptographic hash that will be saved in the Jupyter Notebook configuration. This configuration file is then generated and edited with the commands:

```bash
jupyter notebook --generate-config
nano ~/.jupyter/jupyter_notebook_config.py
```

Four lines are added to the configuration file:

```python
c.NotebookApp.certfile = '/home/username/certs/mycert.pem'
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.open_browser = False
c.NotebookApp.password = 'sha1:123456...789'
```

Where `username` is the actual username and `'sha1:123456...789'` is the hash returned by the `passwd()` command above.

# Systemd Configuration

To access Jupyter Notebook directly from the VM's IP address, traffic needs to be redirected from the default port to port 443. A systemd service is created for this purpose:

```bash
sudo nano /etc/systemd/system/redirect-https.service
```

This oneshot service adds an iptable rule on startup to redirect all TCP traffic from port 443 to Jupyter Notebook's default port 8888. The file is edited as follows:

```systemd
[Unit]
Description=Redirect TCP from Port 443 to Port 8888

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/sbin/iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8888

[Install]
WantedBy=multi-user.target
```

To run Jupyter Notebook automatically on the VM startup, a second systemd service is created:

```bash
sudo nano /etc/systemd/system/jupyter.service
```

This simple service runs Jupyter Notebook with the previously generated configuration in the user's home directory and is edited as follows:

```systemd
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

Where `username` is replaced by the actual usename. Finally, both services are activated:

```bash
sudo systemctl enable redirect-https.service
sudo systemctl enable jupyter.service
```

That's it! In one click, the VM is started from the Google Cloud Platform console and Jupyter Notebook is available on its external IP address.
