# Slack Zaps: Nostr Zaps for the Slack Workspace

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com)
[![License](https://img.shields.io/badge/license-MIT-blue)](https://github.com)

A powerful, deeply integrated Slack application that allows users to send and receive instant, fee-less Bitcoin Lightning "zaps" as a form of appreciation and reward.

This project uses a centralized [LNbits](https://github.com/lnbits/lnbits) instance to create a frictionless internal economy. It features a complete, command-free GUI for all core actions—including onboarding—and optional, one-click wallet funding through the [Alby](https://getalby.com/) browser extension.

## Table of Contents

- [Vision & Philosophy](#vision--philosophy)
- [Core Features](#core-features)
- [The User Journey](#the-user-journey)
  - [1. One-Time Admin Setup](#1-one-time-admin-setup)
  - [2. Onboarding a New User: A GUI-First Approach](#2-onboarding-a-new-user-a-gui-first-approach)
  - [3. Sending a Zap: A Multi-Interface Approach](#3-sending-a-zap-a-multi-interface-approach)
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
2.  **Context and Choice:** Meet users where they are. A user should be able to onboard, send, and manage zaps entirely through the visual interface or use slash commands for speed.

## Core Features

-   ⚡️ **Complete GUI Workflow:** Onboard, send zaps, and check your balance without ever typing a command.
-   🏦 **Centralized LNbits Backend:** All payments are instant and fee-less internal ledger updates.
-   ✨ **GUI-Driven Onboarding:** A prominent "Get Started" button in the App Home guides new users through setup.
-   🏠 **App Home Dashboard:** A central hub to view your balance, see transaction history, and initiate zaps.
-   🔗 **Deep Profile Integration:** Zap users directly from their Slack profile card for ultimate discoverability.
-   💡 **Optional Alby Integration:** One-click wallet funding for users with the Alby browser extension via WebLN.

## The User Journey

### 1. One-Time Admin Setup

To enable the powerful profile integration, a Slack Workspace Admin must perform this crucial one-time setup:

1.  Navigate to **Workspace Settings > Customize > People > Profile fields**.
2.  Click **"Add field"** and create a `Text` field named **"LNbits Read Key"** (or similar).
3.  **Important:** Note the **Field ID** (e.g., `Xf0...`). You will need this for the `.env` configuration file.

### 2. Onboarding a New User: A GUI-First Approach

We empower users to join the system from the interface they first discover it on.

**Initiation (Two Paths, One Destination):**

1.  **GUI Method (Recommended):**
    -   A new user clicks on the "Slack Zaps" app in their sidebar.
    -   The App Home displays a welcoming message and a large **[🚀 Get Started]** button.
    -   Clicking this button directly opens the setup modal.

2.  **Command Method (For Power Users):**
    -   The user types the `/zap-setup` command. This triggers the exact same setup modal as the GUI method.

**The Unified Setup Flow:**

-   No matter how it's initiated, the user is presented with an interactive modal asking them to choose:
    *   **[ ✨ Create a New Internal Wallet ]**: The recommended path for all users.
    *   **[ 🔗 Link an Existing LNbits Wallet Key ]**: For advanced users.
-   The backend then creates/validates the wallet and automatically updates the user's Slack profile, unlocking all features.

### 3. Sending a Zap: A Multi-Interface Approach

Once onboarded, users can send zaps through several intuitive interfaces.

| Method             | User Action                                                   | Use Case & Advantage                                                                                             |
| :----------------- | :------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------- |
| **Slash Command**  | `/zap @user <amt> <msg>`                                      | **Speed & Power.** Ideal for power users and for sending zaps not tied to a specific message.                    |
| **Message Shortcut**| Hover on message `(...)` -> `⚡️ Zap this message`            | **Contextual Reaction.** The most intuitive way to reward valuable content. The zap is tied directly to the message. |
| **App Home GUI**   | Go to App Home -> Click `[Send a Zap]`                        | **Visual Dashboard.** Perfect for users who prefer a graphical interface, allowing recipient selection from a list. |
| **Profile GUI**    | Click a user's profile -> `View details in App` -> `[⚡️ Zap User]` | **Ultimate Discoverability.** The most "native" feeling method, allowing users to zap anyone from their profile card. |

## The App Home: Your Central Dashboard

The App Home is a dynamic view that adapts to the user's status.

-   **For New Users:** It's an onboarding portal with the **[🚀 Get Started]** button.
-   **For Onboarded Users:** It's a full-featured dashboard showing a live balance, the **[Send a Zap]** button, recent transaction history, and the **[Top Up Wallet]** button.

## Optional Alby Integration: One-Click Funding

We enhance the wallet funding process for users who have the Alby browser extension installed via the WebLN standard.

**How it Works:**

1.  A user clicks the **[Top Up Wallet]** button in their App Home.
2.  The app sends a link to a secure, dedicated web page hosted by our backend.
3.  This page displays a standard invoice and QR code for all users.
4.  Crucially, the page's JavaScript also checks for the presence of `window.webln` (injected by Alby).
5.  **If `window.webln` is detected,** an additional button, **[⚡️ Pay with Alby]**, is displayed for a one-click payment experience.

## Technical Architecture

1.  **Slack App:** Built with **Bolt for Python/JS**, managing all native Slack UI. The backend logic for the App Home is conditional, rendering either the onboarding view or the dashboard view based on the user's status.
2.  **Backend Service:** A web service (e.g., **FastAPI**, **Express**) that contains all business logic.
3.  **Central LNbits Instance:** The ledger and wallet provider for the entire internal economy.

## Installation & Configuration

### Prerequisites

-   A running LNbits instance with its URL and Admin API Key.
-   A Slack Workspace where you have administrative privileges.
-   Node.js or Python environment for the backend service.

### Setup Steps

(Follow standard setup for cloning, installing dependencies, and configuring the `.env` file.)

### Slack App Configuration

-   Go to [api.slack.com/apps](https://api.slack.com/apps) and create your app.
-   **OAuth & Permissions:** Add `chat:write`, `commands`, `users:read`, `users.profile:write`.
-   **Slash Commands:** Configure `/zap`, `/zap-setup`, `/zap-balance`, `/zap-help`.
-   **Interactivity & Shortcuts:** Enable Interactivity. Create the "⚡️ Zap this message" shortcut.
-   **App Home:** Enable the App Home feature. This is critical for the GUI onboarding and dashboard functionality.

## Command & Action Reference

| Feature      | Command / Action                               | Description                                                      |
| :----------- | :--------------------------------------------- | :--------------------------------------------------------------- |
| **Onboarding** | App Home `[🚀 Get Started]` or `/zap-setup`     | Initiates the guided wallet setup process.                         |
| **Zap (Cmd)**| `/zap @user <amt> <msg>`                       | Sends an instant, free internal payment to another user via command. |
| **Zap (Msg)**| `(...) -> ⚡️ Zap this message`                | Opens a modal to zap the author of the selected message.         |
| **Zap (App)**| App Home `[Send a Zap]`                        | Opens a GUI modal to select a recipient and send a zap.          |
| **Zap (Profile)**| Profile `[⚡️ Zap User]`                       | Opens a GUI modal to zap the selected user from their profile. |
| **Balance**  | `/zap-balance` or App Home view                | Displays your current internal wallet balance.                     |
| **Help**     | `/zap-help`                                    | Shows a help message detailing all available commands and features. |

## Security Considerations

-   **Database Encryption:** The database storing the `Slack ID <-> LNbits Key` mapping MUST be encrypted at rest.
-   **Environment Variables:** Never commit the `.env` file to version control.
-   **Request Verification:** The backend must verify the cryptographic signature of all incoming requests from Slack.
-   **WebLN Page Security:** The "Top Up" webpage must be served over HTTPS and be protected against common web vulnerabilities.

## Roadmap & Contributing

Pull requests are welcome!

-   [ ] **Aggregated Notifications:** Bundle multiple zaps on a single message into one updating notification.
-   [ ] **Leaderboards:** Optional weekly/monthly summaries to celebrate top zappers.
-   [ ] **Custom Zap Emojis:** Allow users to react with a specific emoji (e.g., `:zap:`) to trigger the zap modal.
-   [ ] **Batch Zaps:** Zap multiple users at once from the App Home GUI.
