# Sourcing Bot V3

An experimental automated sourcing assistant for Alibaba, designed to help automate supplier discovery, supplier evaluation, and supplier communication through Telegram.

> ⚠️ **Project Status: Experimental / Active Development**
>
> This project is still under development and should not be considered production-ready. The goal of publishing it is to share the architecture, implementation approach, and lessons learned with the community and receive feedback from other developers working on browser automation, AI agents, Telegram bots, and procurement automation.

---

## Overview

**Sourcing Bot V3** is an automation system designed to assist with the supplier sourcing process on Alibaba.

The system combines:

* Telegram as the user interface
* Browser automation for Alibaba
* Supplier search and filtering
* Supplier scoring and ranking
* Automated supplier conversations
* Session and browser profile management
* Scheduling
* Multi-user support
* AI-assisted reasoning and decision making

The main idea is to allow a user to interact with the system from Telegram instead of manually performing repetitive sourcing tasks through a browser.

For example, instead of manually searching Alibaba, opening dozens of supplier pages, checking supplier information, and contacting suppliers one by one, the bot can coordinate these steps automatically.

---

# Project Goals

The main goal of this project is to explore how far a sourcing workflow can be automated while still maintaining reasonable control and reliability.

The project is focused on solving repetitive procurement tasks such as:

1. Understanding a sourcing request from a user
2. Collecting the required sourcing parameters
3. Searching Alibaba
4. Filtering relevant suppliers
5. Evaluating suppliers
6. Ranking suppliers
7. Contacting suppliers
8. Tracking supplier conversations
9. Handling follow-ups
10. Returning the results to the user through Telegram

The long-term goal is to create a sourcing assistant that can operate with minimal human intervention while still providing mechanisms to prevent dangerous automation behaviour such as duplicate messages, excessive follow-ups, or contacting the wrong supplier.

---

# Example Workflow

A typical workflow looks like this:

```text
User
 │
 │ Telegram request
 ▼
Telegram Bot
 │
 ▼
Intent Detection
 │
 ▼
Sourcing Wizard
 │
 ├── Product
 ├── Quantity
 ├── Target Price
 ├── Destination Country
 └── Other Requirements
 │
 ▼
Sourcing Engine
 │
 ├── Open Alibaba
 ├── Search Product
 ├── Filter Suppliers
 ├── Extract Supplier Data
 ├── Score Suppliers
 └── Rank Suppliers
 │
 ▼
Supplier Communication
 │
 ├── Open Supplier Chat
 ├── Send Initial Message
 ├── Monitor Responses
 └── Handle Follow-ups
 │
 ▼
Results
 │
 ▼
Telegram User
```

---

# Main Features

## Telegram Interface

Telegram is used as the primary user interface.

The goal is to keep the Telegram interface simple enough for a non-technical user.

A user should not need to understand:

* browser automation
* Playwright/Selenium-style concepts
* browser contexts
* sessions
* API calls
* AI models
* selectors
* automation workers

The technical processes should happen in the background.

---

## Sourcing Wizard

The bot can collect sourcing requirements through a conversational wizard.

The wizard is intended to collect information such as:

```text
Product:
Quantity:
Target price:
Destination:
Supplier country:
Additional requirements:
```

Instead of requiring users to type a complex command, the bot can guide them through the required information.

---

## Alibaba Automation

The system contains a browser automation layer responsible for interacting with Alibaba.

The automation layer is separated from the Telegram layer so that changes to the browser automation do not necessarily require rewriting the entire Telegram interface.

---

## Supplier Discovery

The sourcing engine can search for suppliers based on the user's requirements.

Potential supplier information can include:

* Supplier name
* Product relevance
* Supplier location
* Supplier experience
* Available products
* Supplier rating
* Transaction information
* Other available supplier metadata

The exact information depends on what Alibaba exposes to the browser automation layer.

---

## Supplier Scoring

The project includes a supplier evaluation concept intended to rank potential suppliers.

A simplified example:

```text
Supplier Score
│
├── Product relevance
├── Supplier quality
├── Experience
├── Price
├── Trade history
├── Response characteristics
└── Other available signals
```

The scoring system is still subject to improvement.

One of the goals of publishing this project is to get feedback on better ways to design supplier ranking systems.

---

# Supplier Communication

One of the more challenging parts of the project is automated supplier communication.

The system is intended to:

