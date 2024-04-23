# Tools for Unix System Administrators

This is an uncomplete list of freeware Unix programs I have used for about ten years and that I continue to recommend with the same entusiasm I had when I installed them for the first time on Sun Solaris and SunOs machines. 

# Perl my preferred programming and scripting language

Perl is designed to make the easy jobs easy, without making the hard jobs impossible. Perl is a language for powerful text processing, filesystem manipulation, process management, database administration, client-server programming, secure programming, web-based information management and even for object-oriented programming; it was designed to be a glue language from the start. After learning it, i stopped to write small C programs, non trivial shell scripts, using awk and sed. Almost everything needed by a system administrator can be done very fast and with fun using Perl.

# Amanda the perfect network backup solution

It is a very good network backup solution: you have a backup server workstation with attached a tape, many client workstations (also PC’s, but in this case you need samba); you decide how many tape to use, the host and disk list to backup, how frequently to do a full backup of each disk and amanda does the rest. The software every night does an incremental backup of each disk in the network and a full backup of some disks to satisfy your policies. 

# Swatch the syslog watcher

Swatch is designed to monitor system activity. Swatch requires a configuration file which contains patterns to look for and actions to do when each pattern is found. I have redirected the syslogd output of every unix workstation to a single logging workstation, there swatch monitor the syslogd output and alert me, via e-mail, when events, like disk full etc., happens everywhere in the network.

# Sysinfo shows system information

Sysinfo is a program which shows various pieces of information about the hardware and operating system software configuration of the host it’s run on. Very useful: I have a file with its output for every workstation. When some workstation dies i can always call technical service and tell them exactly how it was configured: its processor, its disks, its RAM etc.
