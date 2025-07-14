# Slack Zaps: Nostr Zaps for the Slack Workspace

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com)
[![License](https://img.shields.io/badge/license-MIT-blue)](https://github.com)

A powerful, deeply integrated Slack application that allows users to send and receive instant, fee-less Bitcoin Lightning "zaps" as a form of appreciation and reward.

This project uses a centralized [LNbits](https://github.com/lnbits/lnbits) instance to create a frictionless internal economy. It also features optional, one-click wallet funding through the [Alby](https://getalby.com/) browser extension for an enhanced user experience.

## Table of Contents

- [Vision & Philosophy](#vision--philosophy)
- [Core Features](#core-features)
- [The User Journey](#the-user-journey)
  - [1. One-Time Admin Setup](#1-one-time-admin-setup)
  - [2. Onboarding a New User](#2-onboarding-a-new-user)
  - [3. Sending a Zap](#3-sending-a-zap)
- [The App Home: Your Central Dashboard](#the-app-home-your-central-dashboard)
- [Optional Alby Integration: One-Click Funding](#optional-alby-integration-one-click-funding)
- [Technical Architecture](#technical-architecture)
- [Installation & Configuration](#installation--configuration)
  - [Prerequisites](#prerequisites)
  - [Setup Steps](#setup-steps)
- [Command & Action Reference](#command--action-reference)
- [Security Considerations](#security-considerations)
- [Roadmap & Contributing](#roadmap--contributing)

## Vision & Philosophy

The goal is to foster a culture of recognition by removing all friction from peer-to-peer value transfer. Our philosophy is guided by two principles:

1.  **Frictionless Core Experience:** All zaps between users in the workspace are internal, instant, and completely free of fees.
2.  **Context and Choice:** Meet users where they are. A user can send a zap from anywhere they think of doing it—reacting to a message, looking at a user's profile, or firing off a command.

## Core Features

-   ⚡️ **Four Ways to Zap:** Use `/zap`, a message shortcut, the App Home GUI, or a button on a user's profile.
-   🏦 **Centralized LNbits Backend:** All payments are instant and fee-less internal ledger updates.
-   ✨ **Seamless Onboarding:** A guided `/zap-setup` command creates or links an internal wallet and automatically updates the user's Slack profile.
-   🏠 **App Home Dashboard:** A central hub to view your balance, see transaction history, and top up your wallet.
-   🔗 **Custom Profile Integration:** Onboard by editing your profile and zap users directly from their profile pop-up.
-   💡 **Optional Alby Integration:** One-click wallet funding for users with the Alby browser extension via WebLN.

## The User Journey

### 1. One-Time Admin Setup

Before the app can be used, a Slack Workspace Admin must perform a crucial one-time setup:

1.  Navigate to **Workspace Settings > Customize > People > Profile fields**.
2.  Click **"Add field"**.
3.  Create a `Text` field named **"LNbits Read Key"** (or similar).
4.  **Important:** Note the **Field ID** that Slack generates (e.g., `Xf0...`). You will need this for the `.env` configuration file.

### 2. Onboarding a New User

The user experience begins with a simple, guided setup process.

1.  **Initiation:** The user types the `/zap-setup` command.
2.  **Choice:** The app presents a private, interactive choice:
    *   **[ ✨ Create a New Internal Wallet ]**: The recommended path for all users.
    -   **[ 🔗 Link an Existing LNbits Wallet Key ]**: For advanced users who already have a wallet *on this specific LNbits instance*.
3.  **Backend Magic:**
    -   The backend creates or validates the user's LNbits wallet via the core LNbits API.
    -   It securely stores the mapping of `Slack User ID` -> `LNbits Wallet Key` in its encrypted database.
    -   It then uses the Slack API to **write the LNbits Read Key into the custom profile field** created by the admin.
4.  **Completion:** The user is notified that their setup is complete.

### 3. Sending a Zap

Sending a zap is always an internal transaction, making it simple and instant.

1.  **Initiation:** A user zaps via `/zap`, a message shortcut, the App Home, or a profile button.
2.  **Backend Logic:**
    -   The backend retrieves the LNbits wallet keys for both the sender and the recipient.
    -   It commands the central LNbits instance to perform an internal transfer.
3.  **Confirmation:** The sender receives a notification that the zap was sent instantly and for free.

## The App Home: Your Central Dashboard

The App Home tab is the user's central hub for all zap-related activity. It features:

-   **Live Balance:** Displays the current LNbits wallet balance in sats.
-   **Send a Zap Button:** The primary call-to-action for the GUI zap flow.
-   **Recent Transactions:** A list of the latest zaps sent and received.
-   **[Top Up Wallet] Button:** This is the entry point for funding the wallet, with special integration for Alby users.

## Optional Alby Integration: One-Click Funding

We enhance the wallet funding process for users who have the Alby browser extension installed. This is a **progressive enhancement** and does not affect users without the extension.

**How it Works:**

1.  A user clicks the **[Top Up Wallet]** button in their App Home.
2.  The app opens a view or sends a link to a secure, dedicated web page hosted by our backend service.
3.  This webpage displays a standard invoice and QR code for all users.
4.  Crucially, the page's JavaScript also checks for the presence of `window.webln` (injected by Alby).
5.  **If `window.webln` is detected,** an additional button, **[⚡️ Pay with Alby]**, is displayed.
6.  When the user clicks this button, the webpage requests payment of the invoice via the `webln.sendPayment()` function, prompting the user for a one-click confirmation in their Alby extension.

This flow provides a vastly superior UX for funding, turning a multi-step process into a single click, without complicating the core zap functionality.

## Technical Architecture

1.  **Slack App:** Built with **Bolt for Python/JS**, managing all native Slack UI.
2.  **Backend Service:** A web service (e.g., **FastAPI**, **Express**) that contains all business logic.
    -   Communicates with the Slack API and the central LNbits instance API.
    -   Serves a small, secure frontend page for the "Top Up" process. This page contains the JavaScript necessary to detect and interact with `window.webln`.
3.  **Central LNbits Instance:** The ledger and wallet provider for the entire internal economy.

## Installation & Configuration

### Prerequisites

-   A running LNbits instance with its URL and Admin API Key.
-   A Slack Workspace where you have administrative privileges.
-   Node.js or Python environment for the backend service.

### Setup Steps

1.  **Clone the Repository:**
    ```sh
    git clone https://github.com/your-username/slack-zaps.git
    cd slack-zaps
    ```

2.  **Install Dependencies:**
    ```sh
    # For Node.js
    npm install

    # For Python
    pip install -r requirements.txt
    ```

3.  **Configure Environment Variables:**
    Create a `.env` file and populate it:
    ```env
    # Slack App Credentials
    SLACK_BOT_TOKEN="xoxb-..."
    SLACK_SIGNING_SECRET="..."

    # LNbits Configuration
    LNBITS_URL="https://legend.lnbits.com" # Replace with your instance URL
    LNBITS_ADMIN_KEY="..." # The admin key for your LNbits instance

    # Slack Profile Field ID
    SLACK_PROFILE_FIELD_ID="Xf0..." # The ID of the custom field you created
    ```

4.  **Configure the Slack App:**
    (Follow standard setup for Scopes, Commands, Shortcuts, and App Home as detailed in previous plans). Required scopes are `chat:write`, `commands`, `users:read`, `users.profile:write`.

5.  **Run the Application:**
    ```sh
    # For Node.js
    npm start

    # For Python
    python app.py
    ```

## Command & Action Reference

| Feature      | Command / Action                               | Description                                                      |
| :----------- | :--------------------------------------------- | :--------------------------------------------------------------- |
| **Setup**    | `/zap-setup`                                   | Initiates the guided onboarding process to get an internal wallet. |
| **Send Zap** | `/zap`, Message Shortcut, GUI                  | Sends an instant, free internal payment to another user.           |
| **Balance**  | `/zap-balance`                                 | Displays your current internal wallet balance.                     |
| **Help**     | `/zap-help`                                    | Shows a help message detailing all available commands and features. |

## Security Considerations

-   **Database Encryption:** The database storing the `Slack ID <-> LNbits Key` mapping MUST be encrypted at rest.
-   **Environment Variables:** Never commit the `.env` file to version control.
-   **Request Verification:** The backend must verify the cryptographic signature of all incoming requests from Slack.
-   **WebLN Page Security:** The "Top Up" webpage must be served over HTTPS and be protected against common web vulnerabilities like XSS to ensure the integrity of the payment process.

## Roadmap & Contributing

Pull requests are welcome!

-   [ ] **Aggregated Notifications:** Bundle multiple zaps on a single message into one updating notification.
-   [ ] **Leaderboards:** Optional weekly/monthly summaries to celebrate top zappers.
-   [ ] **UI Enhancements:** Refine the "Top Up" page UI for a smoother experience.
-   [ ] **Custom Zap Emojis:** Allow users to react with a specific emoji (e.g., `:zap:`) to trigger the zap modal.
