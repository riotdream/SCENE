How to get the most out of your glFTPd IPv6 experience.
   last updated 20161206


1. How to have your glFTPd server accept IPv6 connections (and IPv4 as well).

   The easiest is running the installgl.sh script which will take care of this.

   To do it manually: depending on the inetd you use, a small change has to be
   made to the glFTPd inetd configuration:
   - openbsd-inetd: duplicate your existing config-line and change "tcp" to "tcp6" on the duplicated line and "tcp" to "tcp4" on the original line.
                    In some cases the original line with "tcp4" is not needed.
   - inetutils-inetd: same as openbsd-inetd, but no need to duplicate.
                      There are some cases known though where duplication is necessary.
   - xinetd: add "IPv6" to the "flags" setting of your glftpd service config.
   - rlinetd: add "family ipv6;" to your glftpd service config.
   - systemd: change "0.0.0.0" to "[::]" of the "ListenStream" config line in your glftpd.socket file.
   - launchd: (untested) change "IPv4" to "IPv6" of the "SockFamily" dict key of your glftpd plist file.
   Dual stack functionality is maintained at all times (if supported by the kernel).


2. How to use IPv6 addresses and masks in your config and userfiles.

   No real difference with the IPv4 addresses and masks, only one rule to keep in mind:
   an asterisk (*) in an IPv6 mask matches as much as it can, unless if there's
   a double colon (::) present, then it only matches the available 4-bits before,
   after or between adjacent colons.
   An example: ffff:* will match ffff:1234::1
               ffff:abc* will match ffff:abcd::1
               ffff::* will be used as ffff:0000:0000:0000:0000:0000:0000:*
               ffff::98*6 will be used as ffff:0000:0000:0000:0000:0000:0000:98[0-f]6


3. How to connect to your glFTPd server using an IPv6 address.

   First of all you need an FTP client capable of connecting to IPv6 addresses.
   Then this FTP client has to support the EPRT and EPSV commands introduced by RFC 2428.

   Second you need your IPv6 address added to your account of course, unless you have a fancy *@*.


4. How to have FTP clients actually use some of your glftpd IP related settings.

   Sadly some glFTPd server configurations will not work as wanted, such as:
   - pasv_addr lines with IPv6 addresses not matching the connected-to address
   - connected-to addresses not matching any of the available addresses on the server, such as with entry bouncers
   - FXP to a different protocol family than the connected-to protocol, i.e. you are connected to the IPv4 address of your server and want to FXP a file to an IPv6-only second server.

   This problem was already brought forward by the now lost and abandoned glFTPd-NG.
   We follow the same solution with a small alteration.

   The problem is the EPSV reply defined in the aforementioned RFC:
     229 <reply text> (<d><d><d><tcp-port><d>)
   with <d> a delimiter in the range of ASCII 33-126 (the same for each occurence)
   and <tcp-port> the port to make a connection to.
   As can be seen, no IP address (in whatever form) is given (and no protocol
   family either, but this is less of a nuissance).
   The idea behind this is to just use the address that you are connected to,
   but of course sometimes you just don't want that.

   The solution is to provide these informations the same way the FTP client provides these with the EPRT command. So our EPSV reply would look like:
     229 <reply text> (<d><net-prot><d><net-addr><d><tcp-port><d>)
   with <d> and <tcp-port> the same as above
   <net-prot> the protocol family as defined by IANA (1 for IPv4, 2 for IPv6)
   and <net-addr> the IP address the FTP client should use to connect to.

   And now for our small alteration: this behaviour is not by default, in contrast with glFTPd-NG.
   As this is not the standard, it breaks many FTP clients and is only wanted in perhaps a small subset of glFTPd installations.
   Thus a new command was brought to life: CEPR or Custom Extended Passive Reply.
   It works the same as the SSCN command and is also listed in the FEAT reply.
   To enable the IP address in the EPSV reply you first issue "CEPR on", to disable again it's "CEPR off".

   For this to work flawlessly, the FTP clients have to implement issuing these
   commands and reacting correctly to the given reply. So contact your
   FTP client dev and let them read this document.
