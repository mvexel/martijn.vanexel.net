# Deployment

This site is deployed on `lima` (Komodo-managed) at
`https://martijn.vanexel.net`. The Komodo Stack declaration (server,
sync config) lives in the `schaaltreinenhuis-infra` repo's
`komodo/resources.toml`, not here — see that repo's `komodo/README.md`
for the reasoning. This repo only owns what's coupled to the site
itself: the Hugo source, `Dockerfile` (builds via `hugomods/hugo:std`,
serves the static output through a minimal Caddy container), and
`compose.production.yml`.

Pushing to `main` and syncing the infra repo's Resource Sync redeploys
the site.

## Local Development

```bash
hugo server
```

Then visit http://localhost:1313

## Building Locally

```bash
hugo
```

The generated site will be in the `public/` directory.

## Building the production image locally

```bash
docker build -t martijn-vanexel-net .
docker run --rm -p 8080:80 martijn-vanexel-net
```
