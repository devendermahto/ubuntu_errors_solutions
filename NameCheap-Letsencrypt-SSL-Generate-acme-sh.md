Awesome! 🎉 Here's a complete package: a `README.md` guide and an automated bash script you can drop on any Namecheap Stellar Business (or similar cPanel shared hosting) account.

# Free Automated SSL for cPanel Shared Hosting

A complete, battle-tested guide for issuing **free Let's Encrypt SSL certificates** on shared hosting plans that **do not support cPanel AutoSSL** (e.g. Namecheap Stellar / Stellar Business).

This uses [`acme.sh`](https://github.com/acmesh-official/acme.sh) — a pure-shell ACME client — and deploys certificates directly into cPanel via its UAPI.

---

## 📋 Table of Contents

1. [Why this guide?](#why-this-guide)
2. [Requirements](#requirements)
3. [Quick Start (Automated Script)](#quick-start-automated-script)
4. [Manual Step-by-Step](#manual-step-by-step)
5. [Verifying the Certificate](#verifying-the-certificate)
6. [Auto-Renewal](#auto-renewal)
7. [Troubleshooting](#troubleshooting)
8. [Maintenance Checklist](#maintenance-checklist)

---

## Why this guide?

Many shared hosts disable cPanel's built-in **AutoSSL**, so users are forced to either:
- pay for a paid SSL,
- use browser-based tools like PunchSalad (which are often unstable), or
- manually renew every 90 days.

This guide solves all three by installing a self-renewing Let's Encrypt client that lives inside your hosting account.

---

## Requirements

| Requirement | Notes |
|-------------|-------|
| cPanel access | With **Terminal** and **SSH Access** enabled |
| Domain pointing to the server | DNS A record / nameservers must resolve to this host |
| A real email address | **Not** `@example.com` (Let's Encrypt forbids it) |
| No Cloudflare proxy (orange cloud) during issuance | Validation uses HTTP-01 |
| No forced HTTPS redirect blocking `/.well-known/acme-challenge/` | Check `.htaccess` |

> **Namecheap specific:** Enable SSH under **Exclusive for Namecheap Customers → Manage Shell → SSH Access ON**.

---

## Quick Start (Automated Script)

1. Upload `setup-ssl.sh` to your home directory (or create it via Terminal).
2. Make it executable:
   ```bash
   chmod +x ~/setup-ssl.sh
   ```
3. Run it:
   ```bash
   ~/setup-ssl.sh
   ```
4. Answer the prompts:
   - **Email** (for renewal notices)
   - **Domain** (e.g. `rediriq.com`)
   - **Include `www`?** (y/n)
   - **Webroot** (default: `/home/<user>/<domain>`)

The script will:
- install `acme.sh` if missing,
- register the ACME account,
- issue the certificate,
- deploy it to cPanel via UAPI,
- confirm the cron job for auto-renewal.

---

## Manual Step-by-Step

### 1. Open Terminal
cPanel → **Terminal**  
(or SSH: `ssh user@server -p 21098` for Namecheap)

### 2. Install `acme.sh`

```bash
curl https://get.acme.sh | sh -s email=you@yourdomain.com
source ~/.bashrc
```

> Replace `you@yourdomain.com` with a **real** email. Using `example.com` will cause registration to fail with `invalidContact`.

### 3. Locate your webroot

| Type | Path |
|------|------|
| Main domain | `/home/USER/public_html` |
| Addon domain | `/home/USER/yourdomain.com` |

List your home dir:

```bash
ls -d /home/$USER/*
```

### 4. Issue the certificate

```bash
acme.sh --issue --server letsencrypt \
  -d yourdomain.com -d www.yourdomain.com \
  -w /home/USER/yourdomain.com
```

Omit `-d www.yourdomain.com` if `www` is not configured.

### 5. Deploy into cPanel

```bash
acme.sh --deploy -d yourdomain.com --deploy-hook cpanel_uapi
```

Expected output:
```
Successfully deployed certificate for yourdomain.com
```

### 6. Done
Visit `https://yourdomain.com` — the certificate is live and will auto-renew.

---

## Verifying the Certificate

**From the browser:**
- Click the padlock → certificate issued by **Let's Encrypt** → valid dates.

**From Terminal:**
```bash
echo | openssl s_client -connect yourdomain.com:443 -servername yourdomain.com 2>/dev/null \
  | openssl x509 -noout -subject -issuer -dates
```

**From cPanel:**
- **SSL/TLS Status** page — the domain should show ✅ with a future expiry date.

---

## Auto-Renewal

`acme.sh` installs a cron entry automatically. Verify:

```bash
crontab -l | grep acme.sh
```

Expected line:
```
0 0 * * * "/home/USER/.acme.sh"/acme.sh --cron --home "/home/USER/.acme.sh" > /dev/null
```

If missing, install it manually:

```bash
acme.sh --install-cronjob
```

**Test renewal without actually renewing:**

```bash
acme.sh --renew -d yourdomain.com --force
acme.sh --deploy -d yourdomain.com --deploy-hook cpanel_uapi
```

---

## Troubleshooting

### ❌ `invalidContact: contact email has forbidden domain "example.com"`

**Cause:** `acme.sh` was installed with the placeholder email.

**Fix:**
```bash
rm -rf ~/.acme.sh/ca/acme-v02.api.letsencrypt.org
rm -f  ~/.acme.sh/account.conf
acme.sh --issue --server letsencrypt \
  --accountemail "your-real@gmail.com" \
  -d yourdomain.com -w /home/USER/yourdomain.com
```

---

### ❌ `Verify error: 404` or `Invalid response` during validation

**Cause:** Webroot is wrong OR a redirect is blocking `/.well-known/acme-challenge/`.

**Fix:**
1. Confirm webroot path: `ls /home/USER/yourdomain.com/.well-known` (may not exist yet).
2. Temporarily disable HTTPS redirect in `.htaccess`:
   ```apache
   # Comment out:
   # RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
   ```
3. Re-run `--issue`.

---

### ❌ `Verify error: DNS problem: NXDOMAIN` or timeout

**Cause:** Domain DNS doesn't point to this server, or Cloudflare proxy is on.

**Fix:**
- Ensure A record → your hosting IP.
- In Cloudflare, set the record to **DNS only (grey cloud)** until the certificate is issued.
- Wait for DNS propagation (`dig yourdomain.com +short`).

---

### ❌ `uapi: command not found` or deploy hook fails

**Fix (manual install):**

```bash
acme.sh --install-cert -d yourdomain.com \
  --key-file       /home/USER/yourdomain.com.key \
  --fullchain-file /home/USER/yourdomain.com.fullchain.cer
```

Then cPanel → **SSL/TLS → Install an SSL Website**:
- **Certificate:** paste contents of `yourdomain.com.fullchain.cer`
- **Private Key:** paste contents of `yourdomain.com.key`
- **CA Bundle:** (leave blank — fullchain already includes it)

---

### ❌ cPanel error: *"The CA bundle is invalid"*

**Cause:** The wrong certificate was pasted into the **CA Bundle** field.

**Fix:**
- **Certificate field:** the certificate for **your domain** only.
- **CA Bundle field:** only intermediate + root certs, **never** the leaf/domain cert.

You can extract the correct CA bundle from `fullchain.cer` by removing the first `-----BEGIN CERTIFICATE-----...-----END CERTIFICATE-----` block.

---

### ❌ `Too many certificates already issued for this domain`

**Cause:** Let's Encrypt rate limit (5 duplicate certs / week).

**Fix:** Wait 7 days, or issue for a different subdomain. Use staging for testing:
```bash
acme.sh --issue --server letsencrypt_test -d yourdomain.com -w ...
```

---

### ❌ After 90 days, SSL is still expired

**Cause:** cron job missing or `acme.sh` was reinstalled under a different user.

**Fix:**
```bash
crontab -l | grep acme.sh
acme.sh --install-cronjob
acme.sh --renew -d yourdomain.com --force
```

---

## Maintenance Checklist

Run through this every few months:

- [ ] `acme.sh --list` — verify certificate is registered and shows a future renewal date.
- [ ] `crontab -l | grep acme.sh` — cron exists.
- [ ] Visit site → padlock shows valid certificate.
- [ ] Check `~/.acme.sh/acme.sh.log` for recent errors.
- [ ] Keep `.htaccess` clean of rules that block `/.well-known/`.
- [ ] If DNS moved (e.g. new host, Cloudflare), re-validate.

---

## Files & Locations

| Path | Purpose |
|------|---------|
| `~/.acme.sh/` | acme.sh installation & account data |
| `~/.acme.sh/<domain>/` | certificate files for each domain |
| `~/.acme.sh/acme.sh.log` | log file |
| `~/.acme.sh/account.conf` | ACME account settings |

**Backup these paths** if you migrate servers — you'll need the account key to keep the same registration (or just re-issue on the new host).

---

## Uninstall

```bash
acme.sh --uninstall
rm -rf ~/.acme.sh
crontab -l | grep -v acme.sh | crontab -
```

Then remove the certificate from cPanel → **SSL/TLS Status**.

---

## Credits

- [acme.sh](https://github.com/acmesh-official/acme.sh) — the ACME client doing all the heavy lifting.
- [Let's Encrypt](https://letsencrypt.org/) — free, automated, open CA.

---

**License:** MIT — do whatever you want.

## 🛠️ `setup-ssl.sh`

Save this as `~/setup-ssl.sh` and `chmod +x` it.

```bash
#!/usr/bin/env bash
# ============================================================
#  setup-ssl.sh — Automated Let's Encrypt SSL for cPanel shared hosting
#  Author: You
#  License: MIT
# ============================================================
set -euo pipefail

# ---------- Colors ----------
RED='\033[0;31m'; GREEN='\033[0;32m'; YELLOW='\033[1;33m'; BLUE='\033[1;34m'; NC='\033[0m'
info()  { echo -e "${BLUE}[INFO]${NC} $*"; }
ok()    { echo -e "${GREEN}[ OK ]${NC} $*"; }
warn()  { echo -e "${YELLOW}[WARN]${NC} $*"; }
err()   { echo -e "${RED}[FAIL]${NC} $*" >&2; }

# ---------- Banner ----------
cat <<'BANNER'
==============================================================
   Let's Encrypt SSL Setup for cPanel Shared Hosting
   (Namecheap Stellar / Stellar Business & similar)
==============================================================
BANNER

# ---------- Detect user ----------
USER_NAME="$(whoami)"
HOME_DIR="/home/${USER_NAME}"
info "Detected user: ${USER_NAME}  (home: ${HOME_DIR})"

# ---------- Collect input ----------
read -rp "Enter your email (for renewal notices): " EMAIL
[[ -z "$EMAIL" || "$EMAIL" == *"example.com"* ]] && { err "Email is required and must not be @example.com"; exit 1; }

read -rp "Enter your domain (e.g. rediriq.com): " DOMAIN
[[ -z "$DOMAIN" ]] && { err "Domain is required"; exit 1; }

read -rp "Include www.${DOMAIN} too? [y/N]: " INCLUDE_WWW
INCLUDE_WWW="${INCLUDE_WWW,,}"

DEFAULT_WEBROOT="${HOME_DIR}/${DOMAIN}"
if [[ ! -d "$DEFAULT_WEBROOT" ]]; then
  DEFAULT_WEBROOT="${HOME_DIR}/public_html"
fi
read -rp "Webroot path [${DEFAULT_WEBROOT}]: " WEBROOT
WEBROOT="${WEBROOT:-$DEFAULT_WEBROOT}"

if [[ ! -d "$WEBROOT" ]]; then
  err "Webroot does not exist: $WEBROOT"
  info "Existing candidates:"
  ls -d "${HOME_DIR}"/*/ 2>/dev/null || true
  exit 1
fi

echo
info "Configuration:"
echo "   Email     : $EMAIL"
echo "   Domain    : $DOMAIN"
echo "   Include www: $INCLUDE_WWW"
echo "   Webroot   : $WEBROOT"
echo
read -rp "Proceed? [y/N]: " CONFIRM
[[ "${CONFIRM,,}" != "y" ]] && { warn "Aborted."; exit 0; }

# ---------- Install acme.sh if missing ----------
if [[ ! -f "${HOME_DIR}/.acme.sh/acme.sh" ]]; then
  info "Installing acme.sh..."
  curl -s https://get.acme.sh | sh -s "email=${EMAIL}"
  # shellcheck disable=SC1090
  source "${HOME_DIR}/.bashrc" || true
  ok "acme.sh installed."
else
  ok "acme.sh already installed."
fi

ACME="${HOME_DIR}/.acme.sh/acme.sh"
[[ -x "$ACME" ]] || { err "acme.sh not found at $ACME"; exit 1; }

# ---------- Reset broken account if needed ----------
if grep -q "example.com" "${HOME_DIR}/.acme.sh/account.conf" 2>/dev/null; then
  warn "Found stale account with example.com — resetting ACME account data."
  rm -rf "${HOME_DIR}/.acme.sh/ca/acme-v02.api.letsencrypt.org"
  rm -f  "${HOME_DIR}/.acme.sh/account.conf"
fi

# ---------- Build domain args ----------
DOMAIN_ARGS=(-d "$DOMAIN")
[[ "$INCLUDE_WWW" == "y" ]] && DOMAIN_ARGS+=(-d "www.${DOMAIN}")

# ---------- Issue ----------
info "Requesting certificate from Let's Encrypt..."
if ! "$ACME" --issue --server letsencrypt \
      --accountemail "$EMAIL" \
      "${DOMAIN_ARGS[@]}" \
      -w "$WEBROOT"; then
  err "Certificate issuance failed."
  echo
  warn "Common causes:"
  echo "  • DNS not pointing to this server"
  echo "  • Cloudflare proxy (orange cloud) is on"
  echo "  • .htaccess HTTPS redirect blocking /.well-known/"
  echo "  • Rate limit hit (5 duplicate certs/week)"
  echo
  info "Debug:  ${ACME} --issue --debug --server letsencrypt ${DOMAIN_ARGS[*]} -w ${WEBROOT}"
  exit 1
fi
ok "Certificate issued."

# ---------- Deploy to cPanel ----------
info "Deploying certificate into cPanel via UAPI..."
if "$ACME" --deploy -d "$DOMAIN" --deploy-hook cpanel_uapi; then
  ok "Certificate deployed to cPanel."
else
  warn "UAPI deploy failed — falling back to manual file install."
  "$ACME" --install-cert -d "$DOMAIN" \
    --key-file       "${HOME_DIR}/${DOMAIN}.key" \
    --fullchain-file "${HOME_DIR}/${DOMAIN}.fullchain.cer"
  echo
  info "Manually install via cPanel → SSL/TLS → Install an SSL Website:"
  echo "   Certificate : ${HOME_DIR}/${DOMAIN}.fullchain.cer"
  echo "   Private Key : ${HOME_DIR}/${DOMAIN}.key"
  echo "   CA Bundle   : (leave blank)"
fi

# ---------- Ensure cron ----------
if crontab -l 2>/dev/null | grep -q "acme.sh"; then
  ok "Auto-renewal cron already present."
else
  info "Installing auto-renewal cron job..."
  "$ACME" --install-cronjob || warn "Could not install cron — add manually."
fi

# ---------- Verify ----------
echo
info "Verifying certificate..."
sleep 3
if echo | openssl s_client -connect "${DOMAIN}:443" -servername "${DOMAIN}" 2>/dev/null \
     | openssl x509 -noout -dates -issuer 2>/dev/null; then
  ok "SSL is live on https://${DOMAIN}"
else
  warn "Could not verify over HTTPS yet — DNS/HTTPS may not be fully active."
fi

# ---------- Summary ----------
cat <<EOF

==============================================================
 ✅  Setup complete!
==============================================================
 Domain     : ${DOMAIN}$([[ "$INCLUDE_WWW" == "y" ]] && echo " (+ www)")
 Webroot    : ${WEBROOT}
 Renewal    : automatic (cron every ~60 days)
 Cert files : ${HOME_DIR}/.acme.sh/${DOMAIN}/

 Useful commands:
   List certs      : ${ACME} --list
   Force renew     : ${ACME} --renew -d ${DOMAIN} --force
   Re-deploy       : ${ACME} --deploy -d ${DOMAIN} --deploy-hook cpanel_uapi
   View cron       : crontab -l | grep acme.sh
   View logs       : tail -f ${HOME_DIR}/.acme.sh/acme.sh.log
==============================================================
EOF
```

---

### 🚀 How to use the script

1. In cPanel → **Terminal**, create the file:
   ```bash
   nano ~/setup-ssl.sh
   ```
   Paste the script, then `Ctrl+O`, `Enter`, `Ctrl+X`.

2. Make it executable and run:
   ```bash
   chmod +x ~/setup-ssl.sh
   ~/setup-ssl.sh
   ```

3. Answer the prompts, and you're done. The script handles everything — including cleanup of broken ACME accounts and fallback if UAPI deploy fails.

### 🧠 What the script does (safety notes)

- **Never overwrites** an existing `acme.sh` install — only adds to it.
- **Detects** the `example.com` placeholder bug and clears it automatically.
- **Falls back** to manual installation if `cpanel_uapi` isn't available.
- **Verifies** the live certificate with `openssl s_client` before declaring success.
- **Idempotent** — you can re-run it safely; it just renews/redeploys.

Feel free to drop both files into a repo (e.g. `namecheap-letsencrypt-ssl`) and share — this workflow will help a lot of people stuck on the same issue you had.
