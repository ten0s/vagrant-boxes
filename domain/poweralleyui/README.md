# Creating PowerAlleyUI environment box based on CentOS 6.5 x86_64 Mono 3.2.8

## Create the box using Ansible

<pre>
$ vagrant up
$ vagrant ssh-config
</pre>

Make sure ssh config matches data in **inventory** file.
Then check the connection.

<pre>
$ ansible machine -i inventory -m ping
machine | success >> {
    "changed": false,
    "ping": "pong"
}
</pre>

Provision the box.

<pre>
$ ansible-playbook setup.yml -i inventory
</pre>

## Create the box manually

<pre>
$ vagrant up
$ vagrant ssh
</pre>

Inside the virtual machine:

<pre>
cd /vagrant
</pre>

MySQL

<pre>
sudo yum -y install mysql-server-5.1.73
sudo cp files/etc/my.cnf /etc/
sudo service mysqld start
sudo chkconfig mysqld on
mysql -u root
GRANT ALL PRIVILEGES ON *.* TO 'pmm'@'localhost' IDENTIFIED BY 'g0ds4v3th3Qu33n' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'pmm'@'%' IDENTIFIED BY 'g0ds4v3th3Qu33n' WITH GRANT OPTION;
exit
</pre>

Nginx

<pre>
sudo yum -y install nginx-1.0.15
sudo cp files/etc/nginx/nginx.conf /etc/nginx/
sudo cp files/etc/nginx/conf.d/server.conf /etc/nginx/conf.d/
sudo cp files/etc/nginx/fastcgi_params /etc/nginx/fastcgi_params
sudo service nginx start
sudo chkconfig nginx on
</pre>

Supervisord

<pre>
sudo yum -y install python-setuptools
sudo easy_install pip
sudo pip install supervisor
sudo mkdir -p /etc/supervisor.d
sudo mkdir -p /var/log/pmm
sudo chown -R bms:bms /var/log/pmm
sudo cp files/etc/supervisord.conf /etc/supervisord.conf
sudo cp files/etc/supervisor.d/poweralleyui.conf /etc/supervisor.d/
sudo cp files/etc/init.d/supervisord /etc/init.d/
sudo mkdir -p /opt/bin
sudo cp files/opt/bin/poweralleyui.sh /opt/bin/
sudo chkconfig supervisord on
</pre>

PowerAlleyUI

<pre>
sudo tar xfz poweralleyui-1.3.0.tar.gz -C /opt/
sudo chown -R bms:bms /opt/poweralleyui-1.3.0
sudo ln -s /opt/poweralleyui-1.3.0 /opt/poweralleyui
</pre>

Start everything

<pre>
sudo service mysqld restart
sudo service nginx restart
sudo service supervisord restart
exit
</pre>

## Package the provisioned box

<pre>
$ vagrant package --output ten0s_centos6.5_mono3.2.8_poweralleyui_x86_64.box
$ vagrant box add -f ten0s/ten0s_centos6.5_mono3.2.8_poweralleyui_x86_64 ./ten0s_centos6.5_mono3.2.8_poweralleyui_x86_64.box
$ vagrant destroy --force
</pre>