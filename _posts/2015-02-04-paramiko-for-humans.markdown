---
layout: post
title:  "Paramiko"
date:   2015-02-05 10:00:45
categories:
  - python
  - ssh
---

So you want to initiate an SFTP connection with [paramiko][paramiko].  In an
ideal world, you already have the host key in your `~/.ssh/known_hosts` but
there are plenty of cases where this is not the case.  So what are your
options...

You could write the host key to `~/.ssh/known_hosts` but something about that
feels *wrong* to me.

You could just use the `paramiko.client.AutoAddPolicy` to just automatically
add the host key if it's missing when trying to connect.

Or you could jump through these ridiculous hoops, assuming you have the host
key as a string and you want to use it to verify the connection.

{% highlight python %}
import paramiko
import tempfile
import base64

hostname = 'github.com'
key_type = 'ssh-rsa'
key_string = "AAAAB3NzaC1yc2EAAAABIwAAAQEAq2A7hRGmdnm9tUDbO9IDSwBK6TbQa+PXYPCPy6rbTrTtw7PHkccKrpp0yVhp5HdEIcKr6pLlVDBfOLX9QUsyCOV0wzfjIJNlGEYsdlLJizHhbn2mUjvSAHQqZETYP81eFzLQNnPHt4EVVUh7VfDESU84KezmD5QlWpXLmvU31/yMf+Se8xhHTvKSCZIFImWwoG6mbUoWf9nzpIoaSjB+weqqUUmpaaasXVal72J+UX2B+2RPW3RcT0eOzQgqlJL3RKrTJvdsjE3JEAvGq3lGHSZXy28G3skua2SmVi/w4yCE6gbODqnTWlg7+wC604ydGXA8VJiS5ap43JXiUFFAaQ"

key = paramiko.RSAKey(data=base64.b64decode(key_string))
host_keys = paramiko.HostKeys()
host_keys.add(hostname, key_type, key)

f = tempfile.NamedTemporaryFile()
host_keys.save(f.name)
f.flush()

c = paramiko.SSHClient()
c.load_host_keys(f.name)
c.connect(h, username="username", password="password")
{% endhighlight %}

Wow!

My collegue with whom I've been pairing with today remarked that this area is
prime for a Kenneth Reitz to swoop in and write an **SSH For Humans** library.

[paramiko]: http://paramiko-docs.readthedocs.org/en/latest/index.html
