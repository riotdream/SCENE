Here is the entry for glftpd to work with xinetd:

service glftpd
{
	disable	= no
	flags			= REUSE IPv6 NAMEINARGS
	socket_type		= stream
	protocol		= tcp
	wait			= no
	user			= root
	server			= /usr/sbin/tcpd
	server_args		= /glftpd/bin/glftpd -l -o -i
	#if you do not want to add anything to /etc/services you can use
	#type = UNLISTED
	#port = 1337
}


You can either add this to /etc/xinetd.conf or put it in a separate file
(called glftpd) and copy that file to the dir where your other xinetd entries
are (/etc/xinetd.d on redhat). Then you need to restart xinetd (on redhat:
/etc/rc.d/init.d/xinetd restart or killall -USR2 xinetd)


If you dont want to use tcpd, this is the correct entry:

service glftpd
{       
        disable = no
        flags                   = REUSE IPv6
        socket_type             = stream
        protocol                = tcp
        wait                    = no
        user                    = root
        server                  = /glftpd/bin/glftpd
        server_args             = glftpd -l -o -i
	#if you do not want to add anything to /etc/services you can use
	#type = UNLISTED
	#port = 1337
}
