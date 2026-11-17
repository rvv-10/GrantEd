# Infrastructure

Deployment files for one EC2 instance running Docker Compose. **Filled in Week 4.**

```
infrastructure/
├── caddy/      # Caddyfile: HTTPS (Let's Encrypt), reverse proxy to the API, static frontend, basic auth on /admin
└── scripts/    # backup.sh (nightly pg_dump → S3), restore.sh (tested restore)
```

`docker-compose.yml` lives at the repository root so `docker compose up` works from there.

Terraform is **not** here in v1.0 (ADR 0003). It arrives in v1.1 as `infrastructure/terraform/`, once the manual deployment in `docs/deployment.md` has been run successfully.
