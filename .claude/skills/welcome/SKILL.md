---
name: welcome
description: "First-time setup wizard for iSCALE + Claude Code. Detects your role, checks MCP connection, and walks you through getting started. Run this when you first clone the repo or are new to iSCALE."
user_invocable: true
allowed_tools:
  - Bash
  - Read
  - Grep
  - Glob
---

# iSCALE Welcome Wizard

Interactive first-time setup for new iSCALE users. This skill detects your situation and walks you through everything step by step.

## When this runs

- User says "get started", "set up", "I'm new", "help me get started", "walk me through"
- User runs `/welcome` explicitly
- First time opening Claude Code in this repo

## Step 1: Welcome & Role Detection

Start by greeting the user and asking their role:

```
Welcome to iSCALE Lead Routing!

I'll help you get set up. First, which best describes your role?

1. **Admin** — I manage the platform (set up verticals, partners, buyers, offers, contracts)
2. **Partner** — I submit leads into iSCALE (affiliate, publisher, lead generator)  
3. **Buyer** — I receive leads from iSCALE (advertiser, client, law firm)

Which one are you? (1, 2, or 3)
```

## Step 2: Check MCP Connection

After role selection, check if the MCP server is configured:

1. Check if `.mcp.json` exists in the project root
2. If it exists, check for an `iscale` entry with `ISCALE_API_URL` and `ISCALE_API_KEY`
3. If not configured, walk them through setup:

```
To connect Claude Code to your iSCALE instance, I need two things:

1. **Your iSCALE URL** — the URL where you access iSCALE
   (e.g., https://leads.iscale.com or https://your-company.iscale.com)

2. **An API key** — you can get one from Settings > API Keys in the admin dashboard
   (it starts with lr_)

Do you have these ready?
```

If they have the info, write the `.mcp.json` config and tell them to restart Claude Code.

If they don't have an API key yet:
- **Admins:** "Go to Settings > API Keys in your admin dashboard and create one."
- **Partners:** "Ask your admin to create a partner API key for you, or go to Settings in your partner portal."
- **Buyers:** "Ask your admin to create a buyer API key for you, or go to Settings in your buyer portal."

## Step 3: Role-Specific Walkthrough

Based on their role, run the appropriate guided walkthrough:

### For Admins → Run the Admin Onboarding

```
Great! You're set up as an admin. Here's what we'll cover:

1. **How iSCALE works** — the big picture (2 min read)
2. **Entity hierarchy** — what connects to what
3. **Your first campaign** — step-by-step setup
4. **Testing** — submit a test lead and verify routing
5. **Day-to-day operations** — reports, troubleshooting, common tasks

Ready? Let's start with how iSCALE works.
```

Then walk through each section interactively:

**Section 1: How iSCALE Works**
```
iSCALE routes leads from partners to buyers. Here's the flow:

  Partner submits lead → Campaign → Offer → Routing Engine → Contract(s) → Buyer(s)

Key concept: the **Offer** is the hub. It connects campaigns (lead sources) 
to contracts (buyer agreements) and controls the distribution rules.

Think of it like an auction house:
- Partners are sellers bringing items (leads)
- Buyers are bidders with specific criteria (contracts)
- The Offer is the auction rules (who wins, how many winners)

Make sense? Say "next" to continue, or ask me anything.
```

**Section 2: Entity Hierarchy**
```
Here's what you need to set up, in order:

  1. Vertical (lead category — "Solar", "Medicare", etc.)
     └── defines what fields leads have
  
  2. Partner (who sends you leads)
     └── Campaign (their specific program, has a posting key)
  
  3. Buyer (who buys leads)
     └── Contract (what they want: filters, caps, price)
  
  4. Offer (connects campaigns ↔ contracts)
     └── distribution rules: exclusive? multisell? highest price wins?

Each entity depends on the one above it. You can't create a campaign 
without a partner, or a contract without a buyer.

Ready to set up your first campaign? Say "next".
```

**Section 3: First Campaign Setup**
```
Let's set up a complete campaign. I'll walk you through each step.

What vertical (lead type) are you working with? 
Examples: Solar, Medicare, Auto Insurance, Mass Tort, Home Services

(If you already have verticals set up, I can check — just say "check my verticals")
```

Then guide them through creating each entity in order, using the MCP tools if connected, or giving them step-by-step UI instructions if not.

**Section 4: Testing**
```
Now let's test the flow. I'll help you:

1. Get the posting spec for your new campaign
2. Submit a test lead
3. Verify it routed correctly
4. Check the routing waterfall to see what happened

Ready to test?
```

**Section 5: Day-to-Day**
```
You're all set! Here are the things you'll do most often:

- "Show me today's leads" — quick overview
- "Why didn't lead L-12345 route?" — troubleshoot routing
- "What's buyer X's balance?" — check billing
- "Pull a revenue report for this week" — reporting
- "Pause contract C-456" — manage entities

You can also run /admin-guide anytime for the full reference.

Any questions?
```

### For Partners → Run the Partner Onboarding

