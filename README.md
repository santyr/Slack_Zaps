# Slack Zaps: Nostr Zaps for the Slack Workspace

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com)
[![License](https://img.shields.io/badge/license-MIT-blue)](https://github.com)

A powerful, deeply integrated Slack application that allows users to send and receive Bitcoin Lightning "zaps". This app functions as a hybrid system, supporting both a zero-fee internal economy and interoperability with the global Lightning Network via Lightning Addresses, all while adhering to the spirit of Nostr's design patterns.

This project uses a centralized [LNbits](https://github.com/lnbits/lnbits) instance for internal users and the LNURL standard to connect to any external Lightning Address (e.g., from [Alby](https://getalby.com/)).

## Table of Contents

- [Vision & Philosophy](#vision--philosophy)
- [Core Features](#core-features)
- [Internal vs. External Zaps: Understanding the Difference](#internal-vs-external-zaps-understanding-the-difference)
- [Adhering to Nostr Design Patterns](#adhering-to-nostr-design-patterns)
- [The User Journey](#the-user-journey)
  - [1. One-Time Admin Setup](#1-one-time-admin-setup)
  - [2. Onboarding a New User: The Power of Choice](#2-onboarding-a-new-user-the-power-of-choice)
  - [3. Sending a Zap](#3-sending-a-zap)
- [The App Home: Your Dynamic Dashboard](#the-app-home-your-dynamic-dashboard)
- [Technical Architecture](#technical-architecture)
- [Installation & Configuration](#installation--configuration)
- [Command & Action Reference](#command--action-reference)
- [Security Considerations](#security-considerations)
- [Roadmap & Contributing](#roadmap--contributing)

## Vision & Philosophy

The goal is to create the most versatile and user-friendly tipping tool for Slack. Our philosophy is guided by three principles:

1.  **Flexibility and Choice:** Users choose how they want to participate. They can use a frictionless internal wallet for free, instant zaps between colleagues, or link their own personal Lightning Address to interact with the global network.
2.  **Context and Interface:** Meet users where they are. A user can onboard, send, and manage zaps entirely through the visual interface or use slash commands for speed.
3.  **Nostr Alignment:** Structure the user experience around the established and successful patterns of the Nostr ecosystem, such as zap requests and receipts.

## Core Features

-   ⚡️ **Hybrid Wallet Support:** Onboard with the internal wallet for free zaps, or link any external Lightning Address (`user@domain.com`).
-   💸 **Multiple GUI Interfaces:** Onboard, send zaps, and manage your wallet without ever typing a command.
-   🧠 **Intelligent Payment Routing:** The backend automatically detects if a payment is internal (free) or external (via LNURL) and processes it accordingly.
-   📜 **Nostr-Style Workflow:** The zap process mimics the "Zap Request" -> "Zap Receipt" flow common in Nostr clients.
-   🏠 **Dynamic App Home:** The user's dashboard adapts based on their wallet type, showing a live balance for internal users and address details for external users.
-   🔗 **Seamless Profile Integration:** Onboard and initiate zaps directly from a user's Slack profile card.

## Internal vs. External Zaps: Understanding the Difference

This app supports two types of recipients, and the experience is slightly different for each. **Note: To send any type of zap, the sender must use the internal wallet.**

| Feature               | Recipient is Internal (LNbits Wallet) | Recipient is External (Lightning Address)                 |
| :-------------------- | :------------------------------------ | :-------------------------------------------------------- |
| **Recipient Address** | Managed by the app                    | Provided by user (e.g., `user@getalby.com`)             |
| **Speed**             | **Instant**                           | Fast (standard Lightning Network speed)                   |
| **Fees to Send**      | **Zero Fees**                         | Standard Lightning Network routing fees (usually tiny)    |
| **App Home View**     | Shows live balance, transaction history, top-up button | Shows the linked Lightning Address                       |
| **Primary Use Case**  | Frictionless tipping within the workspace | Receiving zaps directly to a personal, non-custodial wallet |

## Adhering to Nostr Design Patterns

While we don't post events to public Nostr relays, we adopt the spirit of its design for a familiar and robust experience.

1.  **The Zap Request (Kind 9734 spirit):** When a user initiates a zap (from any interface), the confirmation modal appears. This modal, containing the sender, recipient, amount, and message context, serves as our "Zap Request." Clicking **"Send"** is the user signing off on this request.

2.  **The Zap Receipt (Kind 9735 spirit):** After the payment is successfully processed (either internally or via LNURL), the app posts a notification. This is our "Zap Receipt." It is posted as a **threaded reply** to the original message (if applicable), **tags the recipient** (`p` tag equivalent), and includes the amount and message, creating a public, contextual record of the completed zap.

## The User Journey

### 1. One-Time Admin Setup

A Workspace Admin must create a custom profile field for discoverability:

1.  Navigate to **Workspace Settings > Customize > People > Profile fields**.
2.  Click **"Add field"** and create a `Text` field named **"Zap Address"** or similar.
3.  Note the **Field ID** (e.g., `Xf0...`) for the `.env` configuration file.

### 2. Onboarding a New User: The Power of Choice

**Initiation (GUI or Command):**
-   **GUI:** A new user clicks the **[🚀 Get Started]** button in the App Home.
-   **Command:** A user types `/zap-setup`.

**The Unified Setup Flow:**
Both paths lead to an interactive modal with three clear options:
1.  **[ ✨ Create an Internal Wallet ]**: The recommended path. Creates a new wallet on the central LNbits instance for free, instant zaps. Required for *sending* zaps.
2.  **[ ⚡️ Link a Lightning Address ]**: For users who want to receive zaps directly to their personal wallet (e.g., Alby, Wallet of Satoshi). The app will ask for their address (e.g., `satoshi@getalby.com`).
3.  **[ 🔗 Link an LNbits Wallet Key ]**: For advanced users who already have a wallet *on the app's LNbits instance*.

The backend validates the user's choice and updates their Slack profile, unlocking the relevant features.

### 3. Sending a Zap

The four interfaces (Command, Message Shortcut, App Home GUI, Profile GUI) all lead to the same robust backend logic.

1.  **Initiation & Zap Request:** The user triggers a zap from any interface, fills out the confirmation modal, and clicks "Send".
2.  **Payment Routing:** The backend checks the recipient's profile.
    -   **If Internal:** An instant, free internal transfer is performed on LNbits.
    -   **If External (Lightning Address):** The backend uses the *sender's* internal wallet to pay the recipient's address via the standard LNURL-Pay workflow.
3.  **Zap Receipt:** Upon successful payment, the app posts the threaded, Nostr-style notification confirming the transaction.

## The App Home: Your Dynamic Dashboard

The App Home intelligently adapts to the user's setup:
-   **For New Users:** An onboarding portal with the **[🚀 Get Started]** button.
-   **For Internal Users:** A full-featured dashboard showing a live balance, transaction history, and a **[Top Up Wallet]** button (with optional Alby WebLN integration).
-   **For External Users:** A simple, informative view: "Your zaps are being sent to `user@domain.com`."

## Technical Architecture

1.  **Slack App:** Built with **Bolt for Python/JS**, managing all native Slack UI.
2.  **Backend Service:** Contains all business logic.
    -   Communicates with the Slack API and the central LNbits instance API.
    -   Includes an **LNURL library** (e.g., `lnurl-python`) to handle the request/callback flow for external payments.
    -   Maintains an encrypted database mapping Slack User IDs to either an LNbits Key or a Lightning Address.
3.  **Central LNbits Instance:** Acts as the wallet and payment processor for all internal users.

## Installation & Configuration

(Setup is the same as before, with the addition of an LNURL library.)

### Prerequisites

-   A running LNbits instance with its URL and Admin API Key.
-   A Slack Workspace where you have administrative privileges.
-   Python/Node.js environment with an LNURL library (`pip install lnurl` or equivalent).

## Command & Action Reference

| Feature      | Command / Action                               | Description                                                                    |
| :----------- | :--------------------------------------------- | :----------------------------------------------------------------------------- |
| **Onboarding** | App Home `[🚀 Get Started]` or `/zap-setup`     | Initiates the guided wallet setup process with three choices.                  |
| **Zap**      | `/zap`, Message Shortcut, GUI                  | Intelligently sends a zap, handling internal and external recipients automatically. |
| **Balance**  | `/zap-balance` or App Home view                | Shows balance for internal users; shows linked address for external users.     |
| **Help**     | `/zap-help`                                    | Shows a help message explaining all features and wallet types.                 |

## Security Considerations

-   **Clear User Communication:** The UI must be clear when a user is about to pay a small network fee for an external zap.
-   **LNURL Validation:** The backend must thoroughly validate all steps of the LNURL flow to prevent abuse.
-   All previously mentioned security considerations (database encryption, API key security, request verification) still apply.

## Roadmap & Contributing

Pull requests are welcome!

-   [ ] **UI Enhancements:** Add a small globe 🌐 or lightning ⚡️ icon next to usernames to indicate if they are external or internal.
-   [ ] **Fallback Support:** If an LNURL payment fails, provide a QR code of the invoice directly to the user as a fallback.
-   [ ] **Aggregated Notifications:** Bundle multiple zaps on a single message into one updating notification.
-   [ ] **Leaderboards:** Optional weekly/monthly summaries to celebrate top zappers.
-   [ ] **Custom Zap Emojis:** Allow users to react with a specific emoji (e.g., `:zap:`) to trigger the zap modal.
-   [ ] **Batch Zaps:** Zap multiple users at once from the App Home GUI.
