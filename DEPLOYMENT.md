# Deploying to Cloudflare Pages

This site is built with Hugo and can be deployed to Cloudflare Pages.

## Cloudflare Pages Setup

1. Go to [Cloudflare Pages](https://pages.cloudflare.com/)
2. Click "Create a project"
3. Connect your Git repository
4. Configure the build settings:
   - **Build command**: `hugo`
   - **Build output directory**: `public`
   - **Environment variables** (optional):
     - `HUGO_VERSION`: `0.153.4` (or your preferred version)

Cloudflare Pages will automatically detect that this is a Hugo site and apply the correct settings.

## Local Development

To run the site locally:

```bash
hugo server
```

Then visit http://localhost:1313

## Building Locally

To build the site:

```bash
hugo
```

The generated site will be in the `public/` directory.
