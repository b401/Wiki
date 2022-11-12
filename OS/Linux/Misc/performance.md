---
title: Linux performance 
author: i4
date: M11-02-2022
---

# Performance
**Requirement**: sysstat

[source](https://netflixtechblog.com/netflix-at-velocity-2015-linux-performance-tools-51964ddb81cf)

## Uptime
```
@Lain ~$ uptime
 13:59:31 up  1:43,  1 user,  load average: 1.52, 0.72, 0.59
```

**Load average:** Shows the average load for the past 1, 5, and 15 minutes.

> System load averages is the average number of processes that are either in a runnable or uninterruptable state.  A process in a runnable state is either using the CPU  or
> waiting  to  use  the CPU.  A process in uninterruptable state is waiting for some I/O access, eg waiting for disk.  The averages are taken over the three time intervals.
> Load averages are not normalized for the number of CPUs in a system, so a load average of 1 means a single CPU system is loaded all the time while on a 4  CPU  system  it
> means it was idle 75% of the time.

For example for the following output:
```
23:51:26 up 21:31, 1 user, load average: 30.02, 26.43, 19.02
```
Since the 1 minute mark is much higher then 5 or 15 minute mark this would indicate a CPU heavy task that was is currently running/just executed.  
``vmstat`` or ``mpstat`` can confirm this observation.

## dmesg | tail
This command lists the last 10 kernel log messages.  
It's always worth to check the last kernel messages and watch out for error logs.

## vmstat 1
Short for virtual memory stat, ``vmstat(8)`` is a commonly available tool. It prints a summary for key server statistics on each line.  

``vmstat`` was run with an argument of 1, to print one second summaries. The first line of output has some columns that show the average since boot, instead of the previous second 

### Columns to check
**r**:  
Number of processes running on CPU and waiting for a turn. This provides a better signal than load averages for determining CPU saturation, as it does not include I/O. To interpret: an "r" value greater than the CPU count is saturation.

**free**:  
Free memory in kilobytes. If there are too many digits to count, you have enough free memory. The ``free -m`` command, included as command 7, better explains the state of free memory.

**si, so**:  
Swap-ins and swap-outs. If these are non-zero, you're out of memory.

**us, sy, id, wa, st**:  
These are breakdowns of CPU time, on average across all CPUs, They are user time, system time(kernel), idle, wait I/O, and stolen time ( by other guests).

The CPU time breakdowns will confirm if the CPUs are busy, by adding user + system time. A constant degree of wait I/O points to a disk bottleneck; this is where the CPUs are idle, because tasks are blocked waiting for pending disk I/O.

System time is necessary for I/O processing. A high system time average, over 20%, can be interesting to explore further: perhaps the kernel is processing the I/O inefficiently


## mpstat -P ALL 1
This command prints CPU time breakdowns per CPU, which can be used to check for an imbalance. a single hot CPU can be evidence of a single-threaded application

## pidstat 1
Pidstat is a little like top's per-process summary, but prints a rolling summary instead of clearing the screen. This can be useful for watching patterns over time, and also recording what you saw into a record of your investigation.


## iostat -xz
Tool for understanding block devices (disks), both the workload applied and the resulting performance.

**r/s, w/s, rkB/s, wkB/s**:  
These are the delivered reads, writes, read Kbytes, and write Kbytes per second to the device. Use these for workload characterization. A performance problem may simply be due to an excessive load applied.

**await**:  
The average time for the I/O in milliseconds. This is the time that the application suffers, as it includes both time queued and time being services. Target than expected average times can be an indicator of device saturation, or device problems.

**avgqu-sz**:  
The average number of requests issued to the device. Values greater than 1 can be evidence of saturation (although devices can typically operate on requests in parallel, especially virtual devices which front multiple back-end disks.)

**%util**:  
Device utilization. This is really a busy percent, showing the time each second that the device was doing work. Values greater than 60^ typically lead to poor performance (which should be seen in await), although it depends on the device. Values close to 100% usually indicate saturation.

## free -m
**buffers**:  
For the buffer cache, used for block device I/O.
**cached**:  
For the page cache, used by file systems.


Only important that those aren't near-zero in size, which can lead to higher disk I/O (confirm using iostat) and worse performance)

## sar -n DEV 1
Use this tool to check network interface throughput: rxkB/s and txkB/s, as a measure of workload, and also to check if any limit has been reached.

## sar -n TCP,ETCP 1
This is a summarized view of some key TCP metrics. These include:

* active/s: Number of locally-initiated TCP connections per second (e.g., via connect()).
* passive/s: Number of remotely-initiated TCP connections per second (e.g., via accept()).
* retrans/s: Number of TCP retransmits per second.

Retransmits are a sign of a network or server issue; it may be an unreliable network (e.g., the public Internet), or it may be due a server being overloaded and dropping packets. The example above shows just one new TCP connection per-second.

## top
A downside to top is that it is harder to see patterns over time, which may be more clear in tools like vmstat and pidstat, which provide rolling output. Evidence of intermittent issues can also be lost if you don’t pause the output quick enough (Ctrl-S to pause, Ctrl-Q to continue), and the screen clears.



