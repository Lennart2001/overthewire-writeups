# Explosion

### Contents
- **[Overview](Overview)**
- **[Nmap](Nmap)**


## Overview

------------
We are currently at the starting point of HackTheBox, and are trying to capture the flag of Machine 'Explosion'.
We are accessing the proper environement, via the HackTheBox Virtual Private Network, which allows us to access their
Machines.
The IP Address of the machine is: ``


## Nmap

------------
We first check out the open TCP ports with Nmap: ``nmap -p- -sC -sV``

- `-p` allows for specifying ports or port ranges
  - `-p-` tells nmap to scan ALL ports
- `-sC` runs a bunch of default scripts - some of which are considered "intrusive".
  - `-sC` is equivalent to `--script=default`
  - `--script=<lua scripts>` are able to be inserted here
- `-sV` runs probes on open ports to determine service/version info
  - `--version-intensity <level>` goes from 0-9, where 0 is the least amount of probes and 9 is ALL probes



