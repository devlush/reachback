
# SSH Reachback

### Motivation
Allow access from a remote host to files on the origin host without a lot of extra commands.  Http protocol would be nice because of its ubiquitous nature and simplicity.  The web server traffic should be transported inside an ssh tunnel and made available only from the remote host.  A concept using ssh control master sockets is proposed for flexibility and portability across different deployment scenarios.

### Behavior
A single directory on the origin host is made accessible via http through a reverse ssh tunnel.  When _sshrb_ is invoked, a small python web server started on the origin host binds to a randomly selected port.  An ssh tunnel is established which forwards traffic from an arbitrarily selected port on the remote host to the randomly selected web server port on the origin host.  User applications on the remote host can now securely access the web server running on the origin host.
Upon exiting the ssh session, _sshrb_ terminates the web server and tears down the ssh tunnel.

![](https://github.com/devlush/reachback/raw/master/docs/reachback.png)

### Usage

```
[titus@origin ~]$ ./sshrb titus@remote.lemmaplex.org

/var/tmp/basecamp ~~~~~~ http://localhost:54937

[titus@remote ~]$ wget http://localhost:54937
[titus@remote ~]$ exit
```

### Features
- doesn't require root permissions
- syntax is interoperable with ssh
- arbitrarily chooses an open port for http on the origin host
- attempts IPv6 first, then falls back to IPv4


### Synopsis
You can manually reproduce _sshrb_ behavior:
```
[titus@origin ~]$ echo "Hello World!" > ./index.html
[titus@origin ~]$ python -m SimpleHTTPServer 8080 &
[titus@origin ~]$ ssh -R 0:localhost:8080 titus@remote.lemmaplex.org
54937

[titus@remote ~]$ wget http://localhost:54937
[titus@remote ~]$ exit

[titus@origin ~]$ pkill python
```
_Note: This method causes python to listen on all interfaces_
