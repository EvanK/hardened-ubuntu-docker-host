# Hardened Ubuntu for Docker Cloud's Bring-Your-Own-Node

Borrowing (heavily in some cases) from several online resources, this uses ansible to lock down a server for use as a Docker Cloud node.

* [Ubuntu Security Guides](https://help.ubuntu.com/community/Security)
* [Matt Brock's Security hardening on Ubuntu Server 14.04](http://blog.mattbrock.co.uk/hardening-the-security-on-ubuntu-server-14-04/)
* [Ryan Eschinger's Securing a Server with Ansible](http://ryaneschinger.com/blog/securing-a-server-with-ansible/)
* [BookOfZeus Harden Ubuntu guides](http://bookofzeus.com/harden-ubuntu/)

### How do I use this?

Good question! You'll need a few things beforehand:

* an ubuntu server with python2
* an existing user on said server with sudo access
* a password or keyfile for said user

### First run

Run the script, and follow the prompts:

```
./bin/dp
```

It will ask you for a hostname (which will be resolved to an ip address) and valid ssh credentials.

An initial connection is made as the provided user, and a `deploy` user is set up with private key access. **You may be prompted for an ssh and/or sudo password.**

Next, you'll be prompted for any custom ports (and corresponding protocols) to open in the firewall.

> Note: Ports for ssh and the docker cloud agent are set up automatically, so there's no need to specify them.

Finally, a series of security actions are taken:

* Automatic security updates through the `unattended-upgrades` package
* Tweaks to IPv4 networking
* An application firewall with `ufw` and `fail2ban`, **including ssh rate limiting & brute force protection**
* Tweaks to sshd, **including disabling password authentication and `root` login**

### Connecting to your hardened server

From here, you can connect as the `deploy` user, with a keypair found in `files/ssh/`:

```
ssh -i ./files/ssh/192.168.33.10 deploy@192.168.33.10
```

### Opening new ports

If you need another port exposed, simply run the script again and specify the hostname (or ip address):

```
./bin/dp

? Hostname or IP Address: 
```

It will detect your existing ssh keys, as well as ansible inventory and host variables. Follow the prompts, and add any new ports.

```
! Opening these ports:
   443 on tcp
   80 on tcp

? Add additional ports, space delimited (or hit ENTER): 3306
```
