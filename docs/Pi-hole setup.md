Allow requests to these ports so that Pi-hole can work as intended.

```bash
sudo ufw allow 53/tcp
sudo ufw allow 53/udp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
# ufw allow 67/tcp for DHCP
# ufw allow 67/udp for DHCP
# ufw allow 123/udp for NTP
```