# Nextcloud Configuration

## Step 1: Login to Nextcloud as Admin

Open your browser and go to your Nextcloud instance:
```https://yourdomain.ngrok-free.app```
- if You get This message:
```
Access via untrusted domain. Please contact your administrator...
```

### Here is the Fix:

Edit `config/config.php` inside Nextcloud snap directory:

```bash
sudo nano /var/snap/nextcloud/current/nextcloud/config/config.php
```

Find `'trusted_domains'` array and add your domain or IP:

```php
'trusted_domains' => [
  0 => 'localhost',
  1 => '192.168.1.100',
  2 => 'your.domain.com',
  3 => 'your_ngrok_static_domain.com'
],
```

Save and exit. Restart Nextcloud if needed.

---

## Step 2: Login to Nextcloud as Admin and Set New Password
---

## Step 3: Setup 2FA

- Click on your profile and open [+ Apps]
- On the left go to Security and Search for ```Two-Factor TOTP Provider```
- Click Enable.

Enforcement Rules (Set who needs to 2FA):
- Go to Settings → Security (admin panel).
- Scroll to Two-Factor Authentication.
- Set the enforcement rules (e.g. "Enforce for all users" or by group).

Now to setup an Authenticator: 
- Go to Personal Settings → Security
- Click on the TOTP Checkbox
- Scan or Enter the Code in Your Authenticator App

## Notes
- You should also Generate Backup codes and keep them Somewhere Secure.
