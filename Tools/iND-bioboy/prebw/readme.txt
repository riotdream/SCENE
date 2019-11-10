glFTPd PreBW by biohazard

Installation steps:

1.    Edit GLFTPD_PATH at top of Makefile
2.    Edit settings in config.hpp
3.    Run 'make' to build prebw utility
4.    Run 'make install' as root to copy binaries to glftpd's bin directory
5.    Copy prebw.tcl to (NGBOT-PATH)/plugins directory
6.    Add source (NGBOT-PATH)/plugins/prebw.tcl to bottom of eggdrop conf
7.    Edit ngBot.conf, ngBot.vars and your ngBot theme accordingly. See editing ngBot theme.
8.    Rehash eggdrop

Editing ngBot theme:

1.    Add PREBW to your existing 'set msgtypes(SECTION)' line in ngBot.conf. If it doesn't exist, copy it from ngBot.conf.defaults and edit it.

2.    Add PREBW variables to ngBot.vars. The number of %u1 (num users) %b1 (bandwidth) pairs depends on how many snapshots set in config.hpp. An example of 7 snapshots:

      set variables(PREBW) "%pf %u1 %b1 %u2 %b2 %u3 %b3 %u4 %b4 %u5 %b5 %u6 %b6 %u7 %b7 %numusers %numgroups %traffic"

3.    Update your theme file, using the variables defined in ngBot.vars. An example of 7 snapshots:

      announce.PREBW = "[%b{prebw}] %b{%reldir} - 1s %b{%u1}@%b{%b1}MB/s 5s %b{%u2}@%b{%b2}MB/s 10s %b{%u3}@%b{%b3}MB/s 15s %b{%u4}@%b{%b4}MB/s 20s %b{%u5}@%b{%b5}MB/s 25s %b{%u6}@%b{%b6}MB/s 30s %b{%u7}@%b{%b7}MB/s - %b{%traffic}MB by %b{%numusers}u/%b{%numgroups}g"