1. Identify the correct supplier conversation
2. Open the supplier chat
3. Send the correct sourcing message
4. Monitor the conversation
5. Detect supplier replies
6. Decide whether a response is required
7. Continue the conversation when appropriate

This area requires particularly strong safeguards because an error can result in:

* duplicate messages
* unnecessary follow-ups
* contacting the wrong supplier
* sending messages to unrelated conversations
* excessive automation

For this reason, communication logic is treated as a higher-risk part of the system than ordinary data extraction.

---

# Architecture

The project is organized into several major components.

```text
Sourcing Bot V3
│
├── source.mjs
│
├── config/
│
├── core/
│
├── telegram/
│
└── bot/
```

---

# Directory Structure

```text
Sourcing Bot V3/
│
├── source.mjs
│
├── config/
│   ├── users
│   ├── shifts.json
│   └── browser-profiles.json
│
├── core/
│   ├── browser-pool.js
│   ├── context-factory.js
│   ├── fingerprint.js
│   └── session-store.js
│
├── telegram/
│   ├── telegram-bot.js
│   ├── telegram-admin.js
│   ├── telegram-api.js
│   ├── telegram-commands.js
│   ├── telegram-templates.js
│   ├── telegram-menu.js
│   ├── telegram-intent.js
│   └── telegram-wizard.js
│
└── bot/
    └── Alibaba sourcing logic
```

> The directory structure above reflects the current architecture and may change as the project evolves.

---

# Core Components

## `source.mjs`

The main entry point of the application.

Responsibilities include:

* starting the bot
* initializing services
* managing users
* starting scheduled jobs
* coordinating different components
* handling the overall application lifecycle

---

## `config/`

Contains configuration related to users, scheduling, and browser profiles.

Example configuration areas include:

```text
users
shifts
browser profiles
automation settings
```

### `shifts.json`

Used for scheduling automation activity.

This can be useful for controlling when automated tasks are allowed to run.

---

## `core/`

The `core` directory contains infrastructure used by the automation engine.

### `browser-pool.js`

Responsible for managing browser instances.

The current architecture is designed around multiple browser instances rather than creating an unlimited number of browsers for every task.

The goal is to:

* reduce resource usage
* reuse browser instances
* isolate workloads
* control concurrency

---

### `context-factory.js`

Responsible for creating browser contexts.

Browser contexts provide a layer of isolation between sessions and tasks.

---

### `fingerprint.js`

Contains logic related to browser fingerprint configuration.

This component exists because browser automation environments can behave differently from ordinary browser sessions.

This area is still experimental and should be reviewed carefully before being used in a production environment.

---

### `session-store.js`

Responsible for storing and managing browser session information.

The goal is to reduce the need to repeatedly authenticate users and maintain persistent browsing sessions where possible.

---

# Telegram Layer

The `telegram/` directory contains the user-facing communication system.

---

## `telegram-bot.js`

Main Telegram bot implementation.

It handles interaction with end users and acts as the primary interface to the sourcing engine.

---

## `telegram-admin.js`

Administrative functionality.

This layer is intended to provide additional controls for managing the bot and monitoring system activity.

---

## `telegram-api.js`

Handles Telegram API communication.

This provides the abstraction layer between the application and Telegram.

---

## `telegram-commands.js`

Contains command handling logic.

Examples could include:

```text
/start
/help
/source
/status
/cancel
```

---

## `telegram-templates.js`

Contains reusable message templates.

The goal is to avoid duplicating message content throughout the application.

---

## `telegram-menu.js`

Provides menu-based interaction.

This helps reduce the need for users to remember commands.

---

## `telegram-intent.js`

Responsible for interpreting user intent.

The goal is to transform natural-language messages into structured actions.

For example:

```text
"Find 500 pcs of stainless steel bottles from China"
```

could eventually become something similar to:

```json
{
  "intent": "sourcing_request",
  "product": "stainless steel bottle",
  "quantity": 500,
  "supplier_country": "China"
}
```

The exact implementation may evolve.

---

## `telegram-wizard.js`

Responsible for collecting missing information from users.

For example:

```text
User:
I need 500 bottles.

Bot:
What type of bottle?

User:
Stainless steel.

Bot:
What is your target price?

...
```

This approach allows the system to handle incomplete sourcing requests.

---

# Alibaba Bot Layer

The `bot/` directory contains the core Alibaba sourcing logic.

This layer is intentionally separated from Telegram.

