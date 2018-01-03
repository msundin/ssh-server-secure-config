# ssh-server-secure-config
## The goal is to create a highly secure ssh server configuration.

### Source and inspiration: https://stribika.github.io/2015/01/04/secure-secure-shell.html

1. Enable sshd by adding the following to /etc/rc.conf
```
shd_enable="YES"
```
2. Replace /etc/ssh/sshd_config with the included file `sshd_config`, and change `port` to something non-standard (and not 22)
3. Replace /etc/ssh/ssh_config with the included file `ssh_config`
4. Recreate all ssh host keys without passwords (/etc/ssh/ssh_host_*key*)
```
rm /etc/ssh/ssh_host_*key*
ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key -N "" < /dev/null
ssh-keygen -t rsa -b 4096 -f /etc/ssh/ssh_host_rsa_key -N ""  < /dev/null
```
5. Recreate /etc/ssh/moduli
```
rm /etc/ssh/moduli
ssh-keygen -G /etc/ssh/moduli.all -b 4096 # takes a few minutes
ssh-keygen -T /etc/ssh/moduli.safe -f /etc/ssh/moduli.all # takes hour/hours
mv /etc/ssh/moduli.safe /etc/ssh/moduli
rm /etc/ssh/moduli.all
```
6. Restart the sshd deamon
```
service sshd restart
```
7. Generate client keys **with strong passwords** using the following commands (or generate them on the client side):
```
ssh-keygen -t ed25519 -o -a 100
ssh-keygen -t rsa -b 4096 -o -a 100
```
9. Add the public keys to your user's (not root) `~/.ssh/authorized_keys` file (or if created on the client by using the `ssh-copy-id` utility (Google is your friend(?)). Then `chmod 600 ~/.ssh/authorized_keys`.
8. Preferly add the private keys as an attachment to a record in your [KeePass](https://www.keepas.info) and then use [KeeAgent](https://lechnology.com/software/keeagent/) when using the keys for SSH sessions.
9. If you for some reason copy a private key to Windows, generated in Linux/FreeBSD/etc terminal, mind the whitespaces and save the file in UTF-8 format if you want to use PuTTYgen to convert the keys to PuTTY format. Test the validity of the copied key by trying to import it into PuTTYget. If it fails, you have format errors or it is not saved in UTF-8 format.
10. Remove generated private client keys from the server (not the server keys) when you have stored them safely in PuTTY or elsewhere. This is for security reasons.
