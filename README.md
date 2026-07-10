# VoidHash Umbrel Community App Store

This repo is the missing piece referenced by `voidcoin-pool-umbrel`'s README
("Option 1: Community App Store"). Push this folder's contents as a new
GitHub repo named `umbrel-app-store` under the `VoidHash-Crypto` org, at:

    https://github.com/VoidHash-Crypto/umbrel-app-store

## Why this exists

`voidcoin-pool-umbrel`'s README tells users to add
`https://github.com/VoidHash-Crypto/umbrel-app-store` as a Community App
Store in Umbrel. That repo doesn't exist yet, so adding the URL fails and the
app never shows up to install — this is what's been breaking installs.

Scaffolded from Umbrel's official template
(https://github.com/getumbrel/umbrel-community-app-store).

## How to publish it

```bash
cd umbrel-app-store
git init
git add .
git commit -m "Initial community app store"
git remote add origin https://github.com/VoidHash-Crypto/umbrel-app-store.git
git push -u origin main
```

(Create the empty `umbrel-app-store` repo on GitHub first, or push and then
adjust the default branch name to match.)

Then in Umbrel: App Store → Community App Stores (⋮ menu) → add
`https://github.com/VoidHash-Crypto/umbrel-app-store` → find "Void Pool" →
Install.

## What was changed vs. the app's own docker-compose.yml

- Added an `app_proxy` service, required by every Umbrel app store listing —
  it's what makes Umbrel route its reverse proxy / login screen to the app.
- Removed the direct `3080:80` port publish on the `web` service (app_proxy
  handles that now). The `stratum` service still publishes port 3333
  directly since miners speak raw TCP, not HTTP, and need to bypass the
  proxy.
- Nothing else about the app (images, env vars, healthchecks) was touched —
  the CI-built images at `ghcr.io/voidhash-crypto/voidcoin-pool-*` already
  build successfully, so this is purely the missing distribution wrapper.

## Before you publish: test it

Umbrel provides a local dev environment (`umbrel-dev`) for exactly this. Full
steps: https://github.com/getumbrel/umbrel-apps#3-testing-the-app-on-umbrelos

I can't spin up an actual umbrelOS instance from here to verify the install
end-to-end, so please test via umbrel-dev (or a spare Umbrel device) before
telling other people to add the store URL.

## Faster alternative, right now

If you just want Void Pool running today without any of the above, use
Option 2 from the original README (standalone `docker compose`) — it doesn't
depend on any app store and the published images already work:

```bash
git clone https://github.com/VoidHash-Crypto/voidcoin-pool-umbrel.git
cd voidcoin-pool-umbrel
cp .env.example .env
sed -i "s/NODE_RPC_PASSWORD=.*/NODE_RPC_PASSWORD=$(openssl rand -hex 32)/" .env
sed -i "s/DB_PASSWORD=.*/DB_PASSWORD=$(openssl rand -hex 32)/" .env
nano .env   # set POOL_ADDRESS
docker compose up -d
```
