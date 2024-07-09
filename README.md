# Minty Blog

An over-engineered security blog!

[![Build Status](https://ci.mintydev.gay/api/badges/clovis/blog/status.svg)](https://ci.mintydev.gay/clovis/blog)

- **Blog:** [blog.mintydev.gay](https://blog.mintydev.gay/)
- **Main Gitea Repo:** [gitea.mintydev.gay/clovis/blog](https://gitea.mintydev.gay/clovis/blog)
- **Mirror Github Repo:** [github.com/BettridgeKameron/blog](https://github.com/BettridgeKameron/blog)

## About

Minty Blog is an overengineered security blog powered by Gitea and DroneCI. It is hosted on microk8s, utilizing a Continuous Integration and Continuous Deployment (CICD) pipeline with DroneCI. I have also implemented a Mail server with Mailu, which has working [webmail](https://roundcube.net/), and properly configured DMARC, DKIM, and SPF. It now also includes fully functional SSO using Keycloak! All hosted on a 4GB Server on Linode. I eventually plan on migrating to a new home server I got recently.

## Technologies Used

- **Source Control:** [Gitea](https://about.gitea.com/)
- **CICD:** [DroneCI](https://www.drone.io/)
- **Container Orchestration:** [microk8s](https://microk8s.io/)
- **Static Site Engine:** [Zola](https://www.getzola.org/)
- **Zola Theme:** [Terminimal](https://github.com/pawroman/zola-theme-terminimal)
- **Mail Server:** [Mailu](https://mailu.io)
- **SSO**: [Keycloak](https://www.keycloak.org)
