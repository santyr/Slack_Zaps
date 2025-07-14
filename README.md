# Slack_Zaps

Slack Zaps: Nostr Zaps for the Slack Workspace

![alt text](https://img.shields.io/badge/build-passing-brightgreen)


![alt text](https://img.shields.io/badge/license-MIT-blue)

A powerful, deeply integrated Slack application that allows users to send and receive instant, fee-less Bitcoin Lightning "zaps" as a form of appreciation and reward.

This project leverages a centralized LNbits instance to create a seamless user experience, making the act of showing appreciation as easy as reacting with an emoji.

Table of Contents

Vision & Philosophy

Core Features

The User Journey

One-Time Admin Setup

Onboarding a New User

Sending a Zap: The Four Methods

The App Home: Your Central Dashboard

Technical Architecture

Installation & Configuration

Prerequisites

Setup Steps

Command & Action Reference

Security Considerations

Roadmap & Contributing

Vision & Philosophy

The goal is to foster a culture of recognition by removing all friction from peer-to-peer value transfer. Our philosophy is guided by two principles:

Context and Choice: Meet users where they are. A user should be able to send a zap from anywhere they think of doing it—reacting to a message, looking at a user's profile, or firing off a command.

Instant and Free: By using a single LNbits instance for internal ledger updates, all transactions are instantaneous and incur zero network fees.

Core Features

⚡ Four Ways to Zap: Use /zap, a message shortcut, the App Home GUI, or a button on a user's profile.

🏦 Centralized LNbits Backend: All payments are instant and fee-less internal transfers.

✨ Seamless Onboarding: A guided /zap-setup command creates or links a wallet and automatically updates the user's Slack profile.

🏠 App Home Dashboard: A central hub to view your balance, see transaction history, and top up your wallet.

🔗 Custom Profile Integration: Onboard by editing your profile and zap users directly from their profile pop-up.

🧠 Smart Notifications: The app intelligently invites new users and can aggregate multiple zaps into a single notification thread to reduce noise.

The User Journey
1. One-Time Admin Setup

Before the app can be used, a Slack Workspace Admin must perform a crucial one-time setup:

Navigate to Workspace Settings > Customize > People > Profile fields.

Click "Add field".

Create a Text field named "LNbits Read Key" (or similar).

Important: Note the Field ID that Slack generates (e.g., Xf0...). You will need this for the .env configuration file.

2. Onboarding a New User

The user experience begins with a simple, guided setup process.

Initiation: The user types the /zap-setup command.

Choice: The app presents a private, interactive choice: [ Create a New Wallet ] or [ Link an Existing Wallet ].

Backend Magic:

The backend creates or validates the user's LNbits wallet via the core LNbits API.

It securely stores the mapping of Slack User ID -> LNbits Wallet Key in its encrypted database.

It then uses the Slack API to write the LNbits Read Key into the custom profile field created by the admin.

Completion: The user is notified that their setup is complete.

3. Sending a Zap: The Four Methods

Once onboarded, users have four intuitive ways to send a zap.

Method	User Action	Use Case & Advantage
Slash Command	/zap @user <amt> <msg>	Speed & Power. Ideal for power users and for sending zaps not tied to a specific message.
Message Shortcut	Hover on message (...) -> ⚡️ Zap this message	Context. The most intuitive way to reward valuable content. The notification appears in the message's thread.
App Home GUI	Go to App Home -> Click [Send a Zap]	Visual. Perfect for users who prefer a graphical interface over commands.
Profile GUI	Click a user's profile -> View details in App -> [⚡️ Zap User]	Discoverability. The most native-feeling method, allowing users to zap directly from a profile card.
The App Home: Your Central Dashboard

The App Home tab is the user's central hub for all zap-related activity. It features:

Live Balance: Displays the current LNbits wallet balance in sats.

Send a Zap Button: The primary call-to-action for the GUI zap flow.

Recent Transactions: A list of the latest zaps sent and received.

Top Up Wallet Button: Generates a new Lightning invoice from the user's wallet for easy funding.

Manage Wallet Link: A direct link to the user's wallet on the LNbits web interface.

Technical Architecture

The system consists of three distinct components:

Slack App: Built with a framework like Bolt for Python/JS. Manages all user-facing interactions (modals, commands, shortcuts).

Backend Service: A web service (e.g., FastAPI, Express) that contains all business logic. It communicates with both Slack and LNbits and stores user mappings in an encrypted database.

Central LNbits Instance: A single, self-hosted or managed LNbits server that acts as the ledger for all internal transactions. The backend communicates with it via the core LNbits API (specifically POST /api/v1/users for wallet creation).

Installation & Configuration
Prerequisites

A running LNbits instance with its URL and Admin API Key.

A Slack Workspace where you have administrative privileges.

Node.js or Python environment for the backend service.

Setup Steps

Clone the Repository:

Generated sh
git clone https://github.com/your-username/slack-zaps.git
cd slack-zaps


Install Dependencies:

Generated sh
# For Node.js
npm install

# For Python
pip install -r requirements.txt
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Sh
IGNORE_WHEN_COPYING_END

Configure Environment Variables:
Create a .env file in the root directory and populate it with the following:

Generated env
# Slack App Credentials
SLACK_BOT_TOKEN="xoxb-..."
SLACK_SIGNING_SECRET="..."

# LNbits Configuration
LNBITS_URL="https://legend.lnbits.com" # Replace with your instance URL
LNBITS_ADMIN_KEY="..." # The admin key for your LNbits instance

# Slack Profile Field ID
SLACK_PROFILE_FIELD_ID="Xf0..." # The ID of the custom field you created
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Env
IGNORE_WHEN_COPYING_END

Configure the Slack App:

Go to api.slack.com/apps and create a new app.

Enable Socket Mode under "Settings".

OAuth & Permissions: Add the following scopes:

chat:write

commands

users:read

users.profile:write

Slash Commands: Configure the /zap, /zap-setup, /zap-balance, and /zap-help commands, pointing them to your app.

Interactivity & Shortcuts:

Enable Interactivity.

Under "Message Shortcuts", create a new shortcut named "⚡️ Zap this message".

App Home: Enable the App Home feature.

Run the Application:

Generated sh
# For Node.js
npm start

# For Python
python app.py
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Sh
IGNORE_WHEN_COPYING_END
Command & Action Reference
Feature	Command / Action	Description
Setup	/zap-setup	Initiates the guided onboarding process to create or link a wallet.
Send Zap	/zap @user <amt> <msg>	Sends a specified amount of sats to a user.
Send Zap	(...) -> ⚡️ Zap this message	Opens a modal to zap the author of the selected message.
Send Zap	App Home [Send a Zap]	Opens a GUI modal to select a recipient and send a zap.
Send Zap	Profile [⚡️ Zap User]	Opens a GUI modal to zap the selected user.
Balance	/zap-balance	Displays your current wallet balance in a private message.
Help	/zap-help	Shows a help message detailing all available commands and features.
Security Considerations

Database Encryption: The database storing the Slack ID <-> LNbits Key mapping MUST be encrypted at rest.

Environment Variables: Never commit the .env file to version control. Use a secrets management system for production.

Request Verification: The backend must verify the cryptographic signature of all incoming requests from Slack to prevent forged requests.

API Key Scopes: The LNbits Admin Key is highly sensitive. Ensure it is stored securely and network access to your LNbits instance is restricted.

Roadmap & Contributing

This project provides a robust foundation. Pull requests are welcome! Future enhancements could include:

Aggregated Notifications: Bundle multiple zaps on a single message into one updating notification.

Leaderboards: Optional weekly/monthly summaries to celebrate top zappers.

Custom Zap Emojis: Allow users to react with a specific emoji (e.g., :zap:) to trigger the zap modal.

Batch Zaps: Zap multiple users at once from the App Home GUI.
