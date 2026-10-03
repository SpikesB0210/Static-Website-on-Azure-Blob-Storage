# Static Website on Azure Blob Storage

> **A live website with no VMs, no web server, and no OS to patch.**
> One setting, two files, one public URL.

![Azure](https://img.shields.io/badge/Microsoft_Azure-0078D4?style=flat&logo=microsoftazure&logoColor=white)
![Service](https://img.shields.io/badge/Service-Blob_Storage-0078D4?style=flat)
![Model](https://img.shields.io/badge/Model-PaaS_%2F_Serverless-success?style=flat)
![Level](https://img.shields.io/badge/Level-Beginner-informational?style=flat)
![Time](https://img.shields.io/badge/Time-~30_min-lightgrey?style=flat)

Part of my **Home Lab Series**, where I'm building out my cloud skill set one lab at a time, in public.

---
## 🎬 Watch Me Build This Lab!
https://www.loom.com/share/82f1e0e2f5184be28888a054a544953a

## Overview

Most people think putting a website on the internet means renting a server, installing a web server, and keeping it patched forever. For a **static** site (HTML, CSS, JavaScript, images), you don't need any of that.

Azure Storage has a built-in **static website** feature. Turn it on, and Azure creates a special `$web` container. Anything you upload there is served to the public over HTTPS. I manage the content; Microsoft manages everything underneath it.

## Architecture

<img width="3200" height="2000" alt="Image" src="https://github.com/user-attachments/assets/12fc1223-309c-4925-b752-e918d5b7dbd6" />

**What's not in the diagram matters most:** no virtual machine, no IIS/Apache/Nginx, no operating system, no patching, no load balancer.

## What's in This Repo

```
lab-6-azure-static-website/
├── README.md               ← you are here
├── site/
│   ├── index.html          ← homepage (upload to $web)
│   └── 404.html            ← custom error page (upload to $web)
├── docs/
│   ├── SOP.md              ← full step-by-step runbook (portal)
│   ├── troubleshooting.md  ← issues people actually hit, with fixes
│   └── images/             ← screenshots and architecture diagram
└── scripts/
    ├── deploy.sh           ← same deployment with the Azure CLI
    └── cleanup.sh          ← tear-down (safe for shared subscriptions)
```

## Quick Start

### Option 1: Azure Portal (the learning path)

In short:

1. Create a **Storage Account**: Standard, LRS, StorageV2.
2. **Data management → Static website → Enabled**. Set the index document to `index.html` and the error document to `404.html`.
3. **Containers → `$web` → Upload** both files from [`site/`](site/).
4. Open the **Primary endpoint** URL. 🎉

### Option 2: Azure CLI

```bash
git clone https://github.com/<your-username>/lab-6-azure-static-website.git
cd lab-6-azure-static-website
az login

RG=rg-lab06-yourname SA=stlab06yourname ./scripts/deploy.sh
```

## Naming Convention

| Resource | Pattern | Rule worth knowing |
|---|---|---|
| Resource Group | `rg-lab06-<yourname>` | Scoped to your subscription |
| Storage Account | `stlab06<yourname>` | **Globally unique**, 3–24 chars, lowercase letters and numbers only, because the name becomes a public DNS hostname |

## Key Concepts

| Concept | What it means here |
|---|---|
| **PaaS** | Azure runs the platform (hardware, OS, web serving). I only supply content. |
| **`$web` container** | The one container served publicly by the static website endpoint. Think of it as the filing-cabinet drawer with a glass front. |
| **Index document** | The file returned when someone visits `/`. |
| **Error document** | The file returned for any path that doesn't exist. |
| **LRS** | Locally-redundant storage: three copies in one datacenter. Cheapest tier, fine for a lab. |
| **Anonymous access** | *Not* needed. The static website endpoint serves `$web` even with blob anonymous access disabled, so leave it off. |

## Troubleshooting

The common issues: case-sensitive file names, macOS TextEdit saving rich text, Windows saving `index.html.txt`, browser caching, and the "anonymous access" setting that looks important but isn't.


## Cost

Pennies. A Standard LRS account holding a few KB of HTML, with lab-level traffic, is about as close to free as Azure gets. Clean up anyway.

## Clean Up

```bash
# Deletes only the storage account (safe in shared subscriptions)
RG=rg-lab06-yourname SA=stlab06yourname ./scripts/cleanup.sh

# Also delete the resource group, only if you created it just for this lab
DELETE_RG=true RG=rg-lab06-yourname SA=stlab06yourname ./scripts/cleanup.sh
```



## What I'd Do Differently in Production

- **Infrastructure as Code:** define the storage account and static website config in Terraform (see Lab 5 for my Terraform work).
- **CI/CD:** add a GitHub Actions workflow that syncs `site/` to `$web` on every push, so the repo becomes the source of truth.
- **Custom domain + HTTPS:** put Azure Front Door or a CDN in front for a custom domain, caching, and a WAF.
- **Consider Azure Static Web Apps** once the site needs APIs, auth, or staging environments.

## Skills Demonstrated

`Azure Storage` · `Static website hosting` · `PaaS / serverless concepts` · `Azure Portal` · `Azure CLI` · `Bash scripting` · `HTML/CSS` · `Technical documentation (SOP writing)` · `Resource lifecycle / cleanup`

---

## Credits

Based on the *"Lab 01: Hosting Your First Static Website in Azure"* SOP by **Jhante Charles**. I extended it with macOS steps, a custom 404 page, an expanded troubleshooting guide, an Azure CLI path, and cleanup that's safe for shared subscriptions.

**Author:** Benjamin Spikes · System Administrator, moving into Cloud Engineering
