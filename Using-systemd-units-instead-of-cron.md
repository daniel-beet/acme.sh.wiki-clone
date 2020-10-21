**1. Create a systemd unit for acme.sh:**

`/etc/systemd/system/acme_letsencrypt.service`
```
[Unit]
Description=Renew Let's Encrypt certificates using acme.sh
After=network-online.target

[Service]
Type=oneshot
# --home's argument should be where the acme.sh script resides.
ExecStart=/path/to/acme.sh --home /path/to/acme.sh

SuccessExitStatus=0 2
```

**2. Test that it works before creating the timer:**
```
sudo systemctl daemon-reload
sudo systemctl start acme_letsencrypt
```

**3. Create systemd timer unit for the service above:**

`/etc/systemd/system/acme_letsencrypt.timer`
```
[Unit]
Description=Daily renewal of Let's Encrypt's certificates

[Timer]
OnCalendar=daily
RandomizedDelaySec=1h
Persistent=true

[Install]
WantedBy=timers.target
```

**4. Enable timer:**
```
sudo systemctl start acme_letsencrypt.timer
sudo systemctl enable acme_letsencrypt.timer
```