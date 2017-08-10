
# SSH Reachback

### Motivation
Allow an application or user to access files on the source host without a lot of extra commands.  Http protocol would be nice because it's ubiquitous in nature and many userland applications use it.  Additionally, the web server and its traffic should be transported inside the ssh tunnel and only accessible from the remote host.
A concept called ssh carrier (ControlMaster) is proposed to provide flexibility and portability for different deployment scenarios.

### Behavior
A single directory on the origin host is made accessible through a reverse ssh tunnel on the remote host.  When _sshrb_ is invoked, a small python web server starts up on a randomly chosen vacant port on the origin host.  An ssh tunnel is established which forwards an arbitrarily chosen port on the remote host back to the port on which the web server listens.  Now, user applications on the remote host have access to the web server running from the origin host.
Upon exiting the ssh session, _sshrb_ terminates the web server and tears down the ssh tunnel

<img src="https://raw.githubusercontent.com/devlush/reachback/master/docs/reachback.png"/>

### Usage

```bash
[titus@origin ~]$ ./sshrb titus@remote.lemmaplex.org

/var/tmp/basecamp ~~~~~~ http://localhost:54937

[titus@remote ~]$ wget http://localhost:54937
[titus@remote ~]$ exit
```

### Features
- arbitrarily chooses an open port for http on the origin host
- attempts IPv6 first, then falls back to IPv4
- syntax is interoperable with ssh
