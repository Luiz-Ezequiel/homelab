# Linkding

This is a simple Docker Compose file that defines how my linkding application is deployed.

## Why

Simple and fast bookmark application, with a community Android app.

## Configuration

- Default Linkding parameters deployed on Docker Compose and a simple healthcheck test using curl on the port.
    Used default configuration for it being a simple application that I can **focus on deploying and improving the infra around it**.
- Using SQLite with Docker Volumes due to the low storage requirements of the application.
    Using Docker Volumes so the data in the SQLite is persistent and its easier to manage than bind mounts, but maybe I'll configure an external DB instead of the SQLite in the future.
- Currently being exposed to the LAN by port-forwarding.
- Using .env files for secrets.

### Next Steps

- [x] Add Healthcheck
- [ ] Access via reverse proxy
- [ ] Add simple script for backup
- [ ] Setup monitoring
