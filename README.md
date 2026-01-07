# My Homelab

This lab is a place where I can practice Linux and infrastructure design and operations deploying real applications.

## Hardware

This lab is running on a Sony Vaio notebook with a Intel Core i3-2350M CPU and 8GiB of memory, is what I have available in the moment but in the future I plan to expand it to 2 or 3 small machines, probably a combination of notebooks and mini pcs, so I can explore clusters and a more production like environment with High Availability, staging/prod setups and kubernetes.

## Tooling
- Ubuntu Server Ubuntu 24.04.3 LTS as the OS
- Docker and Docker Compose for deploying apps
- Git for local development ( The server only deploys what it pulls from the repository )

## Future goals

- Migrate from using docker to k3s
- Add more hardware
- Automate OS configuration and Infra deployment

## TODOs

- [ ] Deploy apps
  - [x] Linkding
  - [ ] Ebook library management
  - [ ] Arr Stack
  - [ ] ...
- [ ] Expose apps with a reverse proxy ( Ngnix or Traefik maybe)
- [ ] Set up monitoring

## Repo Structure

Following a monorepo approach, inspired by flux's example structure, with this git repo being the only source of truth, with isolated apps.

```bash
├── apps
│   └── linkding
│       ├── docker-compose.yml
│       ├── .env.example
│       └── README.md
├── infra
│   └── reverse-proxy
└── README.md
```
