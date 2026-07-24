# Setting up Traefik

Following the documentation for setting up traefik with TLS Certificate using Let's Encrypt and a duckdns domain.
The traefik configuration is split between the compose file and a traefik.yml file.

## Certificate

Uses a self-signed certificate located in `/certs` created using openssl.

```bash
mkdir -p certs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout certs/local.key -out certs/local.crt \
  -subj "/CN=*.docker.localhost"
```
For dynamic configuration it uses the TLS certificate configured on a tls.yml file.

Created a certificate resolver called `le` that'll use a dnsChallenge on my duckdns domain to get a certificate.

## Credentials

Uses basic auth middleware to generate a hashed username/password pair.

```bash
htpasswd -nb admin "P@ssw0rd" | sed -e 's/\$/\$\$/g'
```

For more information go to https://doc.traefik.io/traefik/setup/docker/
