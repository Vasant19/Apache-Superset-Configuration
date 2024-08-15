# Apache Superset Installation Guide

## Overview

This repository provides a comprehensive guide and script for installing and configuring Apache Superset on a VirtualBox machine running Ubuntu/Debian. Apache Superset is a modern, open-source data exploration and visualization platform that is highly extensible and easy to use. This project has been developed using Linux Mint 22, but it is compatible with any Ubuntu-based distribution.

<img src="https://github.com/Vasant19/Apache-Superset-Configuration/blob/main/Apache%20superset.png" alt="Architecture Apache Superset" width="600">


## Features

- Automated installation of Apache Superset
- Configuration of a virtual Python environment
- Setup of a Superset configuration file
- Optional systemd service for automatic startup

## Prerequisites

- A VirtualBox machine running Ubuntu/Debian
- Basic knowledge of Linux commands
- Access to the terminal with `sudo` privileges

## Step-by-Step Installation and Configuration

### 1. Update Ubuntu/Debian
```bash
sudo apt update -y && sudo apt upgrade -y
```

### 2. Install Dependencies
```bash
sudo apt-get install build-essential libssl-dev libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev default-libmysqlclient-dev python3.10-venv
```

### 3. Create Application Directory
```bash
sudo mkdir /app
sudo chown $USER /app
cd /app
```

### 4. Set Up Python Environment
```bash
mkdir superset
cd superset
python3 -m venv superset_env
source superset_env/bin/activate
pip install --upgrade setuptools pip
```

### 5. Install Apache Superset
```bast
pip install pillow
pip install apache-superset
```

### 6. Create Superset Configuration File
```bash
touch superset_config.py
export SUPERSET_CONFIG_PATH=/app/superset/superset_config.py
```

### 7. Edit superset_config.py
Add the following configuration to superset_config.py:

```bash
# Superset specific config
ROW_LIMIT = 5000

# Flask App Builder configuration
SECRET_KEY = 'YOUR_OWN_RANDOM_GENERATED_SECRET_KEY'

# SQLAlchemy connection string
SQLALCHEMY_DATABASE_URI = 'sqlite:////app/superset/superset.db?check_same_thread=false'

TALISMAN_ENABLED = False
WTF_CSRF_ENABLED = False

# Mapbox API key
MAPBOX_API_KEY = ''
```
`openssl rand -base64 42`

### 8. Initialize the Database
```bash
pip install flask==2.2.5 flask-wtf==1.0.1
pip install marshmallow_enum

export FLASK_APP=superset
superset db upgrade
superset fab create-admin
superset load_examples
superset init
```
### 9. Create a Script to Run Superset
```bash
nano run_superset.sh

#!/bin/bash
export SUPERSET_CONFIG_PATH=/app/superset/superset_config.py
. /app/superset/superset_env/bin/activate
gunicorn \
      -w 10 \
      -k gevent \
      --timeout 120 \
      -b 0.0.0.0:8088 \
      --limit-request-line 0 \
      --limit-request-field_size 0 \
      --statsd-host localhost:8125 \
      "superset.app:create_app()"

chmod +x run_superset.sh
sh run_superset.sh
```

### 10. Access Apache Superset
Once the setup is complete and Superset is running, you can access the web interface by navigating to http://0.0.0.0:8088(Please modify it if you have previously addressed it in Step 9) in your web browser.

### Conclusion
You now have Apache Superset installed and configured on your Ubuntu/Debian VirtualBox machine. You can start exploring data, creating dashboards, and leveraging Superset's powerful visualization features.
If you face any problems feel free to Open an issue. 