```
Great! You're set up as a partner. Here's what we'll cover:

1. **How lead submission works** — the big picture
2. **Your campaigns** — what's available to you
3. **Posting specs** — exactly what fields to send
4. **Your first test lead** — submit one and see what happens
5. **Day-to-day** — checking leads, payouts, troubleshooting

Ready? Let's go.
```

**Section 1: How Lead Submission Works**
```
You submit leads to iSCALE via API. Here's how it works:

  Your system → POST to iSCALE API → Routing engine finds buyers → You get paid

Each of your campaigns has a **posting key** (starts with lr_). 
This key identifies your campaign and authorizes the submission.

You send a JSON body with the lead's info (name, email, phone, etc.) 
and iSCALE tells you immediately if the lead was accepted or rejected.

Make sense? Say "next" to continue.
```

**Section 2: Your Campaigns**
If MCP is connected, use `list_campaigns` to show their campaigns:
```
Here are your active campaigns:

  Campaign: Solar Leads West Coast (C-123)
  Vertical: Solar
  Posting Key: lr_abc***  
  Caps: 100/day, 500/week
  
  Campaign: Medicare AEP (C-456)
  Vertical: Medicare
  Posting Key: lr_def***
  Caps: 50/day

Want to see the posting spec for one of these?
```

If not connected, tell them where to find this info in the portal.

**Section 3: Posting Spec**
```
Let's look at exactly what fields you need to send. 
Which campaign do you want to integrate first?
```

Then show the full posting spec with required fields, types, and a copy-paste sample request.

**Section 4: First Test Lead**
```
Let's submit a test lead. I'll generate a sample request for you.

⚠️  This will create a REAL lead in the system (with test data).
Your admin can delete it afterward.

Want me to generate the curl command, or do you want to submit via your own system?
```

**Section 5: Day-to-Day**
```
You're all set! Here's what you'll use most:

- "Show me my leads from today" — check submissions
- "Why was my last lead rejected?" — troubleshoot rejections  
- "What's my posting spec for campaign X?" — quick reference
- "Show me my payouts" — check earnings

You can run /partner-guide anytime for the full reference.
```

### For Buyers → Run the Buyer Onboarding

```
Great! You're set up as a buyer. Here's what we'll cover:

1. **How you receive leads** — the big picture
2. **Your contracts** — what's configured for you
3. **Filters & caps** — controlling what you receive
4. **Delivery** — how leads reach your system
5. **Day-to-day** — checking leads, billing, returns

Ready? Let's go.
```

**Section 1: How You Receive Leads**
```
Partners submit leads into iSCALE. The routing engine checks your 
contracts — if a lead matches your criteria, it's delivered to you.

  Lead comes in → Routing engine checks your filters → Match? → Delivered to you

You only pay for leads that pass ALL your checks:
✓ Geographic filters (states/zips you want)
✓ Demographic filters (age range, etc.)  
✓ Custom filters (vertical-specific criteria)
✓ Caps not exceeded (daily/weekly/monthly limits)
✓ Not a duplicate (if dedup is enabled)

Make sense? Say "next" to continue.
```

**Section 2: Your Contracts**
If MCP is connected, use `list_contracts` to show their contracts:
```
Here are your active contracts:

  Contract: Solar CA/TX (CT-123)
  Vertical: Solar
  Price: $15.00/lead
  Geo: CA, TX only
  Caps: 50/day, 200/week
  
  Contract: Medicare National (CT-456)  
  Vertical: Medicare
  Price: $25.00/lead
  Geo: All states
  Caps: 100/day

Want to adjust any of these?
```

**Section 3: Filters & Caps**
```
Your contracts control exactly what leads you receive:

**Geographic filters** — which states/zips
  Example: Only CA, TX, FL leads; or only 902xx zip codes

**Demographic filters** — age range, etc.
  Example: Ages 25-65 only

**Custom filters** — vertical-specific criteria  
  Example: homeOwner = true, utilityBill > $100

**Caps** — volume limits
  Example: Max 50 leads/day, $1000/day spend

**Schedule** — operating hours
  Example: Mon-Fri 9am-5pm Pacific (empty = 24/7)

Want to see or adjust the filters on any of your contracts?
```

**Section 4: Delivery**
```
When a lead matches your contract, it's delivered to your system.

Delivery methods:
- **HTTP POST** — JSON payload to your endpoint (most common)
- **HTTP GET** — data as query parameters  
- **Email** — lead data sent to an email address

Your delivery endpoint and field mapping are configured on your 
contract or intake center. 

Is your delivery endpoint already set up, or do you need help configuring it?
```

**Section 5: Day-to-Day**
```
You're all set! Here's what you'll use most:

- "Show me leads I received today" — check incoming leads
- "What's my balance?" — check billing
- "Return lead L-12345" — return a bad lead
- "Which contracts are hitting caps?" — monitor volume
- "Pause contract CT-456" — stop receiving leads temporarily

You can run /buyer-guide anytime for the full reference.
```

## Conversation Style

Throughout the walkthrough:
- Use plain language, not jargon (explain terms the first time)
- Keep each section short — max 10 lines of text before asking "ready for next?"
- Let the user ask questions at any point
- If MCP is connected, show real data from their instance
- If not connected, give UI directions ("go to Partners > Create")
- Don't dump everything at once — this is a conversation, not a manual