The reason is simple:

```text
Telegram
   ↓
Application Logic
   ↓
Sourcing Engine
   ↓
Alibaba Automation
```

This separation makes it easier to replace the interface in the future.

For example, Telegram could eventually be replaced or supplemented with:

* Web UI
* Discord
* WhatsApp
* Internal company dashboard
* API

without rebuilding the entire sourcing engine.

---

# Multi-User Architecture

The project was designed with multi-user operation in mind.

The architecture allows different users to have their own:

* sessions
* sourcing requests
* browser contexts
* configurations
* activity

The exact level of isolation is still an active development area.

---

# Browser Management

Browser management is one of the most important parts of this project.

Instead of launching a completely new browser process for every operation, the system can maintain a pool of browser instances.

Conceptually:

```text
Browser Pool
│
├── Browser 1
│   ├── User A
│   └── User B
│
├── Browser 2
│   ├── User C
│   └── User D
│
└── Browser 3
    ├── User E
    └── User F
```

The exact isolation strategy is still being evaluated.

---

# Automation Scheduling

Automation should not necessarily run continuously.

The project includes scheduling functionality that can be used to define permitted working periods.

Example:

```text
09:00 - 12:00
13:00 - 17:00
```

This allows the automation engine to respect operating schedules.

---

# Safety and Automation Controls

One of the biggest lessons from developing this project is that **automation without strong state management can produce unintended behaviour**.

For example, an automation system may accidentally:

* process an unrelated conversation
* send duplicate messages
* repeatedly follow up with the same supplier
* continue a conversation after the user has requested it to stop
* send a message to the wrong conversation

Therefore, the project should eventually implement stronger safeguards such as:

```text
Conversation State
        ↓
Supplier Identity
        ↓
Message State
        ↓
Last Action
        ↓
Cooldown
        ↓
Follow-up Limit
        ↓
Stop Condition
```

The communication system should always know:

```text
WHO am I talking to?
WHY am I talking to them?
WHAT was the last action?
WHEN was the last action?
SHOULD I send another message?
HAS THE USER OR SUPPLIER REQUESTED STOP?
```

---

# Important Design Principle

The most important design principle of this project is:

> **Automation should be state-aware, not message-driven.**

A bot should not simply see a message and decide:

```text
message detected → send reply
```

Instead, it should understand the state of the conversation:

```text
Conversation
    ↓
Identify supplier
    ↓
Identify sourcing task
    ↓
Load conversation state
    ↓
Check previous actions
    ↓
Check cooldown
    ↓
Check stop conditions
    ↓
Decide whether action is allowed
    ↓
Execute action
    ↓
Store resulting state
```

This is an important area where community feedback would be valuable.

---

# Current Development Challenges

This project is still experimental, and several areas require additional work.

## 1. Conversation State

Maintaining reliable conversation state is difficult.

The system needs to distinguish between:

* active supplier conversations
* historical conversations
* unrelated conversations
* unread messages
* conversations requiring action
* conversations that should no longer be contacted

---

## 2. Duplicate Messages

Automation can accidentally send the same message multiple times if state is not persisted correctly.

A future implementation should ensure that every outbound action has an identifiable state.

For example:

```text
message_id
supplier_id
conversation_id
task_id
message_type
timestamp
status
```

---

## 3. Follow-Up Control

Follow-up automation requires strict limits.

A system should not continuously send:

```text
Follow-up #1
Follow-up #2
Follow-up #3
...
Follow-up #44
```

without verifying whether the supplier has already replied or requested that communication stop.

Future versions should have explicit limits such as:

```text
maximum follow-ups
minimum interval
maximum total messages
stop keywords
supplier response detection
manual override
```

---

## 4. Supplier Identity

Supplier identification is critical.

The system must not assume that:

```text
open chat
=
correct supplier
```

A robust implementation should verify the supplier using multiple signals where possible.

---

## 5. Login and CAPTCHA

Browser automation on commercial websites can encounter:

* login challenges
* CAPTCHA
* expired sessions
* verification steps
* browser changes
* unexpected UI changes

These situations cannot always be solved reliably through automation.

The architecture therefore needs a way to pause the automation and allow human intervention when necessary.

---

# Human-in-the-Loop

Although the long-term goal is high automation, some situations should remain capable of being handed back to a human.

For example:

