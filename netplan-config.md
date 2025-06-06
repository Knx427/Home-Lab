# Netplan Static IP & WiFi Config

## Static IP Setup

### Routing metrics fallback method
Ethernet (eno1) is preferred because of the lower metric (100).   
If Ethernet goes down, routing automatically switches to Wi-Fi (wlan0) with higher metric (200).   
   
Use Netplan to configure a static IP on your Ubuntu server:
```sudo nano /etc/netplan/01-netcfg.yaml```

```yaml
network:
  version: 2
  renderer: networkd

  ethernets:
    eno1[YourEthAdapter]:
      dhcp4: no
      addresses: [192.168.1.100/24]
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
      routes:
        - to: 0.0.0.0/0
          via: 192.168.1.1
          metric: 100

  wifis:
    wlan0[YourWlanAdapter]:
      dhcp4: no
      addresses: [192.168.1.101/24]
      access-points:
        [YourSSID]:
          password: [YourPassword]
      routes:
        - to: 0.0.0.0/0
          via: 192.168.1.1
          metric: 200

```
You can see your adapters using the command ```ip a```.   
Apply changes:

```
sudo netplan apply
```

---

## Notes

- Indentation must be exact and aligned, or Errors could display.
- `gateway4` is preferred over deprecated `default routes` for the gateway.  
- Use `sudo netplan try` to test config safely.
