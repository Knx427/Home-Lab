# SSH Setup and Security

### Disable Password Authentication and use custom port

Edit `/etc/ssh/sshd_config`:

```
PasswordAuthentication no
PermitRootLogin no
port 2222[Whatever you want just dont use commonly used ones]
```

check SSH:

```bash
sudo systemctl enable ssh
sudo systemctl start/restart ssh
sudo systemctl status ssh
```
## Fail2Ban

Protect SSH from brute force:

```bash
sudo apt install fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```
You’ll edit the .local file instead of the default so updates don’t overwrite your changes
```bash
[sshd]
enabled = true
port    = 2222      # your custom SSH port or 22 for standard
logpath = %(sshd_log)s
backend = systemd
```
### Check Fail2Ban
```
sudo systemctl enable fail2ban
sudo systemctl start/restart fail2ban
```
to see specific ssh jail details:
```
sudo fail2ban-client status sshd
```
### Optional: Adjust Ban Settings
In the same jail.local file, under [DEFAULT], you can tweak:
```
bantime = 3600         how long to ban (seconds) – default: 10 mins   
findtime = 600         time window to count failures   
maxretry = 5           number of tries before ban
```
---

## Notes

- [Setup Port Forwarding](port-forwarding.md)
- CGNAT (ISP NAT) blocks direct port forwarding in some cases.  
- Use ngrok or VPN to bypass CGNAT see [ngrok.md](ngrok.md).
- Use key-based authentication only for security.  
- Protect your private key with a passphrase.

