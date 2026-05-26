# Forms — Service Department

Unmanaged Visualforce PDF forms for [DealerTeam DMS](https://www.dealerteam.com/) customer orgs. Deploy these pages into your org to customize service print layouts without modifying the managed package.

<a href="https://githubsfdeploy.herokuapp.com?owner=DealerTeam&repo=Forms-Service-Department&ref=main">
  <img alt="Deploy to Salesforce" src="https://raw.githubusercontent.com/afawcett/githubsfdeploy/master/src/main/webapp/resources/img/deploy.png"/>
</a>

> **Deploy to a sandbox?** Use the same button above and choose **Sandbox** on the deployment screen, or append `&endpoint=https://test.salesforce.com` to the URL.

## Overview

DealerTeam DMS ships managed Visualforce pages for common service documents. Those pages cannot be edited directly in subscriber orgs. This repository provides **unmanaged copies** of those pages (prefixed with `c`) so you can:

- Adjust layout, branding, and field visibility
- Add or remove sections for your dealership's workflow
- Test changes safely without touching the managed package

Each page uses the same Apex controllers and extensions from the DealerTeam managed package (`dealer__` namespace). No additional Apex is included in this repository.

## Prerequisites

Before deploying, confirm your target org has:

| Requirement | Details |
|-------------|---------|
| **DealerTeam DMS** | The managed package must be installed. These pages depend on `dealer__` objects, fields, and Apex classes. |
| **Service Repair Order access** | Users who print these forms need access to `dealer__Service_Repair_Order__c` and related records. |
| **Deploy permissions** | The deploying user needs **Modify All Data** or **Customize Application**, plus permission to author Apex pages. |

> **Note:** A scratch org created from `config/project-scratch-def.json` alone will **not** include DealerTeam DMS. Use a sandbox or production org that already has the package installed.

## Included Forms

| Unmanaged Page | Managed Package Equivalent | Description | Controller / Extension |
|----------------|---------------------------|-------------|------------------------|
| `cServiceRepairOrderPDF` | `dealer__ServiceRepairOrderPDF` | Standard service repair order PDF | `dealer__ServiceRepairOrder_EXT` |
| `cServiceRepairOrderAuditPDF` | `dealer__ServiceRepairOrderAuditCopyPDF` | Audit copy of the repair order | `dealer__SROPrint` |
| `cServiceRepairOrderWarranty` | `dealer__ServiceRepairOrderWarrantyCopy` | Warranty copy of the repair order | `dealer__SROPrint` |
| `cTechJobCardPDF` | `dealer__TechJobCardPDF` | Technician job card | `dealer__ServiceRepairOrder_EXT` |

All pages:

- Render as PDF (`renderAs="PDF"`)
- Use `dealer__Service_Repair_Order__c` as the standard controller
- Target API version **64.0**

## Quick Deploy (One Click)

1. Click **Deploy to Salesforce** above.
2. Log in to your target org (production, developer edition, or sandbox).
3. Review the components and click **Deploy**.
4. Wait for the deployment to complete, then verify the four Visualforce pages appear under **Setup → Visualforce Pages**.

The deploy button uses the [GitHub Salesforce Deploy Tool](https://githubsfdeploy.herokuapp.com/) and reads metadata from the `main` branch of this repository.

## Manual Deployment (Salesforce CLI)

If you prefer the CLI, clone this repo and deploy with [Salesforce CLI](https://developer.salesforce.com/tools/salesforcecli) (`sf`):

```bash
git clone https://github.com/DealerTeam/Forms-Service-Department.git
cd Forms-Service-Department

# Authenticate to your org
sf org login web --alias my-org --set-default

# Deploy Visualforce pages
sf project deploy start --source-dir force-app --wait 10
```

### Deploy to a sandbox

```bash
sf org login web --alias my-sandbox --instance-url https://test.salesforce.com --set-default
sf project deploy start --source-dir force-app --wait 10
```

### Validate without deploying

```bash
sf project deploy validate --source-dir force-app --wait 10
```

## Using the Pages After Deployment

Each page is accessed via a Visualforce URL on a Service Repair Order record. Replace `{ORG_DOMAIN}` and `{RECORD_ID}` with your values:

| Form | URL Pattern |
|------|-------------|
| Repair Order PDF | `https://{ORG_DOMAIN}/apex/cServiceRepairOrderPDF?id={RECORD_ID}` |
| Audit Copy PDF | `https://{ORG_DOMAIN}/apex/cServiceRepairOrderAuditPDF?id={RECORD_ID}` |
| Warranty Copy PDF | `https://{ORG_DOMAIN}/apex/cServiceRepairOrderWarranty?id={RECORD_ID}` |
| Tech Job Card PDF | `https://{ORG_DOMAIN}/apex/cTechJobCardPDF?id={RECORD_ID}` |

### Pointing buttons and links to your custom pages

After deployment, update any custom buttons, Lightning actions, or page overrides in your org to reference the `c`-prefixed page names instead of the managed package pages. Common places to check:

- Service Repair Order page layouts and buttons
- Custom list views or Lightning record actions
- Workflow email templates or automation that embed Visualforce URLs

Managed package buttons will continue to use the original `dealer__` pages until you change them.

## Customizing

1. Deploy the pages to your org (or clone and edit locally first).
2. Edit the `.page` files under `force-app/main/default/pages/`.
3. Redeploy with the CLI or the deploy button.

Tips:

- Keep the `StandardController` and `extensions` attributes unchanged unless you also provide replacement Apex.
- PDF styling is controlled by the embedded CSS in each page's `<style>` block and `@page` rules.
- Test PDF output with real Service Repair Order records that include jobs, parts, and customer data.

## Project Structure

```
Forms-Service-Department/
├── config/
│   └── project-scratch-def.json   # Scratch org definition (DMS not included)
├── force-app/
│   └── main/
│       └── default/
│           └── pages/               # Unmanaged Visualforce PDF pages
├── sfdx-project.json                # Salesforce DX project configuration
├── LICENSE
└── README.md
```

## Troubleshooting

| Issue | Likely cause | Resolution |
|-------|--------------|------------|
| Deployment fails on Apex references | DealerTeam DMS not installed | Install the managed package in the target org first |
| Page loads but PDF is blank | Missing record ID or insufficient field access | Pass a valid `id` query parameter; check FLS and record access |
| Managed page still appears | Org still references `dealer__` page names | Update buttons, links, and overrides to use `c`-prefixed pages |
| Deploy button shows no components | Branch or repo mismatch | Confirm you deploy from `main` and that `sfdx-project.json` is present |

## Contributing

1. Fork the repository.
2. Create a feature branch from `main`.
3. Make your changes and test in an org with DealerTeam DMS installed.
4. Open a pull request with a clear description of the form changes.

## License

This project is licensed under the [MIT License](LICENSE).

Copyright (c) 2025 DealerTeam
