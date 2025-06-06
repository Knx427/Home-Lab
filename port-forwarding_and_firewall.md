# Router Port Forwarding & Firewall Setup

## Port Forwarding Setup

- Forward external port (e.g., 2222) to internal SSH port (e.g., 5555) on server IP.    
 *(Check ssh port at /etc/ssh/sshd_config)*
- Use TCP protocol.
- Example:

| External Port | Internal IP    | Internal Port | Protocol |
|--------------|----------------|---------------|----------|
| 2222         | 192.168.1.100  | 5555          | TCP      |

---

## UFW Firewall

Allow SSH and Nextcloud port on server:

```bash
sudo ufw allow 5555/tcp # Use port you configured in /etc/ssh/sshd_config
sudo ufw allow 80 # Use 80 for HTTP or 443 for HTTPS
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
```
---

- CGNAT (ISP NAT) blocks direct port forwarding in some cases, Check if your router's settings if WAN IP is private (e.g. 10.x.x.x, 100.64.x.x)
- Use ngrok or VPN to bypass CGNAT [ngrok Setup](ngrok-setup.md).
