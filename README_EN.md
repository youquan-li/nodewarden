<p align="center">
  <img src="./NodeWarden.png" alt="NodeWarden Logo" />
</p>

<p align="center">
  A third-party Bitwarden server running on Cloudflare Workers, fully compatible with official clients.
</p>

[![Powered by Cloudflare](https://img.shields.io/badge/Powered%20by-Cloudflare-F38020?logo=cloudflare&logoColor=white)](https://workers.cloudflare.com/)
[![License: LGPL-3.0](https://img.shields.io/badge/License-LGPL--3.0-2ea44f)](./LICENSE)
[![Deploy (R2)](https://img.shields.io/badge/Deploy%20(R2)-Cloudflare%20Workers-F38020?logo=cloudflare&logoColor=white)](https://deploy.workers.cloudflare.com/?url=https://github.com/shuaiplus/NodeWarden)
[![Deploy (KV)](https://img.shields.io/badge/Deploy%20(KV)-Cloudflare%20Workers-2ea44f?logo=cloudflare&logoColor=white)](#kv-mode-no-credit-card)
[![Latest Release](https://img.shields.io/github/v/release/shuaiplus/NodeWarden?display_name=tag)](https://github.com/shuaiplus/NodeWarden/releases/latest)
[![Sync Upstream](https://github.com/shuaiplus/NodeWarden/actions/workflows/sync-upstream.yml/badge.svg)](https://github.com/shuaiplus/NodeWarden/actions/workflows/sync-upstream.yml)

[Release Notes](./RELEASE_NOTES.md) • [Report an Issue](https://github.com/shuaiplus/NodeWarden/issues/new/choose) • [Latest Release](https://github.com/shuaiplus/NodeWarden/releases/latest)

中文文档：[`README.md`](./README.md)

> **Disclaimer**  
> This project is for learning and communication purposes only. We are not responsible for any data loss; regular vault backups are strongly recommended.  
> This project is not affiliated with Bitwarden. Please do not report issues to the official Bitwarden team.

---

## Feature Comparison Table (vs Official Bitwarden Server)

| Capability | Bitwarden  | NodeWarden | Notes |
|---|---|---|---|
| Web Vault (logins/notes/cards/identities) | ✅ | ✅ | Web-based vault management UI |
| Folders / Favorites | ✅ | ✅ | Common vault organization supported |
| Full sync `/api/sync` | ✅ | ✅ | Compatibility and performance optimized |
| Attachment upload/download | ✅ | ✅ | Backed by Cloudflare R2 (or optional KV mode) |
| mport / export | ✅ | ✅ | Fully implemented, including Bitwarden vault + attachments ZIP import. |
| Website icon proxy | ✅ | ✅ | Via `/icons/{hostname}/icon.png` |
| passkey、TOTP fields | ❌ | ✅ | Official service requires premium; NodeWarden does not |
| Multi-user | ✅ | ✅ | Full user management with invitation mechanism |
| Send | ✅ | ✅ | Text Send and File Send are supported |
| Organizations / Collections / Member roles | ✅ | ❌ | Not necessary to implement |
| Login 2FA (TOTP/WebAuthn/Duo/Email) | ✅ | ⚠️ Partial | User-level TOTP only |
| SSO / SCIM / Enterprise directory | ✅ | ❌ | Not necessary to implement |
| Emergency access | ✅ | ❌ | Not necessary to implement |
| Admin console / Billing & subscription | ✅ | ❌ | Free only |
| Full push notification pipeline | ✅ | ❌ | Not necessary to implement |

## Tested clients / platforms

- ✅ Windows desktop client (v2026.1.0)
- ✅ Mobile app (v2026.1.0)
- ✅ Browser extension (v2026.1.0)
- ✅ Linux desktop client (v2026.1.0)
- ⬜ macOS desktop client (not tested)

---

# Quick start

### One-click deploy

**Deploy steps:**

1. Fork this repository and name it **NodeWarden**.
2. Click the one-click deploy button below, rename the project to **NodeWarden2**, set **JWT_SECRET** to a 32-character random string; if you **do not have a credit card**, **use KV mode** and change the deploy command to `npm run deploy:kv`.

    [![Deploy to Cloudflare Workers (R2)](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/shuaiplus/nodewarden)

3. After deployment, open the Workers settings on the same page and disconnect the **Git repository**.
4. From the same location, reconnect the **Git repository** to the fork you created in step 1.

> [!NOTE] R2 vs KV
>- R2: typically requires a payment method; **single attachment/Send file limit is 100 MB** (**project-level limit, editable in code**); **10 GB free storage included**.
>- KV: no card required; **single attachment/Send file limit is 25 MiB** (**Cloudflare platform limit, not editable**); **1 GB free storage included**.

> [!TIP] Sync upstream (keep your fork updated):
>- Manual: open your fork on GitHub and click **Sync fork** when prompted.
>- Automatic: in your fork, go to **Actions**, click "I understand my workflows, go ahead and enable them". It will auto-sync from upstream daily at 3 AM.

### CLI deploy 

```powershell
# Clone repository
git clone https://github.com/shuaiplus/NodeWarden.git
cd NodeWarden

# Install dependencies
npm install

# Cloudflare CLI login
npx wrangler login

# Create cloud resources (D1 + R2)
npx wrangler d1 create nodewarden-db
npx wrangler r2 bucket create nodewarden-attachments

# Deploy
npm run deploy 

# (Optional) KV mode (no R2 / no credit card)
npx wrangler kv namespace create ATTACHMENTS_KV
# Put returned namespace id into wrangler.kv.toml -> [[kv_namespaces]].id
npm run deploy:kv

# To update later: re-clone and re-deploy — no need to recreate cloud resources
git clone https://github.com/shuaiplus/NodeWarden.git
cd NodeWarden
npm run deploy 
```

---
## Local development

This repo is a Cloudflare Workers TypeScript project (Wrangler).

```bash
npm install
npm run dev
```
---

## FAQ

**Q: How do I back up my data?**  
A: Use **Export vault** in your client and save the JSON file.

**Q: Which import/export formats are supported?**  
A: NodeWarden supports Bitwarden `json/csv/vault + attachments zip` and NodeWarden `vault + attachments json` in both plain and encrypted modes, and every format visible in the import selector is directly importable.  
A: It also supports direct import of Bitwarden `vault + attachments zip`, which is not directly supported by official Bitwarden Web import.

**Q: What if I forget the master password?**  
A: It can’t be recovered (end-to-end encryption). Keep it safe.

**Q: Can multiple people use it?**  
A: Yes. The first registered user becomes the admin. The admin can generate invite codes from the admin panel, and other users register with those codes.

---

## License

LGPL-3.0 License

---

## Credits

- [Bitwarden](https://bitwarden.com/) - original design and clients
- [Vaultwarden](https://github.com/dani-garcia/vaultwarden) - server implementation reference
- [Cloudflare Workers](https://workers.cloudflare.com/) - serverless platform



---
## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=shuaiplus/NodeWarden&type=timeline&legend=top-left)](https://www.star-history.com/#shuaiplus/NodeWarden&type=timeline&legend=top-left)
