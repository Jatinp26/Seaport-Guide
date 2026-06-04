# Canton Hackathon Builders — Seaport Guide

No validator setup. No IP allowlisting.

> **Seaport URL**: https://app.devnet.seaport.to

This guide walks you through the entire Seaport workflow — from getting your DevNet wallet and setting up the Daml SDK, to writing, building, deploying, and exercising contracts on the 5N Seaport shared `5n sandbox` validator.

---

## Who is this guide for?

This guide is for new users getting started with Seaport. Before you create, build, or deploy anything, you need to understand one thing: **Seaport has two modes, and you can only be in one at a time.**

### The two modes

| Mode | What it is | What lives in it |
|------|------------|------------------|
| **Personal mode** | Your private workspace, tied to your account. Always available — it is yours the moment you sign in. | Your personal projects, your personal validator configurations, and any contracts you create on those validators. |
| **Org mode** | A shared workspace owned by a specific **organization (org)**. You can only enter it once an org admin has added you. | That org's projects, its members, its validator configurations, and any contracts created on those validators. |

In both modes you can do the same things — create projects, write Daml, build, deploy, create contracts, exercise choices. The difference is **who can see your work and which validator you deploy to**:

- In **Personal mode**, only you can see your projects, and you deploy to one of *your* validators.
- In **Org mode**, the org's members can see your projects, and the **Validator** dropdown shows only *that org's* validators.

> **For hackathon builders:** you will be added to your team's org by the organizer. Once you are in, you will see a shared validator called **`5n sandbox`** already configured for your org — no setup required.

---

## What is Seaport?

Seaport is a web-based workspace for writing, building, and deploying smart contracts on the Canton Network. It lets you:

- **Create projects** to organise your contract code
- **Write and edit** Daml smart contracts directly in your browser
- **Build** your contracts into deployable packages (DAR files)
- **Deploy** packages to a Canton validator
- **Create live contracts** from deployed packages
- **Interact with contracts** by exercising choices (actions defined in your smart contract templates)

---

## Step 1: Get Your DevNet Wallet

Before logging into Seaport, you need a DevNet wallet.

