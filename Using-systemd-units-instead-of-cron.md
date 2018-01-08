**1. Create a systemd unit for acme.sh:**

`/etc/systemd/system/acme_letsencrypt.service`
```
[Unit]
Description=Renew Let's Encrypt certificates using acme.sh
After=network-online.target

[Service]
Type=oneshot
# Directory where the acme.sh script resides.
Environment="HOME=/path/to/"
ExecStart=/path/to/acme.sh <your acme.sh arguments here>
# acme.sh returns 2 when renewal is skipped (i.e. certs up to date)
SuccessExitStatus=0 2
```

**2. Test that it works before creating the timer:**
```
sudo systemctl daemon-reload
sudo systemctl start acme_letsencrypt --now
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
`sudo systemctl enable acme_letsencrypt.timer`