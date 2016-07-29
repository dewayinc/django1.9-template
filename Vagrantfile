# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.define "{{ project_name }}" do |vm_define|
  end

  config.vm.network :private_network, ip: "192.168.33.15"

  config.vm.network "forwarded_port", guest: 80, host: 8000

  config.vm.synced_folder ".", "/home/vagrant/{{ project_name }}/"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "512"
    vb.name = "{{ project_name }}"
  end

  config.vm.provision "dependencies", type:"shell", inline:  <<-SHELL

    # Install dependencies
    apt-get update
    apt-get install -y python2.7-dev python-virtualenv postgresql postgresql-contrib postgresql-server-dev-9.3

    # Create user and database
    sudo -u postgres psql --command="CREATE USER {{ project_name }} WITH PASSWORD '{{ project_name }}';"
    sudo -u postgres psql --command="CREATE DATABASE {{ project_name }} WITH OWNER {{ project_name }};"
    sudo -u postgres psql --command="GRANT ALL PRIVILEGES ON DATABASE {{ project_name }} TO {{ project_name }};"

  SHELL

  config.vm.provision "django", type:"shell", privileged: false, inline:  <<-SHELL

    # Config django
    virtualenv venv
    source venv/bin/activate
    cd {{ project_name }}
    pip install -r requirements.txt
    python manage.py migrate
    python manage.py collectstatic --noinput
  SHELL
end