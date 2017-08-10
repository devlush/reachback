
# SSH Reachback

### Goal/Motivation

Allow an application or user to access files on the source host without a lot of extra commands.  Http protocol would be nice because it's ubiquitous in nature and many userland applications use it.  Additionally, the web server and its traffic should be transported inside the ssh tunnel and only accessible from the remote host.
The ssh carrier concept (ControlMaster) is utilized to provide flexibility and portability accross many different scenarios.

### Behavior Description
A single directory on the origin host is made accessible through a reverse ssh tunnel on the remote host.  When `sshrb` is invoked, a small python web server starts up on a randomly chosen vacant port on the origin host.  Then, an ssh tunnel is established which forwards an arbitrarily chosen port on the remote host back to the port on which the web server is listening.  Now, user applications on the remote host have access to the web server that's hosted on the origin host.

Upon exiting the ssh session, **sshrb** terminates the web server and tears down the ssh tunnel



<img src="https://raw.githubusercontent.com/devlush/reachback/master/docs/reachback.png" width="477" height="105" border="20" />




### Features
- arbitrarily chooses an open port for http on the origin host
- attempts IPv6 first, then falls back to IPv4
- syntax is interoperable with ssh

### Usage

```bash
[titus@origin ~]$ ./sshrb titus@remote.lemmaplex.org

/var/tmp/basecamp ~~~~~~ http://localhost:54937

[titus@remote ~]$ wget http://localhost:54937
[titus@remote ~]$ exit
```
