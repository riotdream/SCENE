This product includes cryptographic software written by Eric Young
(eay@cryptsoft.com)

this readme is based on readme from ftpd-tls project, check the links for
ftpd-tls and ftp-tls in links section (ftp.runestig.com)

------------------------------------------------------------------------
first of all, what is ftp TLS :
------------------------------------------------------------------------
 ftp TLS is a secure extension for normal ftp standard which lets us
 use SSLv3 encryption with ftp connections, it is described in included
 IETF draft file (draft-murray-auth-ftp-ssl-xx.txt), please read it
 for a lot of important informations on how does it work

------------------------------------------------------------------------
now, how to install glftpd-TLS :
------------------------------------------------------------------------
 first install glftpd using the steps in glftpd.docs, now create yourself
 a key with the included script file (/glftpd/create_server_key.sh),
 for example "/glftpd/create_server_key.sh FOOBAR" will make DSA key (default)
 with filename ftpd-dsa.pem and comment test "FOOBAR"
 
 when done, put it to some safe place (outside /glftpd chroot)
 and tell glftpd where to get it (add DSA_CERT_FILE /glftpd/ftpd-dsa.pem line to
 glftpd.conf) and you are done with basic install, now verify all works ok
 if you have any problems check error.log, if you used RSA key use RSA_CERT_FILE...

new glftpd.conf settings :
 with these you can deny TLS access to some people
 check the examples

 NOTE : you cannot use =GROUP or FLAGS in these 2 lines !!!! userfile isnt
 loaded yet you can only use -username and *

EXAMPLE :
#not allow lamer to use TLS mode
userrejectsecure -lamer !* 
#allow only hoe to use insecure access
userrejectinsecure !-hoe *

v1.29 new settings:
you can now deny unecrypted access to dirlist using denydiruncrypted
and to other transfers using denydatauncrypted
so :

denydiruncrypted !-idiot =IDIOTS *
denydatauncrypted !-idiot =IDIOTS *

only user idiot and group IDIOTS can get unencrypted dirlist or file
transfer


basically to create 100% secure site you will want to use this setting :
userrejectsecure !* 
userrejectinsecure *
denydiruncrypted *
denydatauncrypted *

to create site where people can using both noTLS and TLS connections use:
userrejectsecure !* 
userrejectinsecure !*
denydiruncrypted !*
denydatauncrypted !*

------------------------------------------------------------------------
2.01RC5 NEW FEATURES :
with 2.01rc5 there are more configuration options in glftpd.conf and old commandline -z switches
are removed

specify one of :
RSA_CERT_FILE /glftpd/ftpd-rsa.pem
DSA_CERT_FILE /glftpd/ftpd-dsa.pem
to configure path to certificate

cipher settings (with default settings if the setting is not in glftpd.conf) :
CIPHERS_FOR_CTRL HIGH:MEDIUM:LOW (ciphers for control channel connection, HIGH is highly recommended)
CIPHERS_FOR_DIR MEDIUM:HIGH:LOW (ciphers for dirlists)
CIPHERS_FOR_DATA MEDIUM:HIGH:LOW (ciphers for upload/download)

these lines specify prefered cipher order for openssl, if you for example want to 
speed up data transfers you can put LOW first for CIPHERS_FOR_DATA,
or you want to be very paranoid put HIGH first for CIPHERS_FOR_DATA

for more info about ciphers check openssl documentation

ftps switch :
TLS_FTPS [0/1] (0 is default)
    if 1 glftpd will run in ftps mode, whole connection from the beginning
    will be in ssl mode... (except for connections from bouncers, those must
    supply IDNT command first) (note that data connection is set the ssl mode
    too, use PROT command to switch back if you want) (check ftp-tls draft for more info)
    (for normal ftp server you dont want this)    
------------------------------------------------------------------------


------------------------------------------------------------------------
few notes :
------------------------------------------------------------------------
 please note that using a bouncer like bnc4all or dike will not work
 with TLS mode, this is because these bouncers parse the ftp commands
 and this isnt possible in TLS mode, because the bouncer doesnt know
 how to decrypt the data...
 for port bouncing you can use hpbnc (http://pftp.suxx.sk) and
 for data bouncing you can use hbnc (http://pftp.suxx.sk) however
 hbnc is in very early state of development at this moment (december 2000)
 and doesnt have much features yet and hpbnc doesnt support ftps mode

 it is maybe possible to use hbnc as a decrypt bouncer and put it before
 dike bouncers and create a chain when hbnc will decrypt the ssl connection
 to dike bouncer and that will just work as usually... however i didnt try
 it.. maybe i will try and document it in next hbnc release so check it out
 if you are interested...
 
------------------------------------------------------------------------
advanced features :
------------------------------------------------------------------------
 check the rest of this file for some more parameters and informations,
 basically you may be interested in adding "-z certsok" to inetd, this
 will let people with expired keys (if they use a key) to not be rejected,
 or you can use "-z ftps" for ftps mode (lftp supports it with open ftps://")
 
links :
 clients that support TLS : pftp (http://pftp.suxx.sk)
                            lftp (http://ftp.yars.free.net/projects/lftp/)
                            ftp-tls (ftp://ftp.runestig.com/pub/ftp-tls/)
			    jfxp (http://jfxp.com)
                      NEW : now also windows client WS-FTP and CUTE FTP
                            PRO are supported
                            (www.globalscape.com and www.wsftp.com)
    and many others (flashfxp 2.0 included :))
    
 servers : ftpd-tls (ftp://ftp.runestig.com/pub/ftpd-tls)
           glftpd-TLS (http://pftp.suxx.sk)
           or any normal server with hbnc (http://pftp.suxx.sk)
	   ...

