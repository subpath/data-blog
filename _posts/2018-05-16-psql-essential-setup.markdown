---
layout: post
title:  "PostgreSQL essentials — installation and set up"
date:   2018-05-16 13:18:29 +0500
permalink: "/psql-essential-setup/"
tags:
- 
  name: infrastructure
  style: primary

---

## Step by step instruction for Ubuntu

![psql_logo]({{ site.url }}/{{ site.baseurl }}/assets/2018-05-16-psql-essential-setup/psql_logo.png)
On my daily job from time to time I face the need to install PostgreSQL on some remote server and then restore database from backup file. This case appears when I need to test something (ML-pipeline for instance) in save environment without affecting production database. Surprisingly I could not find comprehensive instruction where all required installation and setting up steps were in one place. So I made my own instruction.

## Installation

1. Import the GPG key for PostgreSQL packages
```bash
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
```

2. Add the repository to your system.
```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```
3. Install PostgreSQL Database Server
```bash
sudo apt-get update
```
```bash
sudo apt-get install postgresql postgresql-contrib
```

## Set up
### Configure PostgreSQL to allow remote connection

By default PostgreSQL is configured to be bound to localhost. You can check this with

```bash
netstat -nlt
```

On the port 5432 (default port for PSQL) you will probably see something like this:

```
tcp        0      0 127.0.0.1:5432          0.0.0.0:*         LISTEN
```

Change configuration in postgresql.conf
```
#postgresql.conf can be found in 

cd /etc/postgresql/YOUR_PSQL_VERSION/main

sudo nano postgresql.conf
```

search for `listen_addresses = 'localhost'`

uncomment this line and change for listen_addresses = '*'

Then save file

Now you need to change pg_hba.conf file also
```bash
sudo nano pg_hba.conf
```
Add this two line at the end of the file:
```
host    all             all              0.0.0.0/0               md5
host    all             all              ::/0                    md5
```
Restart the server
```bash
sudo service postgresql restart
```
Check that everything working
```bash
netstat -nlt
```
You should see line like this:
```
tcp        0      0 0.0.0.0:5432       0.0.0.0:*             LISTEN
```
### Create user and database
```
sudo su - postgres
psqlCREATE USER user_name WITH PASSWORD "pass";
ALTER USER user_name WITH SUPERUSER;#if you will be using UUID it's necessary to create extension
create extension "uuid-ossp";#Create empty database that you latter will restore:
CREATE DATABASE my_db;
```

### Restore database:
```bash
sudo -u postgres pg_restore -d my_db --jobs 4 --verbose db_backup.backup
```
---
<br>
You just set up your PostgreSQL on remote server, configure it to accept external connections and restore database! 

Awesome! Now you have separate database to work with :)