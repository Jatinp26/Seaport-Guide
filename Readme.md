# Canton Hackathon Builders Seaport Guide
No validator setup. No IP allowlisting.

> **Seaport URL**: https://app.devnet.seaport.to 

## Step 1: Get Your DevNet Wallet

Before logging into Seaport, you need a DevNet wallet.

1. Ask your organizer for a **DevNet wallet invite code** (check the hackathon's Discord Canton channel).
2. Go to **https://devnet.cantonloop.com** and redeem the code to create your wallet.

> Once set up, copy your **Party ID** from your wallet profile (looks like `abc123::122...34a`). **Save your Party ID** as your organizer needs it to add you to the right team in Seaport.

## Step 2: Log Into Seaport

1. Go to **https://app.devnet.seaport.to**
2. Log in with your DevNet wallet credentials.
3. Find your hackathon org in the sidebar → navigate to **Teams** → find your team.

#### Inside your team you'll see:

- **Members** — your teammates
- **Projects** — where your Daml projects live after upload
- The shared **5n sandbox** validator is available to all teams automatically

> **Don't see your org?** Your organizer needs to add your Party ID first. Contact them and they'll sort it.

## Step 3: Set Up Your Dev Environment

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

### Create a New Project

Scaffold from scratch:

```bash
dpm new my-hackathon-project --template skeleton
cd my-hackathon-project
```

Or clone the Canton quickstart (recommended includes TypeScript backend + React frontend):

```bash
git clone https://github.com/digital-asset/cn-quickstart
cd cn-quickstart/quickstart
```

## Step 4: Write Daml Contracts and Compile Your Daml Contracts

A DAR is the deployable artifact equivalent to a compiled smart contract.

```bash
dpm build
```

Output: `.daml/dist/<project-name>-<version>.dar`

#### Test Locally

Unit test your contract logic with Daml Script:

```bash
dpm test
```

## Step 5: Deploy to DevNet via Seaport

### Via the Seaport UI

1. In Seaport, go to your team → **Projects** → **New Project**
2. Connect your GitHub repo or upload a DAR file directly
3. Seaport compiles and uploads your DAR to the `5n sandbox` validator
4. Once uploaded, your package is live on DevNet

## Step 6: Interact with Your Contracts Via Seaport UI

Use Seaport's **Contracts** tab to create, browse, and exercise choices on your contracts. Full contract history (every create, exercise, archive) is visible there.

### Building a TypeScript/React Frontend

#### Generate TypeScript Bindings

```bash
dpm codegen-js .daml/dist/my-project-1.0.0.dar -o src/generated
```

#### Connect to the Ledger

Use `@c7/ledger` (the current Canton client SDK not the deprecated `@daml/ledger`):

```bash
npm install @c7/ledger
```

> **Need help?** Post in your hackathon Discord Canton Channel.