# How to Install & Configure odoo 15 on Ubuntu

## Getting Started

First, it is recommended to update your system with latest version.

You can do it with the following command:

```sh
sudo apt-get update -y
sudo apt-get upgrade -y

```

Once your system is updated, restart the system to apply all the changes:

Next, you will need to install some packages required by Odoo to your server.

You can install all of them by running the following command:

```sh

sudo apt install git python3-pip build-essential wget python3-dev python3-venv python3-wheel libxslt-dev libzip-dev libldap2-dev libsasl2-dev python3-setuptools node-less libpq-dev -y

```

Next,

You can install all of them by running the following command:

```sh

sudo apt-get install python-dev libxml2-dev libxslt1-dev zlib1g-dev libsasl2-dev libldap2-dev build-essential libssl-dev libffi-dev libmysqlclient-dev libjpeg-dev libpq-dev libjpeg8-dev liblcms2-dev libblas-dev libatlas-base-dev

```
After installation of the packages and libraries, need to install some web dependencies also.

```sh

sudo apt-get install -y npm
sudo ln -s /usr/bin/nodejs /usr/bin/node
sudo npm install -g less less-plugin-clean-css
sudo apt-get install -y node-less

```

Install wkhtmltopdf to print PDF reports. ‘0.12.5’ is the recommended version for odoo15. You can install wkhtmltopdf using the following command.

```sh

sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb

sudo dpkg -i wkhtmltox_0.12.5-1.bionic_amd64.deb

sudo apt install -f

```


Once all the packages are installed, you can proceed to the next step.

## Install and Configure PostgreSQL

Before installing PostgreSQL, you will need to create a new system user for Odoo.


You can do it with the following command:


```sh

sudo useradd -m -d /opt/odoo15 -U -r -s /bin/bash odoo15


```

Next, install PostgreSQL with the following command:

```sh

sudo apt-get install postgresql -y


```
Once the installation is completed,

you can check the status of PostgreSQL with the following command:

```sh

sudo systemctl status postgresql

```

Next, you will need to create a new PostgreSQL for Odoo.

You can create it with the following command:

```sh


sudo su - postgres -c "createuser -s odoo15"


```
## Install and Configure Odoo

First, you will need to download the latest version of Odoo from the Git repository.

You can do it with the following command:


```sh

sudo su - odoo15


cd /opt/odoo15/

git clone https://www.github.com/odoo/odoo --depth 1 --branch 15.0

```
After downloading Odoo, create a new Python virtual environment for

the odoo 15 installation with the following command:

```sh
cd /opt/odoo15
python3 -m venv odoo15-venv

```
Next, activate the environment with the following command:

```sh

source odoo15-venv/bin/activate

```

Next, install all the required packages with the following command:

```sh

pip3 install wheel

pip3 install -r odoo/requirements.txt

pip3 install Werkzeug PyPDF2 python-dateutil polib Pillow psycopg2 psutil reportlab

```
Next, disconnect from the environment with the following command:


```sh

deactivate


```

Next, you will need to create a new directory for the custom addons. You can do it with the following command:


```sh

mkdir /opt/odoo15/odoo-custom-addons

```

Next, exit from the odoo15 user with the following command:

```sh

exit

```

After the successful installation of dependencies, we have to configure the Odoo. Odoo will maintain log files.

create a directory to store the log file

```sh

sudo mkdir /var/log/odoo


```
Next will give the complete access of this directory to the user odoo.

```sh

sudo chown odoo15:root /var/log/odoo

```

Next, copy the sample odoo15 configuration file with the following command:

```sh
sudo cp /opt/odoo15/odoo/debian/odoo.conf /etc/odoo15.conf

```

Next, open the /etc/odoo15.conf file with the following command:


```sh

sudo nano /etc/odoo15.conf

```
Make the following changes:

```sh

[options]
; This is the password that allows database operations:
admin_passwd = admin@123
db_host = False
db_port = False
db_user = odoo15
db_password = False
addons_path = /opt/odoo15/odoo/addons,/opt/odoo15/odoo-custom-addons
logfile = /var/log/odoo/odoo15.log


```
Save and close the file, when you are finished.

Next, you will need to create a systemd file for Odoo to manage the Odoo service. You can do this with the following command:


```sh

sudo nano /etc/systemd/system/odoo15.service

```

Add the following lines:

```sh


[Unit]
Description=odoo15
Requires=postgresql.service
After=network.target postgresql.service
[Service]
Type=simple
SyslogIdentifier=odoo15
PermissionsStartOnly=true
User=odoo15
Group=odoo15
ExecStart=/opt/odoo15/odoo15-venv/bin/python3 /opt/odoo15/odoo/odoo-bin -c /etc/odoo15.conf
StandardOutput=journal+console
[Install]
WantedBy=multi-user.target

```

Save and close the file. Then, reload the systemd daemon with the following command:


```sh


sudo systemctl daemon-reload


```


Next, start the Odoo service with the following command:



```sh


sudo systemctl start odoo15


```

You can also check the status of Odoo service with the following command:


```sh

sudo systemctl status odoo15

```

### Live url : http://localhost:8069







