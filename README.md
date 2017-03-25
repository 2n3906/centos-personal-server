# Ansible Playbook: CentOS 7 Personal Server

This is an Ansible playbook for configuring a CentOS 7 VPS into a
reasonable personal server.  Attempts will be made to make it speedy, secure, functional, and interoperable with modern (early 2017) Internet standards.  Newer-than-distribution packages provide the features.  Firewalld, SELinux, and automatic package updates will be enabled for security.  Let's Encrypt certificates will be configured for automatic renewal.

## Things we will be installing
- Basic server security (SELinux, chronyd, yum-cron, and a locked-down sshd configuration)
- [nginx](https://www.nginx.org/) stable (currently 1.10.x), plus some sweet config files and automatic certbot-based certificate renewal. Your first Let's Encrypt certificates will be created automatically if you haven't already done so.
- MariaDB 10.1, automatically secured.
- PHP 7.1, secured and configured with php-fpm enabled.
- Postfix, configured as a send-only mailer for your web server with TLS and SPF/DKIM/DMARC support enabled.
- Fail2Ban, blocking suspicious SSH and HTTP visitors.
- Mosquitto (MQTT broker) and OwnTracks Recorder.

## A note about security

We will attempt to lock down your machine with [openstack-ansible-security](https://github.com/openstack/openstack-ansible-security), which you have to install yourself.

We will lock down TLS ciphers used by SSH, NGINX, Postfix, and Mosquitto using best practices from [Cipherli.st](https://cipherli.st/).

We will configure Let's Encrypt certificates for encryption with NGINX, Postfix, and Mosquitto and enable automatic renewal with the magical certbot.  Keys for this host will be installed in the following locations:
- /etc/letsencrypt/live/HOSTNAME/
- /etc/opendkim/keys/DOMAINNAME/


## How to use

To use, simply:

1. Edit the contents of `group_vars/all` to configure usernames and passwords.
2. Run the playbook.

```sh
ansible-playbook deploy.yml -i hosts
```

You will want to follow up with a couple of tasks.
```sh
# Caveat: the first two tasks are optional and will take hours to run
ssh-keygen -G /tmp/moduli -b 4096
ssh-keygen -T /etc/ssh/moduli -f /tmp/moduli
ssh-keygen -t ed25519 -f ssh_host_ed25519_key < /dev/null
ssh-keygen -t rsa -b 4096 -f ssh_host_rsa_key < /dev/null
```

Install the DNS TXT records indicated in `/root/README.dns-records` to enable SPF/DKIM/DMARC support for your outgoing email.

Add your user account, put it in the "wheel" group to enable passwordless sudo, and possibly set sshd PermitRootLogin to no.

Restart everything.

## Future goals

- [ ] Fix ot-recorder packages so they install properly.
- [ ] Fix aide (as installed by openstack-ansible-security)
- [ ] Install Redis cache
- [ ] Tune MariaDB and PHP-FPM for memory-constrained servers
- [ ] Install Wordpress, wp-cli, and Piwik automatically

## Credits

Much inspiration is taken from Ashley Rich's [wordpress-ansible playbook](https://github.com/A5hleyRich/wordpress-ansible) and [wordpress-nginx config files](https://github.com/A5hleyRich/wordpress-nginx).

## License

See the [UNLICENSE](UNLICENSE) file for license rights and limitations.
