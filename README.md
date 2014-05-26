dodocon
======
The DigitalOcean Docker cluster toy.

### What?
An opinionated set of scripts and configuration to help Ops bootstrap and
deploy a small cluster of servers to provide basic host level services
then get out of the way.

*dodocon* is built on these tools:
* [DigitalOcean](https://www.digitalocean.com/) -- Cheap servers connected with a local network.
* [Ansible](http://docs.ansible.com/list_of_all_modules.html) -- configuration management.
* [iptables](http://en.wikipedia.org/wiki/Iptables) -- firewall
* [luks](http://en.wikipedia.org/wiki/Dm-crypt) -- files at rest disk encryption
* [tinc](http://www.tinc-vpn.org/) -- mesh switch VPN networking.
* [papertrail](https://papertrailapp.com/plans) -- aggregated logging.
* [docker.io](https://www.docker.io/) -- service encapsulation.
* [Consul](http://www.consul.io/) -- service discovery and DNS.

### Why?
1. Ops is overhead: focus on Dev.
1. In the cloud: the Host is dead, long live the Host.  (Things like CoreOS will eventually take over.)
1. To kick the tires on some fun tools.

There are more automated tools and ways to do some of these things,
but the hope is that these selections provide a mix of simplicity and
functionality.

### How?
Clone this repo to your local machine.

#### Servers
Spin up three DigitalOcean droplets running Ubuntu 12.04.4 x64.

1. web1
1. app1
1. db1

#### Logging
Set up a papertrailapp account and note down the address:port.

#### Users
Create a set of user files in this format and place them in roles/users/files:

1. username.passwd -- passwd file
1. username.pub -- pub key
1. username.puid -- uid/gid number

#### Ansible Hosts
Fill in the public ip address for each server in the `hosts` file.

#### SSH Known Hosts
If you recycle hosts and ip addresses you may need clear old known hosts:

    $ ssh-keygen -f "/home/username/.ssh/known_hosts" -R 123.123.123.123

#### Bootstrap
One time setup of a fresh server:

    $ ansible-playbook -i hosts bootstrap.yml

1. papertrail -- forward syslog to your papertrailapp account
1. cryptfs -- setup encrypted storage and manually enter password on console during reboot
1. unattended-upgrades -- enable automatic nightly security upgrades
1. users -- setup Ops user accounts
1. sshd -- disable root login and use custom port (ideally  less than 1024)

#### Deploy
Run this multiple times to keep your cluster talking and happy:

    $ ansible-playbook -u USERNAME --ask-sudo-pass -i hosts deploy.yml

1. iptables -- lock down traffic to restricted ports and known hosts
1. tinc -- mesh switch VPN networking
1. common -- install some handy applications and config some defaults
1. docker -- install lxc-docker package and tweak the OPTS a bit

### TODO

* Do more than just disable ipv6 iptables.
* Secure papertrail logging with TLS+TCP encryption.
* Use a newer version of tinc.
* Add application logs (docker and container logs) w/ [logspout](https://github.com/progrium/logspout).
* Add [consul](https://github.com/progrium/docker-consul) (or skydock+skydns2 or etcd or whatever) which may or may not require bridging the docker bridge to the vpn.
* Provide better load balancer/web server/application/db examples.

