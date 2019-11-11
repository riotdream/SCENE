FiSH-weechat

FiSH-weechat in python using original FiSH blowfish cipher instead of pycrypto

The FiSH 'standard' requires that the blowfish cipher support keys up to 72 bytes long. The pycrypto implementation no longer supports this.

This mod of the FiSH-weechat script has the pycrypto blowfish cipher replaced with FiSH compatible code used in many other FiSH implementations.

Install:

Run 'make' then if you store your weechat scripts in the standard location ~/.weechat/python, just run 'make install'.