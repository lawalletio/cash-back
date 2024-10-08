# Satsback Module for LaWallet

<!-- ![Latest Release](https://badgen.net/github/release/lawalletio/satsback/stable/?color=blue&icon=bitcoin-lightning) -->

![License](https://badgen.net/github/license/lawalletio/satsback/?color=cyan)
![Stars](https://badgen.net/github/stars/lawalletio/satsback/?color=yellow)
![Forks](https://badgen.net/github/forks/lawalletio/satsback/?color=grey)

This module is used to send cashback in sats to users who purchase with LaWallet through LaPOS using LaCard.

## Table of Contents

-   [Flow](#flow)
-   [Configuration](#configuration)
-   [To Do](#to-do)

## Flow

1. **Bob** pays with **LaCard** in **LaPOS** (only payments with **LaCard** are valid, payments with QR code don't work).
2. **LaPOS** sends an HTTP `POST` request to the `/publish` endpoint of LaWallet with`internal-transaction-start`, containing **Bob**'s payment to **LaPOS**
3. **Ledger** recive the payment and confirms it by sending `internal-transaction-ok` to LaWallet relay (`wss://relay.lawallet.ar`).
4. The **satsback module** subscribes to LaWallet relay to listen for `internal-transaction-ok` emmited by the **Ledger** and directed to **LaPOS**.
5. The **module** receives the `internal-transaction-ok`.
6. The **module** sends an HTTP `POST` request to `/publish` endpoint of LaWallet with `internal-transaction-start`, containing the satsback for **Bob**.
7. **Ledger** send `internal-transaction-ok` to LaWallet relay and **Bob** recive your satsback. (Don't showing in the map).

> NOTE: The **satsback module** MUST have a LaWallet account.

<img
    src="./public/satsback-map.svg"
    alt="satsback flow"
/>

## Configuration

### 1. Setup environment variables

Copy `.env.example` to `.env` and fill the `MODULE_PRIVATE_KEY` with the private key of LaWallet account

> It's necessary to have a LaWallet account to send internal transactions.

```bash
cp .env.example .env
```

### 2. Setup

Use correct node version

```bash
nvm use
```

Install the dependencies

```bash
pnpm i
```

### 3. Run in dev mode

```bash
pnpm dev
```

## To Do

-   [x] Fix minimum amount of satsback
-   [x] Add database
    -   [x] Add list of volunteers walias (pubkey and sats of voucher)
-   [x] Add satsback percentages (normal and volunteers) to environment variables
-   [x] makeEvent
    -   [x] Volunteers handle
        -   [x] Search in db
        -   [x] Handle sats remaining
    -   [x] Encrypt metadata of conent of event for the ledger send interal transaction
    -   [x] Save id of payments to LaPOS in db
    -   [x] Save the last timestamp of the last payment in db
-   [x] Subscribe to relay with the last timestamp
-   [x] Check if events are already recive satsback
-   [ ] Cache for prisma
    -   [ ] Volunteers model
    -   [ ] Events model
-   [ ] Endpoint to add volunteer
-   [ ] Add documentation
    -   [ ] Of the filters
    -   [ ] Of event that the module can handle and emit
    -   [ ] Of the database
    -   [ ] How avoid double satsback
    -   [ ] How handle relay disconnection
-   [ ] Dockerize
