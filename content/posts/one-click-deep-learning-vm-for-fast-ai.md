---
title: One-Click Deep Learning VM for fast.ai
date: '2019-03-01T10:00:11+01:00'
type: posts
---
In this post I will present the steps I used to set up a one-click deep-learning VM with Google Cloud Platform.

## VM Configuration

Following the [recommendations](https://course.fast.ai/start_gcp.html) from the fast.ai course, the VM is based on the `n1-highmem-8` machine type with a Nvidia Tesla P4 GPU. As this GPU is not available in all regions, I went for the region `us-central1-a`.

The boot disk is set to 200 GB and the "Deep Learning Image" is installed: it includes Anaconda, PyTorch and fastai. In the Management options, preemptibility is switched on.

## One-Click Jupyter Notebook

To run Jupyter Notebook on boot on the port 443 of the VM (the port for HTTPS), a few settings are required. All the commands are typed after accessing the VM via SSH.

First, to set up a password run the command `ipython`. From the Ipython command line, type

```python
from IPython.lib import passwd
passwd()
```

This command will let you chose a password to access Jupyter.
