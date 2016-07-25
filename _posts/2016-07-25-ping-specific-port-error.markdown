---
layout: post
title:  "Why you can't ping a specific port"
date:   2016-07-25 16:00:00 +0530
categories: computer-network
tags: computer-network
excerpt: The reason for this is, ping is nothing but a ICMP echo request and echo reply and there is no concept of port numbers in ICMP.
---

Most of us when a service or a website is down, we try pinging it. If that service or website is running on a specific port and we try pinging it with that port number, we will get an error.


{% highlight html %}
root@spark:~# ping -c3 159.203.216.179:80
ping: unknown host 159.203.216.179:80
{% endhighlight %}

The reason for this is, ping is nothing but a ICMP `echo request` and `echo reply` and there is no concept of port numbers in ICMP.

ICMP protocol is a part of Internet layer of the Internet Protocol Suite. HTTP is at the top layer, i.e. application layer, while TCP and UDP form the transport layer.

![Internet Protocol layers]({{ site.url }}/assets/images/internet_layers.png)

TCP deals with the actual data exchange on the internet. UDP also does data exchange but does it fast without the handshaking, error detection or delivery guarantee.

There is going to be lot of services running on a machine. So TCP and UDP need to know which service they should deliver the message to. For that purpose, they use port numbers.

On the other hand, ICMP is a protocol that is designed specifically for diagnostic purposes.

> It is used by network devices, like routers, to send error messages indicating, for example, that a requested service is not available or that a host or router could not be reached. ICMP can also be used to relay query messages.

Since ICMP is not doing ‘real’ message exchange between machines, ICMP packets don’t need to have port numbers.

**So if a ping succeeds, all it means is the destination machine is on the network, i.e. it is reachable. It doesn’t guarantee that a web server or anything else on that machine is running.**

All ping does is knocking on the door. If you want to find out whether a service on a specific port is up, you should use netcat or telnet which allows you to connect to a machine on a specific port.

{% highlight html %}
root@spark:~# telnet www.google.com 80
Trying 172.217.3.196…
Connected to www.google.com.
Escape character is ‘^]’.
^C
Connection closed by foreign host.
{% endhighlight %}
