NTP configuration - JUNOS Feature Request
=========================================

The NTP service on JUNOS systems will, by default, listen on UDP port
123 on all interfaces and configured addresses.  Modern versions of the
ntpd reference implementation includes an *interface* command to control
specific interfaces or addresses the NTP service opens and listens on.
In addition, there exists a *restrict* configuration option that
controls how the NTP daemon will handle received NTP packets from hosts,
including remote time servers.  Modern JUNOS software appears to be
using an ntpd implementation that includes these features.
Unfortunately the interface control capability and restrict options are
not currently available as JUNOS configuration options in the `system
ntp` hierarchy.  We the undersigned wish to express our desire for the
equivalent JUNOS configuration statements.  Together we request Juniper
Networks commit to adding these capabilities into JUNOS.  These
capabilities are important for network operators to completely and more
easily limit access to the NTP process from outside the local system.


Background and Rationale
========================

Networked systems on the Internet must be resilient to attack and abuse.
No where is this more true than with infrastructure devices such the
enterprise and service provider routers from Juniper Networks.  Network
operators have devised a number of best practices for router management
that include a variety of security protections such as firewall filter
rules, cryptographically-strong authentication mechanisms, and anomaly
monitoring capabilities.

Network infrastructure equipment must often run, and network operators
generally expect, certain network client services such as time
synchronization, remote logging, and traffic flow collection.  Generally
these largely client-side services are only ever intended to be
accessible directly from the local routing operating system itself, and
rarely if ever from  random, unsolicited remote hosts.  It is generally
considered best practice to eliminate a network listener if the service
never expects to receive communications from a remote host.  In other
cases where a network service must communicate with itself over a local
socket, the network listener should only bind to a loopback address.

Open and accessible NTP services on router systems have shown to pose a
particularly acute threat in practice.  Open NTP services from routing
systems have been implicated in real-world DDoS attacks and information
leak cases.  Many network operators have taken steps to protect
NTP services from abuse by configuring loopback firewall filters that
restrict access to the NTP service from all, but a handful of statically
configured time systems the router is set to synchronize time with.

Generally, in enterprise and service provider router systems, the NTP
service is only ever used as a client service, and rarely if ever do
these routers act as NTP servers for other remote systems.  Therefore we
urge Juniper to provide the ability to configure the *interface* and
*restrict* options for the NTP service for those operators that wish to
remove any possibility of access to the NTP service from remote systems.


Suggested JUNOS Syntax Changes
==============================

We are seeking two specific features to the `system ntp` configuration
hierarchy syntax.  We first summarize these changes, provide reference
`ntp.conf` configuration examples and suggest corresponding JUNOS
configuration statements.


`restrict` option
-----------------

The `restrict` access control option dictates what messages the NTP
process will receive, act on, and respond to.  The reference ntpd
provides a number of options and flags to the restrict option, but we
argue that only a handful are necessary for Juniper routers.  We
recommend the following `restrict` options be supported:

```
  [edit system]
  ntp {
      restrict [default | address] {
          ignore;
          nomodify;
          noquery;
          nopeer;
          noserve;
          notrap;
          notrust;
      }
  }
```


`interface` option
------------------

The `interface` configuration option controls which interface or address
will be opened by ntpd and listen for incoming messages for processing.
We recommend the following `interface` options be supported:

```
  [edit system]
  ntp {
      interface [listen | ignore | drop] {
          all;
          ipv4;
          ipv6;
          [interface];
          address[/prefix];
      }
  }
```

 
References
==========

 * [ntpd Access Control Commands and
   Options](https://www.eecis.udel.edu/~mills/ntp/html/accopt.html)

 * [ntpd Miscellaneous Commands and
   Options](https://www.eecis.udel.edu/~mills/ntp/html/miscopt.html)

 * [Attacking the Network Time
   Protocol](https://www.cs.bu.edu/~goldbe/NTPattack.html)

 * [Secure NTP
   Template](http://www.team-cymru.org/secure-ntp-template.html)

 * [Time Management Administration Guide for Routing
   Devices](http://www.juniper.net/documentation/en_US/junos/information-products/pathway-pages/system-basics/time-management.htm)


Signatories
===========

 * Andrew Gallo, Principal Network Engineer, The George Washington University
 * John Kristoff, Network Architect, DePaul University
 * Michael H. Lambert, GigaPOP Manager, Pittsburgh Supercomputing Center/3ROX
 * Karl Newell, Network Security Engineer, Internet2
