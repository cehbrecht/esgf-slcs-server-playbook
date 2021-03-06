# esgf-slcs-server-playbook

This repository provides an Ansible playbook that can deploy a [SLCS Server for ESGF](https://github.com/cedadev/esgf-slcs-server).


## Deploying a test VM using Vagrant

To deploy a test VM using Vagrant, just modify the `config.vm.synced_folder` in
the `Vagrantfile` to the location where you checked out the ESGF SLCS Server and
run `vagrant up`.

Many OAuth clients that you may use to test the service require that the OAuth server
runs using HTTPS. Hence everything on the provisioned VM sits behind an Nginx
proxy providing HTTPS.

The default configuration for development requires you to log in to the provisioned
VM and start the Django development server on port 5000:

```
$ vagrant ssh
[vagrant@localhost ~]$ venv/bin/python /code/esgf-slcs-server/manage.py runserver 127.0.0.1:5000
```

Starting the Django development server on port 5000 allows Nginx to pick it up as
a downstream server and pass requests on.

The provisioned VM communicates with the host on a host-only network on which IP
addresses are allocated using DHCP. This host-only network is attached to the
`eth1` interface of the provisioned VM. To find out the IP address that the
provisioned VM is available at, you can use the following commands:

```
$ vagrant ssh
[vagrant@localhost ~]$ ifconfig eth1 | grep -oP 'inet addr:\K\S+'
```

The ESGF SLCS Server will then be available at `https://<ip>`.

Changes to Python files in your `esgf-slcs-server` checkout will cause the Django
development server to reload automatically. However, because the Nginx server is
now serving static resources, any change to Javascript and CSS files will not be
reflected by the server until the Ansible playbook is re-run to collect the static
files:

```
$ vagrant provision --provision-with=ansible
```
