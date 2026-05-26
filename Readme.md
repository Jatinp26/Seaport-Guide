# Canton Network — Hackathon Builder Guide

> **Powered by Seaport · DevNet · SDK 3.4.11**

No validator setup. No IP allowlisting. No waiting on ops. You have a shared validator ready to go — just follow these steps.

---

## What You Have Access To

| | |
|---|---|
| **Seaport URL** | https://app.devnet.seaport.to |
| **Your Org** | ETH Global Hackathon _or_ Encode Hackathon (check with your organizer) |
| **Shared Validator** | `5n sandbox` — pre-configured, live on DevNet |
| **SDK Version** | `3.4.11` (use this version only) |
| **DevNet Wallet** | https://devnet.cantonloop.com |

---

## Step 1: Get Your DevNet Wallet

Before logging into Seaport, you need a DevNet wallet.

1. Ask your organizer for a **DevNet wallet invite code** (check your hackathon Discord/Slack channel).
2. Go to **https://devnet.cantonloop.com** and redeem the code to create your wallet.
3. Once set up, copy your **Party ID** from your wallet profile (looks like `abc123::122...34a`).

> **Save your Party ID.** Your organizer needs it to add you to the right team in Seaport.

---

## Step 2: Log Into Seaport

1. Go to **https://app.devnet.seaport.to**
2. Log in with your DevNet wallet credentials.
3. Find your hackathon org in the sidebar → navigate to **Teams** → find your team.

Inside your team you'll see:
- **Members** — your teammates
- **Projects** — where your Daml projects live after upload
- The shared **5n sandbox** validator is available to all teams automatically

> **Don't see your org?** Your organizer needs to add your Party ID first. DM them and they'll sort it.

---

## Step 3: Set Up Your Dev Environment

### Install the Daml SDK

Canton uses `dpm` (Digital Asset Package Manager) as its build tool.

```bash
curl -sSL https://get.daml.com | sh -s 3.4.11
```

Verify:

```bash
dpm version
```

If `dpm` isn't found, add it to your PATH:

```bash
export PATH="$HOME/.dpm/bin:$PATH"
```

> **SDK version lock:** Seaport's DevNet environment supports `3.4.11` only. Using a different version will cause DAR upload failures.

### Create a New Project

Scaffold from scratch:

```bash
dpm new my-hackathon-project --template skeleton
cd my-hackathon-project
```

Or clone the Canton quickstart (recommended — includes TypeScript backend + React frontend):

```bash
git clone https://github.com/digital-asset/cn-quickstart
cd cn-quickstart/quickstart
```

### Open Daml Studio

```bash
dpm studio
```

Opens VS Code with the Daml extension — syntax highlighting, type checking, and inline errors as you write.

---

## Step 4: Write and Compile Your Daml Contracts

### A Minimal Contract

```daml
module Main where

template Asset
  with
    owner : Party
    description : Text
  where
    signatory owner

    choice Archive : ()
      controller owner
      do return ()
```

Key concepts:
- `template` — a contract type (like a class)
- `with` — contract fields
- `signatory` — parties whose authorization is required to create this contract
- `choice` — an action that can be exercised on the contract
- `controller` — who can exercise the choice

### Compile to a DAR

A DAR (Daml Archive) is the deployable artifact — equivalent to a compiled smart contract.

```bash
dpm build
```

Output: `.daml/dist/<project-name>-<version>.dar`

### Test Locally

Unit test your contract logic with Daml Script:

```bash
dpm test
```

For integration testing against a local sandbox:

```bash
dpm sandbox --json-api-port 7575
```

---

## Step 5: Deploy to DevNet via Seaport

### Via the Seaport UI

1. In Seaport, go to your team → **Projects** → **New Project**
2. Connect your GitHub repo or upload a DAR file directly
3. Seaport compiles and uploads your DAR to the `5n sandbox` validator
4. Once uploaded, your package is live on DevNet

### Via the JSON API (CLI)

You'll need the **OIDC client secret** from your organizer (shared via DM — do not commit it to a public repo).

```bash
# Get an access token
TOKEN=$(curl -s -X POST <validator-oidc-url>/token \
  -d 'client_id=<client-id>' \
  -d 'client_secret=<secret-from-organizer>' \
  -d 'grant_type=client_credentials' | jq -r .access_token)

# Upload your DAR
curl -X POST https://<validator-url>/v2/packages \
  -H "Authorization: Bearer $TOKEN" \
  -T .daml/dist/my-project-1.0.0.dar
```

---

## Step 6: Interact with Your Contracts

### Create a Contract

```bash
curl -X POST https://<validator-url>/v2/commands/submit-and-wait \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "commands": [{
      "CreateCommand": {
        "templateId": "<package-id>:Main:Asset",
        "payload": {
          "owner": "<your-party-id>",
          "description": "My first Canton asset"
        }
      }
    }],
    "actAs": ["<your-party-id>"]
  }'
```

### Query Active Contracts

```bash
curl -X POST https://<validator-url>/v2/state/active-contracts \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "filter": {
      "filtersByParty": {
        "<your-party-id>": {
          "cumulative": [{
            "templateFilter": {
              "templateId": "<package-id>:Main:Asset"
            }
          }]
        }
      }
    }
  }'
```

### Via Seaport UI

No API calls needed — use Seaport's **Contracts** tab to create, browse, and exercise choices on your contracts. Full contract history (every create, exercise, archive) is visible there.

---

## Building a TypeScript/React Frontend

### Generate TypeScript Bindings

```bash
dpm codegen-js .daml/dist/my-project-1.0.0.dar -o src/generated
```

### Connect to the Ledger

Use `@c7/ledger` (the current Canton client SDK — not the deprecated `@daml/ledger`):

```bash
npm install @c7/ledger
```

```typescript
import { LedgerClient } from '@c7/ledger';

const client = new LedgerClient({
  host: '<validator-url>',
  token: '<your-jwt-token>',
});
```

---

## Quick Reference

| Resource | Link / Value |
|---|---|
| Seaport (DevNet) | https://app.devnet.seaport.to |
| DevNet Wallet | https://devnet.cantonloop.com |
| Canton Docs | https://docs.digitalasset.com/build/3.4/ |
| CN Quickstart Repo | https://github.com/digital-asset/cn-quickstart |
| SDK Version | `3.4.11` |
| JSON Ledger API port | `7575` |
| gRPC Ledger API port | `5001` |
| OIDC Secret | Get from your organizer via DM |

---

## Common Errors

| Error | Fix |
|---|---|
| DAR upload fails / version mismatch | Check `daml.yaml` — must say `sdk-version: 3.4.11` |
| `401 Unauthorized` | JWT token expired — regenerate using the OIDC endpoint |
| Party not found | Share your Party ID with your organizer to be added to the team |
| Can't see org in Seaport | Your wallet address hasn't been added yet — contact your organizer |
| Contract query returns empty | PQS may still be syncing — wait 30–60 seconds and retry |
| `dpm: command not found` | Run `export PATH="$HOME/.dpm/bin:$PATH"` |

---

> **Need help?** Post in your hackathon Discord/Slack or find a Canton mentor on-site.
>
> **Good luck. Build something great on Canton.**