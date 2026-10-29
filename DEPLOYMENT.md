# Deployment and security notes

## Environment variables

Configure these in the host's environment settings (never commit real values):

- `ADMIN_PASSWORD`: a unique admin password. The app no longer advertises or uses the old predictable `123456` default.
- `SECRET_KEY`: a long random secret. Keep it stable across deployments; changing it invalidates existing password hashes and sessions.
- `DATA_DIR=/data`: state file location. Attach persistent storage at `/data` if you need state to survive restarts.
- `PROXY_TOKEN` (optional): enables the `/proxy/...` forward-proxy endpoint only when a secret token is configured. Requests must include `X-Proxy-Token`. Leave unset to keep the endpoint disabled.

## Start command

The included `Procfile` and `railway.json` use `uvicorn main:app --host 0.0.0.0 --port $PORT`. The application entrypoint remains `main.py` to avoid breaking the existing deployment configuration.

## Important hosting note

This application provides VLESS/WebSocket tunnelling and optional HTTP forwarding. Hosting providers may restrict VPN, proxy, tunnelling, or traffic-relay workloads. These code changes improve UI and security; they do not disguise the application's function or guarantee that a provider will permit it. If a deployment was restricted, check the provider's notice and use a host whose published terms explicitly allow the workload. Do not rename files or obfuscate code to bypass an enforcement action.

## Data and compatibility

The current state format remains `rvg_state.json`; existing link and subscription records are not intentionally migrated or renamed. Back up the mounted `/data` volume before deploying. If the admin password was saved in the state file, keep the same `SECRET_KEY` so the existing password hash remains verifiable.
