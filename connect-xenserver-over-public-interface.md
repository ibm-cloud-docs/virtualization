---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Connecting to XenServer over a public interface

By default the {{site.data.keyword.BluSoftlayer}} installation of XenServer is configured to use the private network interface for all management connections. If you have connection issues on the private network, open a support ticket.

Changing this configuration is a security risk and is not advised.
{:tip}

If you want to reconfigure XenServer to use the public interface, run the following commands as root from the host (host should not be pooled!): 

1. *xe pif-list*

2. Make note of the uuid of the interface that you want to enable (most likely eth1 or bond1).

3. Run the following command:

        xe host-management-reconfigure pif-uuid=

These changes take place immediately and do not require a restart.
