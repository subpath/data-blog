---
layout: post
title:  "Jupyter Hub for a Data Science team"
date:   2018-09-17 13:18:29 +0500
permalink: "/jupyter-hub/"
tags:
- name: infrastructure
  style: danger
- name: 2018
  style: secondary
---

{:refdef: style="text-align: center;"}
![jupyter_logo]({{ '/assets/2018-09-07-jupyter-hub-for-a-data-science-team/jupyter-logo.png' | relative_url }})
{: refdef}

## What is Jupyter Hub?

I assume that you already familiar with [Jupyter](https://jupyter.org/), but in short, it’s pretty handy IDE that’s very popular in Data Science community, because it makes the process of sharing your findings much more easier. And it’s pretty customizable too! I made a [review of my favorite Jupyter Lab extensions](https://subpath.github.io/data-blog/jupyter-lab-extensions/) a few months ago.


Jupyter Hub is an environment that you can run on your remote server, which will give you a shared workspace for your Data Science team, where you will be able to run your code and share findings without painful process of setting up virtual environments and without dependencies conflicts (wow! amazing!).


## When do you need Jupyter Hub?


You probably need it when:

* You have a team of Data Scientists with more than 1 person in it;
* When your team is tired of dependencies conflicts and different versions of environments;
* When you want to use Cloud sources within Jupyter Lab (or notebook), for example, run your code in GPU cluster.

## How to setup Jupyter Hub on a remote machine?

As a compulsory requirement, you will need Python 3 on your server. You will be able to use Python 2.7 from withing Jupyter Lab, but you can run Jupyter Hub only with Python 3.

You can install it with 3 simple commands:

```bash

python3 -m pip install jupyterhub

sudo apt-get install npm nodejs-legacy

npm install -g configurable-http-proxy
```

Now you can run Jupyter Hub with a simple command:

```bash
jupyterhub
```

It will start Jupyter Hub with a default port.

I’ve had difficulties installing configurable-http-proxy on Linux, and the only solution that worked for me is installing it with conda command.

1. download anaconda package
```bash
wget  http://repo.continuum.io/archive/Anaconda3-4.3.0-Linux-x86_64.sh
```
2. install anaconda
```bash
bash Anaconda3-4.3.0-Linux-x86_64.sh
```
3. install configurable-http-proxy
```bash
conda install -c conda-forge configurable-http-proxy
```

## The first command to run

Jupyter Hub allows you to generate a config file with a simple command:
```bash
jupyterhub --generate-config
```

this command will create python file `jupyterhub_config.py` that has almost everything you need inside and with pretty good instructions.


## Some additional setup you may need:

### Nginx

If you are using VPN you may need to configure nginx, because you want to make sure that Jupyter Hub is available for remote users, but by default, it runs on localhost.

Here is my piece of nginx config file:

```conf
location / {
    proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;proxy_redirect off;
    proxy_pass http://127.0.0.1:8000;}
```

### Supervisor

I like to use supervisor as a process control system. Because if you connect through ssh to your server and start Jupyter Hub, unfortunately, it will stop as soon as your ssh connection will be closed.

Here is a piece of the config file for supervisor:

```conf
[program:jupyterhub]
directory=/home/user/jupyter
command=/home/user/jupyterhub
stdout_logfile=/home/user/logs/jupyter.log
stderr_logfile=/home/user/logs/jupyter.log
autostart=true
autorestart=true
user=entropy
```

If it’s your first time hearing about Nginx and Supervisor, below I listed pretty useful tutorials on that.

### Jupyter Hub authentication

For security sake, it is always good to use user authentication, luckily it’s more than easy with Jupyter Hub.

You will need to install oathenticator:
```bash
pip3 install oauthenticator
```

For example, you can setup authentication with GitHub account, simply add two lines in `jupyterhub_config.py`

```python
from oauthenticator.github import GitHubOAuthenticatorc.JupyterHub.authenticator_class = GitHubOAuthenticator
```

![jupyter-login]({{ '/assets/2018-09-07-jupyter-hub-for-a-data-science-team/jupyter-login.png' | relative_url }})

*login screen you will see in Jupyter Hub*


![github-login]({{ '/assets/2018-09-07-jupyter-hub-for-a-data-science-team/github-login.png' | relative_url }})

*It will redirect you to GitHub sigh in page*

### There are many different ways to authenticate users:
* [Auth0](https://github.com/jupyterhub/oauthenticator/blob/master/oauthenticator/auth0.py)
* [Azure](https://github.com/jupyterhub/oauthenticator#azure-setup)
* [Bitbucket](https://github.com/jupyterhub/oauthenticator/blob/master/oauthenticator/bitbucket.py)
* [CILogon](https://github.com/jupyterhub/oauthenticator/blob/master/oauthenticator/cilogon.py)
* [GitHub](https://github.com/jupyterhub/oauthenticator#github-setup)
* [GitLab](https://github.com/jupyterhub/oauthenticator#gitlab-setup)
* [Globus](https://github.com/jupyterhub/oauthenticator#globus-setup)
* [Google](https://github.com/jupyterhub/oauthenticator#google-setup)
* [MediaWiki](https://github.com/jupyterhub/oauthenticator/blob/master/oauthenticator/mediawiki.py)
* [Moodle](https://github.com/jupyterhub/oauthenticator#moodle-setup)
* [Okpy](https://github.com/jupyterhub/oauthenticator#okpyauthenticator)
* [OpenShift](https://github.com/jupyterhub/oauthenticator#openshift-setup)



### Here how Jupyter Hub looks like for admin

In `jupyterhub_config.py` you can setup admin users and they will have access to admin panel, where you can manage other people notebooks.

![jupyter-admin]({{ '/assets/2018-09-07-jupyter-hub-for-a-data-science-team/jupyter-hub-admin.png' | relative_url }})

## In conclusion:

* Jupyter Hub is awesome and super handy for Data Science / Machine Learning team
* It allow you to create a shared environment and forget about the headache of dependency conflicts.
* It may be a tedious process to set up the first time, but it definitely worth it!

## References:

* Jupyter Hub — [https://github.com/jupyterhub/jupyterhub](https://github.com/jupyterhub/jupyterhub)
* JupyterHub oathentication — [https://github.com/jupyterhub/oauthenticator](https://github.com/jupyterhub/jupyterhub)
* Nginx proxy configuration — [https://www.nginx.com/resources/wiki/start/topics/examples/full/](https://github.com/jupyterhub/jupyterhub)
* Supervisor configuration — [https://github.com/illagrenan/ubuntu-supervisor-configuration](https://github.com/illagrenan/ubuntu-supervisor-configuration)