```text
Automation
     ↓
Unexpected login challenge
     ↓
Pause
     ↓
Notify operator
     ↓
Human resolves issue
     ↓
Resume automation
```

This is preferable to allowing the system to continue blindly.

---

# Error Handling

Automation failures should be treated as expected events rather than exceptional events.

Examples include:

```text
Browser crashed
Session expired
Element not found
Supplier page changed
Telegram API timeout
Alibaba unavailable
Network failure
Unexpected popup
CAPTCHA
Login required
```

The system should ideally:

```text
Detect
   ↓
Log
   ↓
Retry when safe
   ↓
Escalate when unsafe
```

---

# Logging

Meaningful logs are essential for debugging automation.

Useful events include:

```text
[INFO] Starting sourcing task
[INFO] Searching Alibaba
[INFO] Supplier found
[INFO] Supplier ranked
[INFO] Opening conversation
[INFO] Message sent
[INFO] Supplier replied
[WARN] Session expired
[WARN] Duplicate action prevented
[ERROR] Browser context crashed
```

Logs should help developers determine exactly what happened before an unwanted action occurred.

---

# Security

This project may handle sensitive information such as:

* Telegram credentials
* browser sessions
* supplier conversations
* sourcing requirements
* business information
* API credentials

Therefore:

**Never commit credentials to GitHub.**

Do not commit:

```text
.env
session files
cookies
browser profiles
Telegram tokens
API keys
passwords
private credentials
```

A `.gitignore` file should be used to prevent accidental commits.

Example:

```gitignore
.env
.env.*
node_modules/
logs/
sessions/
profiles/
cookies/
*.sqlite
*.db
secrets/
```

The exact rules should be adjusted according to the actual project structure.

---

# Environment Variables

Sensitive configuration should be provided through environment variables.

Example:

```env
TELEGRAM_BOT_TOKEN=
ALIBABA_USERNAME=
ALIBABA_PASSWORD=
AI_API_KEY=
```

Do not place real credentials directly inside source files.

---

# Installation

> Installation instructions below are a starting point and should be updated after the current project source is reviewed.

Clone the repository:

```bash
git clone https://github.com/USERNAME/REPOSITORY.git
cd REPOSITORY
```

Install dependencies:

```bash
npm install
```

Create the environment file:

```bash
cp .env.example .env
```

Edit `.env`:

```env
TELEGRAM_BOT_TOKEN=your_token
```

Then start the application:

```bash
npm start
```

Development mode:

```bash
npm run dev
```

The exact commands depend on the final `package.json`.

---

# Configuration

Before running the bot, configuration may need to be provided for:

```text
Telegram
Alibaba account
Browser profiles
User configuration
Scheduling
AI/API providers
```

Example conceptual structure:

```text
config/
│
├── users
├── shifts.json
└── browser-profiles.json
```

---

# AI Integration

AI can be used for higher-level reasoning rather than replacing deterministic application logic.

A useful architecture is:

```text
AI
 ↓
Reasoning / Interpretation
 ↓
Structured Decision
 ↓
Application Validation
 ↓
Browser Automation
```

AI should not necessarily have unrestricted control over browser actions.

Instead, the application should validate critical actions before execution.

For example:

```text
AI decides:
"Send supplier follow-up"

        ↓

Application checks:
- Correct supplier?
- Correct conversation?
- Previous message?
- Cooldown?
- Follow-up limit?
- Stop condition?

        ↓

Allowed / Blocked
```

This separation can significantly improve reliability.

---

# Recommended Architecture

The project can eventually evolve into a layered architecture:

```text
┌──────────────────────────┐
│ Telegram Interface       │
└────────────┬─────────────┘
             │
┌────────────▼─────────────┐
│ Task / Workflow Manager  │
└────────────┬─────────────┘
             │
┌────────────▼─────────────┐
│ AI Reasoning Layer       │
└────────────┬─────────────┘
             │
┌────────────▼─────────────┐
│ Safety / Policy Layer    │
└────────────┬─────────────┘
             │
┌────────────▼─────────────┐
│ Browser Automation       │
└────────────┬─────────────┘
             │
┌────────────▼─────────────┐
│ Alibaba                  │
└──────────────────────────┘
```

The key principle is that AI should not bypass application-level safety checks.

---

# Why This Project Is Public

This repository is being published primarily to exchange ideas with the community.

The project is not presented as a finished commercial product.

I am particularly interested in feedback regarding:

