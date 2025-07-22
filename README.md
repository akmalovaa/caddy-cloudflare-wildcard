# Caddy Cloudflare Wildcard

🚀 Simple reverse proxy server based on Caddy with wildcard SSL certificates support from Cloudflare for Homelab

To work with Cloudflare, you need to install the `caddy-dns/cloudflare` plugin.
Therefore, the `Dockerfile` rebuilds the Caddy image with this plugin included.

## Features

- ✅ Automatic wildcard SSL certificates generation and renewal via Cloudflare DNS
- ✅ Simple configuration through Docker Compose
- ✅ Ready-to-use proxy examples

## Requirements

- Docker + Docker Compose
- Domain managed through Cloudflare
- Cloudflare API token with DNS management permissions

## Quick Start

### 1. Clone the repo

```bash
git clone https://github.com/akmalovaa/caddy-cloudflare-wildcard.git
cd caddy-cloudflare-wildcard
```

### 2. Env settings

Edit the `caddy.env` file:

```env
MAIN_DOMAIN=example.com
CLOUDFLARE_API_TOKEN=your_cloudflare_api_token
```

**Getting Cloudflare API token:**
1. Login to [Cloudflare Dashboard](https://dash.cloudflare.com)
2. Go to "My Profile" → "API Tokens"
3. Create a new token with permissions:
   - Zone: Zone Settings:Read
   - Zone: Zone:Read
   - Zone: DNS:Edit

### 3. Caddyfile configuration

The `Caddyfile` already contains basic configuration with examples:

- `test.example.com` - returns test message
- `whoami.example.com` - proxies to whoami container

Add your services similarly:

```caddyfile
@myapp host myapp.{env.MAIN_DOMAIN}
handle @myapp {
    reverse_proxy myapp:8080
}
```

### 4. Launch

```bash
docker-compose up -d
```

### 5. Verify operation

After launch, verify the operation:

```bash
# Check container status
docker-compose ps

# View logs
docker-compose logs caddy
```

example successful log output:
```log
caddy-1  | {"level":"info","logger":"tls.obtain","msg":"lock acquired","identifier":"*.example.com"}
caddy-1  | {"level":"info","logger":"tls.obtain","msg":"obtaining certificate","identifier":"*.example.com"}
caddy-1  | {"level":"info","logger":"tls.issuance.acme","msg":"waiting on internal rate limiter","identifiers":["*.example.com"],"ca":"https://acme-v02.api.letsencrypt.org/directory","account":""}
caddy-1  | {"level":"info","logger":"tls.issuance.acme","msg":"done waiting on internal rate limiter","identifiers":["*.example.com"],"ca":"https://acme-v02.api.letsencrypt.org/directory","account":""}
caddy-1  | {"level":"info","logger":"tls.issuance.acme","msg":"using ACME account","account_id":"https://acme-v02.api.letsencrypt.org/acme/acct/2541678081","account_contact":[]}
caddy-1  | {"level":"info","msg":"trying to solve challenge","identifier":"*.example.com","challenge_type":"dns-01","ca":"https://acme-v02.api.letsencrypt.org/directory"}
caddy-1  | {"level":"info","msg":"authorization finalized","identifier":"*.example.com","authz_status":"valid"}
```

## Project structure

```
.
├── Dockerfile          # Build Caddy with Cloudflare plugin
├── Caddyfile          # Caddy configuration
├── caddy.env          # Environment variables
├── compose.yaml       # Docker Compose configuration
└── README.md          # This file
```

## Troubleshooting

### openssl check wildcard certificate

```bash
cd caddy_data/caddy/certificates/acme-v02.api.letsencrypt.org-directory/wildcard_.example.com/
ls -la
openssl x509 -in wildcard_.example.com.crt --text -noout
```


### Certificate generation issues

1. **Check Cloudflare API token:**
```bash
curl -X GET "https://api.cloudflare.com/client/v4/user/tokens/verify" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

2. **Check Caddy logs:**
```bash
docker-compose logs caddy | grep -i error
```

3. **Make sure domain is managed through Cloudflare**


## Security

- 🔒 All connections are automatically redirected to HTTPS
- 🔒 Modern TLS settings are used
- 🔒 Keep your Cloudflare API token secure
- 🔒 Regularly update Docker images

## Useful commands

```bash
# Reload configuration without restart
docker-compose exec caddy caddy reload --config /etc/caddy/Caddyfile

# Validate configuration
docker-compose exec caddy caddy validate --config /etc/caddy/Caddyfile

# View active certificates
docker-compose exec caddy caddy list-certificates

# Force certificate renewal
docker-compose exec caddy caddy reload --config /etc/caddy/Caddyfile
```

## License

MIT License - use as you wish!

## Support

If you encounter problems:
1. Check [official Caddy documentation](https://caddyserver.com/docs/)
2. Study [Cloudflare plugin documentation](https://github.com/caddy-dns/cloudflare)
3. Create an issue in this repository

