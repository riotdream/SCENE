systemd can start glftpd.

Let's define an instantiated service and a socket unit that starts it.
If you want to have its name changed make sure you keep the @.service part.

1. /etc/systemd/system/glftpd@.service

  [Unit]
  Description=glftpd per connection server
  
  [Service]
  ExecStart=-/glftpd/bin/glftpd glftpd -l -o -i -r /etc/glftpd.conf -e
  StandardInput=socket
  SyslogFacility=ftp

2. /etc/systemd/system/glftpd.socket

  [Unit]
  Description=glftpd socket
  
  [Socket]
  ReusePort=true
  ListenStream=[::]:1337
  Accept=yes
  
  [Install]
  WantedBy=sockets.target

3. Tell systemd to read its files again

  $> systemctl daemon-reload

4. Test what systemd has to say about your socket

  $> systemctl status glftpd.socket

  > glftpd.socket - glftpd socket
  >    Loaded: loaded (/etc/systemd/system/glftpd.socket; disabled)
  >    Active: inactive (dead)
  >  Accepted: 0; Connected: 0


5. Let's start and enable it on boot

  $> systemctl enable glftpd.socket

  > Created symlink from /etc/systemd/system/sockets.target.wants/glftpd.socket

  $> systemctl start  glftpd.socket

  $> systemctl status glftpd.socket

  > glftpd.socket - glftpd socket
  >    Loaded: loaded (/etc/systemd/system/glftpd.socket; enabled)
  >    Active: active (listening)
  >  Accepted: 0; Connected: 0

6. That's it.
   You can get more details here http://0pointer.de/blog/projects/inetd.html
