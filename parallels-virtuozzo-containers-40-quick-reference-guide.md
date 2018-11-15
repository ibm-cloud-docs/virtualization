---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"
---

# Parallels Virtuozzo Containers 4.0 quick reference guide

Use this information to find Virtuozzo container utilities and common terms. 

## General utilities

General utilities are for day-to-day maintenance tasks.
  
|General Utilities||
|---|---|
|vzctl|Utility to control Containers.|
|vzlist|Utility to view a list of Containers existing on the Node with additional information.|
|vzquota|Utility to control Virtuozzo Containers disk quotas.|
{: caption="Table 1. General utilities" caption-side="top"}

## Licensing utilities

Licensing utilities are used to install a new license, view the license state, or generate a license request for a new license.

|Licensing Utilities||
|---|---|
|vzlicview|Utility to display the Virtuozzo license status and parameters.|
|vzlicload|Utility to manage Virtuozzo licenses on the Hardware Node.|
|vzlicupdate|Utility to activate the Virtuozzo Containers installation, update the Virtuozzo licenses installed on the Hardware Node, or transfer the Virtuozzo license from the Source Node to the Destination Node.|
{: caption="Table 2. Licensing utilities" caption-side="top"}

## Container migration utilities

Container migration tools are used to migrate containers between hardware nodes or within one hardware node.

|Container Migration Utilities||
|---|---|
|vzmigrate|Utility for migrating containers from one hardware node to another.|
|vzmlocal|Utility for the local cloning or moving of the containers.|
|vzp2v|Utility to migrate a physical server to a container on the node.|
|vzv2p|Utility to migrate a container to a physical server.|
{: caption="Table 3. Container migration utilities" caption-side="top"}

## Container backup utilities

Container backup utilities are used to back up and restore the container private areas, configuration files, action scripts, and quota information.

|Container Backup Utilities||
|---|---|
|vzbackup|Utility to back up Containers.|
|vzrestore|Utility to restore backed up Containers.|
|vzabackup|Utility to back up Hardware Nodes and their Containers. This utility requires the Parallels Agent software.|
|vzarestore|Utility to restore backed up Hardware Nodes and Containers. This utility requires the Parallels Agent software.|
{: caption="Table 4. Container backup utilities" caption-side="top"}

## Template management utilities

Template management tools are used to create templates and to maintain and install applications into a container.

|Template Management Utilities||
|---|---|
|vzpkg|Utility to manage OS and application EZ templates either inside your containers or on the hardware node itself.|
|vzmktmpl|Utility to create OS and application EZ templates.|
|vzveconvert|Utility to convert containers based on Virtuozzo standard templates to EZ template-based containers.|
|vzpkgproxy|Utility to create caching proxy servers for handling OS and application EZ templates.|
|vzrhnproxy|Utility to create RHN proxy servers for handling the packages included in the RHEL 4 and RHEL 5 OS EZ templates.|
|vzpkgls|Utility to get a list of templates available on the hardware node and in containers.|
|vzpkginfo|Utility to get the information on any template installed on the hardware node.|
|vzpkgcreat| Create a new package set from binary RPM or DEB files.|
|vzpkgadd|Utility to add a template to a Container.|
|vzpkglink|Utility to replace real files inside a container with symlinks to the files on the node.|
|vzpkgrm|Utility to remove a template from a container.|
|vzpkgcache|Update a set of preinstalled container archives after new template installation.|
{: caption="Table 5. Template management utilities" caption-side="top"}

## Supplementary tools

Supplementary tools are used for miscellaneous tasks in the hardware node and container context.

|Supplementary Tools||
|---|---|
|vzup2date|Utility to update your Virtuozzo software and templates.|
|vzup2date-mirror|Utility to create local mirrors of the Virtuozzo official repository.|
|vzfsutil|Utility for the VZFS optimization and consistency checking.|
|vzcache|Utility to gain extra disk space by caching the files identical in different containers.|
|vzsveinstall|Utility to create the Service Container on the Hardware Node.|
|vzsveupgrade|Utility to update the packages inside the Service Container.|
|vzps|Utility working as the standard ps and top utilities, with container-related functions added.|
|vztop|Utility working as the standard ps and top utilities, with container-related functions added.|
|vzsetxinetd|Utility to switch some services between stand-alone mode and `xinetddependent` mode.|
|vzdqcheck|Print file space current usage from quota’s point of view.|
|vzdqdump|Utility to dump the Container user or group quota limits and grace times from the kernel or the quota file or for loading them to a quota file.|
|vzdqload|Utility to dump the Container user or group quota limits and grace times from the kernel or the quota file or for loading them to a quota file.|
|vznetstat|Utility that prints network traffic usage statistic by containers.|
|vzcpucheck|Utility for checking CPU usage by containers.|
|vzmemcheck|Utility for checking the hardware node and container current memory parameters.|
|vzcalc|Utility to calculate resource usage by a container.|
|vzcheckovr|Utility to check the current system overcommitment and safety of the total resource control settings.|
|vzstat|Utility to monitor the hardware node and container resources consumption in real time.|
|vzpid|Utility that prints container ID that the process belongs to.|
|vzsplit|Utility to generate container configuration file sample, “splitting” the hardware node into equal parts.|
|vzcfgscale|Utility to scale the container configuration.|
|vzcfgvalidate|Utility to validate container configuration file correctness.|
|vzcfgconvert|Utility to convert Virtuozzo 2.0.2 container configuration files to Virtuozzo 2.5.x format.|
|vzstatrep|Utility to analyze the logs that are collected by vzlmond and to generate statistics reports based on these logs (in the text and graphical form).|
|vzreport|Utility to draw up a problem report and to automatically send it to the Parallels support team.|
|vzhwcalc|Utility to scan the main resources on any Linux server and create a file where this information is specified.|
|vzveconvert|Utility to convert the containers based on Virtuozzo standard OS templates to the EZ template-based ones.|
|vznetcfg|Utility to manage network devices on the hardware node.|
|vzmtemplate|Utility to migrate the installed OS and application templates from the one hardware node to another.|
{: caption="Table 6. Supplemental tools" caption-side="top"}

## Common Terms

|Common terms||
|---|---|
|Hardware Node|Hardware node (or HN) is a physical computer that hosts containers.|
|Container|Container is functionally identical to an isolated stand-alone server with its own IP addresses, processes, files, users, its own version, its own configuration files, its own applications, system libraries, and other server functions. Containers share hardware nodes and the same OS kernel, but they are isolated from each other. Each container has a unique numerical ID starting with 1.|
|Host Operating System|Host operating system (or Host) is an operating system that is installed on the hardware node. It is also called container 0, in contrast to real containers that have IDs that start with 1.|
|Template|Template (or package set) is a set of original application files that are repackaged for mounting over Virtuozzo File System (VZFS). Two types of templates are available in Virtuozzo: OS templates that can be used to create new containers on their basis and application templates that can be added to containers after their creation.|
|PIM|Parallels Infrastructure Manager (or PIM) is a web-based management tool that is designed for Host administrators.|
|PMC|Parallels Management Console (or PMC) is a remote management tool with a graphical user interface that is designed for Host administrators.|
|PPP|Parallels Power Panel is a web-based management tool that is designed for Container owners.|
{: caption="Table 7. Common terms" caption-side="top"}

