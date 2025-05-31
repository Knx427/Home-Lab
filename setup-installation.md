Download the Linux ISO of your choice (Ubuntu Server or Debian are good starts).
https://ubuntu.com/download/server

[Bootable Device]
Boot your laptop from USB and start the install.
Create a bootable USB using tools like Rufus.

[Language]
Choose Language and Keyboard Layout

[Network]
During install, set a Dynamic (DHCP) or static IP. You should configure it to static afterward.

[Storage]
For Storage use LVM — to grow and adapt your server without reinstalling everything later.
Encrypt LVM Group with LUKS - If You're storing anything personal or private.
Create a recovery key if you encrypt your disk.
after installation get the key with: gpg -c /root/luks-recovery.key and store it somewhere accessible but encrypted
You can have up to 8 different keys. 
create another one with: sudo dd if=/dev/[disk] of=/root/luks-recovery.key bs=1 count=64
and add it to your disk with: sudo cryptsetup luksAddKey /dev/[disk] /root/luks-recovery.key
then delete the key from the disk(security reasons): shred -u /root/luks-recovery.key
You can find your disk name with: lsblk

[Create user && set servername]
Set Your server username and servername

[Ubuntu pro]
Choose to use or not

[Install SSH and set ssh_key]
Select import key from github it will automaticaly fetch your public sshkey.
If you dont have an key generate one in your CLI with: ssh-keygen -t ed25519 -C "Text or name to identify usage"
Save them a location of your liking, You'll get two files: .ssh/id_ed25519 [private key] and .ssh/id_ed25519.pub [public key]
Go to your github profile and add a new ssh key -> set key type: authentication -> paste the public key 
store and keep secret your private key at local device (device/s you'll be connectiong to the server with)

[Server Saps]
Install server software (SAPS) as needed:
- Nextcloud for private cloud storage.
- SSH for remote management.

[Finish installation]
When installation Finishes reboot and when prompted remove bootable usb. (if you're stuck at black screen reboot manually)
