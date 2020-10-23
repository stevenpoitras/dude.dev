---
layout: post
title: "Advanced Nutanix – SQL Best Practices"
tags: mssql nutanix
---

# Best Practices Checklist

## General
* Perform a current state analysis to identify workloads and sizing
* Spend time up front to architect a solution that meets both current and future needs
* Design to deliver consistent performance, reliability, and scale
* Don’t undersize, don’t oversize, right size
* Start with a PoC, test, optimize, iterate, scale

## Core Components

### Performance and Scalability

* Utilize multiple drives for TempDB Log/Data and Database Log/Data
    * Start with a minimum of 2 for small environments or 4 for larger environments
    * Look for PAGEIOLATCH_XX contention and scale number of drives as necessary
* Utilize a 64KB NTFS allocation unit size for MSSQL drives
* Enabled locked pages in memory for MSSQL Server service account (NOTE: if this setting is used the VM’s memory must be locked, only applies with memory > 8GB)
* TempDB Data Files
    * Set TempDB size between 1 and 10% of instance database sizes
    * If number of cores < 8
        * # of cores = # of data files
    * If number of cores > 8
        * Use 8 data files to begin with
        * Look for contention for in-memory allocation (PAGELATCH_XX) and scale 4 files at a time until contention is eliminated
* Database Data files
    * Size appropriately and enable AUTOGROW respective to database growth
    * Do not AUTOSHRINK data and log files
    * At a maximum keep below 80% of disk capacity utilization
    * Use multiple data file and drives
        * Look for contention  for in-memory allocation (PAGELATCH_XX), if contention increase number of files
        * Look for I/O subsystem contention (PAGEIOLATCH_XX), if contention, spread the data files across multiple drives
* Trace flags
    * Implement trace flag 1118 at startup to remove single page allocations
    * Implement trace flag 834 to enable large pages (for tier-1 performance)
* Utilize the MSSQL Server Best Practices Analyzer (BPA) to identify potential issues
* Utilize fast file initialization
* Scale number of MSSQL VMs vs. number of MSSQL instances per VM
* More memory = higher performance, if seeing memory pressures, increase VM memory
* Utilize a dedicated disk for Microsoft Page File

### Availability
* Standardize, monitor and maintain
* Leverage a MSSQL application monitoring solution (eg. System Center, etc.)
* Create standardized MSSQL VM Templates
* Utilize consistent disk quantities and layout schemes for MSSQL VMs
* Join the MSSQL Server to the domain and use Active Directory for authentication
* Leverage Contained Database Authentication (MSSQL 2012)
* Use named instances for MSSQL database instances, even when only planning a single instance per VM
* For named instances,  ensure application compatibility with dynamic ports, otherwise set instance to use a fixed port