* browser automation architecture
* supplier scoring
* conversation state management
* multi-agent architecture
* AI integration
* automation safety
* session management
* concurrency
* Telegram bot architecture
* anti-duplicate mechanisms
* scheduling
* error recovery
* human-in-the-loop design

---

# Known Problems

There are still known areas that need improvement.

Examples include:

```text
- Conversation state can be improved
- Supplier chat detection needs stronger validation
- Follow-up logic requires additional safeguards
- Browser session recovery can be improved
- Alibaba UI changes can break selectors
- Some automation tasks may still require human intervention
- AI decisions require stronger validation
- Logging and observability can be expanded
```

These limitations are intentionally documented instead of hidden because this repository is also a development and learning project.

---

# Roadmap

## Phase 1 — Stability

* Improve browser stability
* Improve session persistence
* Improve Telegram reliability
* Improve error handling
* Improve logging

## Phase 2 — Conversation Safety

* Strong conversation state
* Supplier identity verification
* Duplicate message prevention
* Follow-up cooldown
* Maximum follow-up limits
* Stop-condition detection
* Manual override

## Phase 3 — Sourcing Intelligence

* Better supplier scoring
* Better supplier filtering
* More reliable product matching
* Price analysis
* Supplier comparison

## Phase 4 — AI Agent Architecture

Potential future architecture:

```text
                    Maestro / Orchestrator
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
        Reasoning       Browser Worker   Telegram
           Agent            Agent          Agent
             │              │
             └───────┬──────┘
                     ▼
               Sourcing Engine
```

The exact architecture is still being explored.

---

# Contributing

Contributions, suggestions, architecture discussions, and bug reports are welcome.

Before submitting a pull request, please consider:

1. Keep changes focused.
2. Avoid committing credentials.
3. Add useful logs for automation changes.
4. Explain why the change is necessary.
5. Test automation carefully before enabling it against a real account.
6. Consider failure scenarios, not only the successful path.

---

# Reporting Bugs

When reporting a bug, please include:

```text
Operating system:
Node.js version:
Browser version:
Bot version:
Relevant configuration:
What happened:
What was expected:
Relevant logs:
Steps to reproduce:
```

Please remove credentials and private business information before posting logs.

---

# Disclaimer

This project is intended for educational, research, and automation-development purposes.

Automating interactions with third-party websites may be subject to those websites' terms of service, technical restrictions, account policies, and applicable laws.

Users are responsible for ensuring that their use of this software complies with the policies and laws applicable to them.

The project author is not responsible for account restrictions, data loss, unintended messages, or other consequences resulting from the use of the software.

---

# Project Philosophy

The main philosophy behind this project is:

> **Automate repetitive work, but never assume automation is correct simply because it executed successfully.**

A successful browser action does not necessarily mean a successful business action.

For example:

```text
"Message sent successfully"
```

does not necessarily mean:

```text
"Correct supplier contacted successfully"
```

Similarly:

```text
"Follow-up sent successfully"
```

does not necessarily mean:

```text
"Follow-up was appropriate"
```

The system therefore needs to care about **context, state, identity, timing, and intent**, not simply whether a browser command succeeded.

---

# Current Status

This project is currently under active development.

Expect:

* architectural changes
* bugs
* incomplete features
* experimental components
* breaking changes
* changes to configuration formats

The repository should therefore be considered a development project rather than a stable release.

---

# Feedback Wanted

I am particularly interested in hearing from developers who have experience with:

* browser automation
* Playwright
* Telegram bots
* AI agents
* multi-agent systems
* procurement automation
* workflow engines
* distributed systems
* session management
* state machines
* web automation reliability

Questions and suggestions are welcome.

For example:

> How would you design the conversation state system to guarantee that the bot never sends a follow-up after a supplier has asked it to stop?

Or:

> What architecture would you recommend for combining AI reasoning with deterministic browser automation while preventing unsafe actions?

---

# License

Add your preferred license here.

For example:

```text
MIT License
```

or another license appropriate for your intended use.

---

# Final Note

This repository is not meant to demonstrate a perfect automation system.

It documents an ongoing attempt to build a practical sourcing automation platform and to learn from real-world problems encountered during development.

The most valuable part of the project may not be the current implementation itself, but the engineering problems encountered while trying to make browser automation, AI reasoning, Telegram, and sourcing workflows operate reliably together.

Community feedback and alternative architectural approaches are highly welcome.

