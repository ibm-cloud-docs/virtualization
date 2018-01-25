---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-17"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Understanding UBC / User Beancounter Values

Use SLM Mode if UBC is not understood. SLM is easier and provides the same type of control.

|Name|Type|Description|
|---|---|---|
|*Main parameters*|||
|`numproc`|*system*|Number of processes and threads|
|`numtcpsock`|*system*|Number of TCP sockets|
|`numothersock`|*system*|Number of sockets other than TCP|
|`vmguardpages`|*system*|Memory allocation guarantee|
|`cpuunits`|*cpu*|CPU power|
|`diskspace`|*disk*|Disk space quota|
|`rate`|*network*|Network bandwidth|
|`ratebound`|*network*|Whether network bandwidth is limited|
|*Additional parameters*|||
|`kmemsize`|*system*|Size of unswappable kernel memory allocated for processes in this Container|
|`tcpsndbuf`|*system*|Total size of TCP send buffers|
|`tcprcvbuf`|*system*|Total size of TCP receive buffers|
|`othersockbuf`|*system*|Total size of UNIX-domain socket buffers, UDP and other datagram protocol send buffers|
|`dgramrcvbuf`|*system*|Receive buffers of UDP and other datagram protocols|
|`oomguardpages`|*system*|The guaranteed amount of memory for the case the memory is over-booked (out-of-memory kill guarantee)|
|`privvmpages`|*system*|Memory allocation limit|
|`lockedpages`|*system*|Process pages not allowed to be swapped out (pages locked by [mlock(2) ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://linux.die.net/man/2/mlock))|
|`shmpages`|*system*|Total size of shared memory (interprocess communication or IPC, shared anonymous mappings and tmpfs objects) in pages|
|`physpages`|*system*|Total number of RAM pages used by processes|
|`numfile`|*system*|Number of open files|
|`numflock`|*system*|Number of file locks|
|`numpty`|*system*|Number of pseudo-terminals|
|`numsiginfo`|*system*|Number of siginfo structures|
|`dcachesize`|*system*|Total size of dentry and inode structures locked in memory|
|`numiptent`|*system*|Number of NETFILTER (IP packet filtering) entries|
|`cpulimit`|*cpu*|Limit on CPU consumption|
|`diskinodes`|*disk*|Disk inode (filesystem object) quota|
|`quotatime`|*disk*|Disk quota grace period|
|`quotaugidlimit`|*disk*|Limit on the number of uid and gid accounting entries|

You can access VzLinuxUBCMgmt.pdf or view it in HTML, within PIM on your server by going to:
**Management > Support > Downloads > Management of UBC Resources Administrator's Guide**

For more information about UBC, see, [OpenVZ Virtuozzo Containers, Category: UBC ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://wiki.openvz.org/Category:UBC).
**Note:** Not everything in OpenVZ is the same as Virtuozzo.