1. Ask your organizer for a **DevNet wallet invite code** (check the hackathon's Discord Canton channel).
2. Go to **https://devnet.cantonloop.com** and redeem the code to create your wallet.

> Once set up, copy your **Party ID** from your wallet profile (looks like `abc123::122...34a`). **Save your Party ID** as your organizer needs it to add you to the right team in Seaport.

---

## Step 2: Log Into Seaport

1. Go to **https://app.devnet.seaport.to**
2. Log in with your DevNet wallet credentials.
3. Find your hackathon org in the sidebar → navigate to **Teams** → find your team.

#### Inside your team you'll see:

- **Members** — your teammates
- **Projects** — where your Daml projects live after upload
- The shared **`5n sandbox`** validator is available to all teams automatically

> **Don't see your org?** Your organizer needs to add your Party ID first. Contact them and they'll sort it.

---

## Step 3: Choose your mode

> **Do this step before you create any projects, build, or deploy.**

### The org switcher

The org switcher is the small button in the **top-left of the navigation bar**, next to the Seaport logo. It shows your current mode (e.g. "Personal" or the name of the org you are in). Click it to open a dropdown with three sections:

1. **Account** → **Personal** (enter Personal mode)
2. **Organizations** → every org you belong to (enter that org's mode)
3. A footer with **Manage organizations** and **Create organization** buttons

Click an item to switch. The whole app re-scopes to that mode — your project list, the validators you can deploy to, and the members you can invite all change.

> **Tip — tabs are scoped.** A project opened in a tab stays in whichever mode (Personal or a specific org) you opened it from. Switching the org switcher in the top bar does *not* move a project. If you started a project in Personal mode and want it in your hackathon team, use [Step 12 — Transfer Project Ownership](#step-12-transfer-project-ownership).

### How do I get added to an org?

There is no "join" button — an org admin **invites you by your Canton party ID**:

1. An org admin opens the org's **Members** tab and clicks **Invite**.
2. They paste in your **party ID** (the same one your wallet uses to sign in).
3. If you have already signed in once, the invite is applied **immediately** to your account.
4. If you have not signed in yet, the invite is held as a **pending invite** and is applied the first time you sign in.

Once added, the org appears in your org switcher automatically.

> **For hackathon builders:** this is the step your organizer handles for you — give them your Party ID from Step 1 and they will add you to your team's org.

---

## Step 4: Set Up Your Dev Environment

### Install the Daml SDK

Canton uses `dpm` (Digital Asset Package Manager) as its build tool.

```bash
curl https://get.digitalasset.com/install/install.sh | sh
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

### Create a New Project (CLI)

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

---

## Step 5: Create a Project in Seaport

A project in Seaport is a workspace that holds your Daml files. Once the project exists, you can edit files in the browser, build the project into a DAR, and deploy it.

> **Make sure you are in the right mode first.** For hackathon work, switch to your team's org in the org switcher before creating the project (or use [Step 12](#step-12-transfer-project-ownership) afterwards to move it).

### Starting from scratch

1. On the Dashboard, click **New Blank Project**.
2. Give your project a **name** (e.g. "My First Contract").
3. Pick an optional **colour** to identify it visually.
4. Click **Create**.

Your project opens in a new tab with a file explorer on the left, a code editor in the centre, and action buttons at the top.

### Starting from a template

1. Scroll down on the Dashboard to **Start With A Template**.
2. Pick a template that fits your use case (e.g. "DAML Intro Data").
3. The project is created with starter files already in place.

### Importing from GitHub (optional)

If you already have a project on GitHub (e.g. the `cn-quickstart` repo from Step 4):

1. Click **Connect GitHub** on the Dashboard.
2. Complete the GitHub authorisation flow.
3. Select a repository and branch to import.
4. Files will be pulled into a new project automatically.

---

## Step 6: Write Your Smart Contract

1. In the **File Explorer** on the left, click on a `.daml` file to open it.
2. Use the code editor to write your contract template.
3. When ready, click **Save** in the top bar (or press Ctrl/Cmd + S) to save the file.

You can also write and edit your `.daml` files locally in your favourite editor and just commit them to the GitHub repo that Seaport is connected to.

---

## Step 7: Build Your Project

A DAR is the deployable artifact equivalent to a compiled smart contract. You can build either from the CLI or from inside Seaport.

### Option A — Build inside Seaport

1. Click **Build Project** in the top bar of your project.
2. The **OUTPUT** panel at the bottom shows the build progress.
3. When the build finishes, you will see a success message and the location of the built `.dar` file.
4. The DAR file appears under the **Builds** folder in the File Explorer.

### Option B — Build from the CLI

From inside your project directory:

```bash
dpm build
```

Output: `.daml/dist/<project-name>-<version>.dar`

### Test locally

Before deploying, unit test your contract logic with Daml Script:

```bash
dpm test
```

> If the build fails, the output panel will show the error. Fix the code and try again.

---

## Step 8: Configure a Validator

> **Most hackathon builders can skip this step.** Your org's shared **`5n sandbox`** validator is set up for you automatically and will appear in the Validator dropdown the first time you deploy. If you do not see it, ask your organizer to verify your org membership.

If you ever do need to add a custom validator (for example, a private validator your team hosts), here's how:

A **Validator configuration** is a saved connection to a Canton validator: a base URL, an OIDC identity provider, and a few related settings. Configurations are **scoped to your current mode** — in Personal mode you manage *your* validators at `/validator-settings`; in Org mode you manage *that org's* validators at `/{slug}/validator-settings`. The two lists are completely independent.

### Open Validator Settings

- **From the sidebar**: click the **gear icon** (Validator Settings) in the left sidebar.
- **From the URL bar**: `/validator-settings` (Personal) or `/{slug}/validator-settings` (Org).

The page title is **Validator Settings**. The subtitle reads either *Configure Validator integration for Canton API access* (Personal) or *Configure Validator integration for {orgName}* (Org).

### Create a new Validator configuration

1. Click **New Configuration** (top-right).
2. Fill in the form:

| Field | What it is | Notes |
|-------|------------|-------|
| **Validator Name** | A friendly label you choose | e.g. "Devnet — My Validator". Shown in the Validator dropdown elsewhere in the app. |
| **Validator Environment** | Which network the validator lives on | Pick **devnet**, **testnet**, or **mainnet**. Shown as a coloured badge next to the name. |
| **Ledger API URL** | Base URL of the Canton JSON Ledger API (v2) | e.g. `https://api.example.com`. |
| **OIDC Issuer URL** | URL of the identity provider that issues tokens for this validator | e.g. `https://auth.example.com`. |
| **OIDC Client ID** | The application client ID Seaport uses to authenticate | Provided by your validator / IdP admin. |
| **Client Secret** | The matching client secret | Leave **blank** when editing an existing config to keep the current secret. |
| **API Audience** | Optional. Audience claim expected in the token | Usually empty for Keycloak. |
| **Scope** | OIDC scopes to request | Typical default: `daml_ledger_api`. |
| **Default Operator Party** | The party ID used as `actAs` when creating contracts from the Contract Factory | Optional. If unset, you must pick the party manually each time. |
| **Use Proxy Server** | Toggle on only if your validator requires a fixed IP for IP allow-listing | Rare. Leave off unless your org admin tells you to. |

3. Click **Create Configuration**.

### Edit or delete

- **Edit**: click **Edit** on a row. Leave the **Client Secret** blank to keep the current secret.
- **Delete**: click **Delete** and confirm. Existing contracts on that validator are **not** affected, but you will no longer be able to deploy new packages or create new contracts against it from this mode.

### Permissions

| Action | Personal | Org |
|--------|----------|-----|
| View configurations | Anyone signed in | Viewer and above |
| Create / edit / delete | The owner | **Admin and above** (Owner can do everything) |

---

## Step 9: Deploy to a Validator

Deploying uploads your built DAR file to a Canton validator so it can be used on the network.

> **The Validator dropdown shows only the validators for your current mode.** In Org mode you see that org's validators (including the shared `5n sandbox`); in Personal mode you see your personal validators. If the validator you expect is missing, switch the org switcher to the correct mode, or have an Admin add it in Validator Settings.

### Via the Seaport UI

1. Click **Deploy** in the top bar of your project.
2. The **Deploy** modal opens showing:
    - A list of your built DAR files (tick the ones you want to deploy).
    - A **Validator** dropdown — only the validators configured for the current mode are listed.
3. Pick the validator you want to deploy to. For hackathon work, this should be the **`5n sandbox`** validator configured for your team org.
4. Click **Deploy** and confirm when prompted.
5. Wait for the deployment to finish. On success, the modal confirms the deployment.
6. Close the modal — your DAR is now live on the chosen validator.

> **Tip:** deploy to the validator your org admin specified. If you are unsure which one, ask in your hackathon Discord Canton channel.

---

## Step 10: Create a Contract

Now that your DAR is deployed, you can create an actual contract instance from one of its templates.

> **The validator selector on this page also only shows validators for your current mode.** Switch to the org you want to create the contract in before continuing.

### Open the Contract Factory

There are two ways to get there:

- **From your project**: In the File Explorer, right-click a built `.dar` file and choose the create contract option.
- **From anywhere**: Click the **Deploy DAR** icon (lightning bolt) in the sidebar, or go to the Dashboard and click **Create Contract**.

### Fill in the form

The Contract Factory has several sections:

1. **Validator Selector** — choose which validator to create the contract on (shows only the validators for your current mode).
2. **Template List** — pick a template from the loaded DAR file (e.g. `Greeting`).
3. **Contract Form** — fill in the required fields:
    - **sender** — select or paste a party ID.
    - **message** — type the initial message text.
4. Click **Create Contract**.

5. A log panel on the right shows the creation request and result.
6. On success, you will see the new **Contract ID** — this is your live, on-ledger contract.

---

## Step 11: Exercise a Choice

A "choice" is an action you can perform on a contract — think of it as a button you press to change or end the contract's state.

### 11.1 — Find your contract

After inspecting a DAR file and selecting a template, scroll down to the **ACTIVE CONTRACTS** section. You will see a table with these columns:

| Column | Description |
|--------|-------------|
| **ID** | Shortened contract identifier — hover or click **Show Data** to see the full details |
| **Signatories** | Parties who signed the contract |
| **Time** | When the contract was created |
| **Data** | Contract payload — click **Show Data** to expand |
| **Choice** | Button to execute actions on this contract |

> **The active contracts list also shows only contracts from your current mode's validators.** To see contracts on a different validator, switch the org switcher.

### 11.2 — Pick a choice

Find the contract you want to interact with and click the **○ Choice** button in the rightmost column.

- If the template has **one available action**, you go straight to the execution form.
- If the template has **multiple actions**, a dropdown appears — pick the choice you want to execute.

### 11.3 — Fill in and execute

The view switches to **Contract Detail** mode with two panels:

- **Left panel** — Read-only contract details: signatories, observers, and the full payload.
- **Right panel** — Execution form for your selected choice, with a field for each argument.

Fill in the required fields, then click **Execute \<ChoiceName\>** to submit the transaction.

### 11.4 — After execution

The contract list refreshes automatically. What happens depends on the choice type:

| Choice type | Result |
|-------------|--------|
| **Consuming** | The contract is archived and disappears from the list |
| **Non-consuming** | The contract remains visible with updated values |

> **For hackathon builders:** every create, exercise, and archive on your team's contracts is recorded in your org's **Contracts** tab — judges and teammates can see the full history there.

---

## Step 12: Transfer Project Ownership

Use this when a project you started in one mode needs to live in another — most commonly:

- **Personal → Org**: you drafted a project in Personal and now want to share it with your hackathon team.
- **Org → Personal**: a project is no longer needed by the org and should be moved to a single user (org owner only).

### How to transfer

1. Open the project.
2. In the project toolbar, click the **three-dots (overflow) menu** next to the **Deploy** button.
3. Choose **Transfer Ownership**.
4. In the modal, pick the target org (or "Personal" for the reverse direction).
5. Confirm.

A toast confirms the transfer, and the project is removed from its old mode and added to the new one. Your open tab will reload against the new mode automatically.

### Permissions

| From → To | Who can do it |
|-----------|---------------|
| Personal project → Org | The project owner, **and** they must be **Admin or higher** in the target org |
| Org project → Personal | **Org Owner** only |
| Org project → A different org | **Org Owner** only — transfers via Personal as an intermediate step |

> **What gets transferred:** the project, its files, its builds, and any contract state attached to the project's DARs. The previous mode's members lose live-edit access immediately.

---

## Reference: Building a TypeScript/React Frontend

> This section is optional. You can use Seaport's **Contracts** tab to create, browse, and exercise choices on your contracts without writing any frontend code — full contract history (every create, exercise, archive) is visible there.

If you want to build a custom UI:

### Generate TypeScript Bindings

```bash
dpm codegen-js .daml/dist/my-project-1.0.0.dar -o src/generated
```

### Connect to the Ledger

Use `@c7/ledger` (the current Canton client SDK, not the deprecated `@daml/ledger`):

```bash
npm install @c7/ledger
```

---

## Quick reference

- **DevNet Seaport URL**: https://app.devnet.seaport.to
- **DevNet wallet**: https://devnet.cantonloop.com (redeem your organizer's invite code)
- **SDK version lock**: `3.4.11` only
- **Shared validator**: the `5n sandbox` validator is auto-configured for your team org
- **Org switcher**: top-left of the top nav (button next to the Seaport logo)
- **Validator Settings**: gear icon in the sidebar — Personal at `/validator-settings`, org at `/{slug}/validator-settings`. Admin+ can create/edit/delete org configs.
- **Transfer a project**: open the project → three-dots menu (next to Deploy) → **Transfer Ownership**
- **CLI cheatsheet**:
    - `dpm new my-project --template skeleton` — scaffold
    - `dpm build` — build DAR
    - `dpm test` — run Daml Script tests
    - `dpm codegen-js <dar> -o src/generated` — TypeScript bindings

> **Need help?** Post in your hackathon Discord Canton Channel.
