**Newsletter Digest — Automated Daily Briefing with n8n + Claude**
A personal AI digest workflow that runs daily, pulls from your newsletter subscriptions in Gmail, and delivers a single filtered email summary — so you read one email instead of five.

**The Problem**
I subscribed to several high-quality AI newsletters but never actually read them. I needed one filtered signal, not five full emails.

**What It Does**
Every day at 6pm, this workflow:

- Fetches the latest newsletters from Gmail
- Combines and cleans the raw email content
- Passes it to Claude (Anthropic API) with a context-rich prompt tuned to my specific role and goals
- Delivers a structured HTML digest back to my inbox

The digest is organized into four sections: things to build/teach/post, things to know, interest in higher ed AI, and an edge of the day — one concrete thing I can act on immediately.

**Stack**

- n8n (self-hosted on Render) — scheduling, Gmail fetch, email delivery
- Anthropic API (claude-sonnet-4-20250514) — summarization and filtering
- Gmail OAuth2 — fetching newsletters and sending the digest

**Nodes**
Schedule Trigger (6pm daily)
  → Gmail: Get Messages (newer_than:2d, simple=false)
  → Code: Combine + clean email bodies
  → HTTP Request: Anthropic API
  → Gmail: Send HTML digest

**Key Technical Decisions**
simple: false on the Gmail node — required to access the full email text field. When simple is on, you only get a snippet.
newer_than:2d instead of 1d — accounts for UTC vs local timezone mismatch in n8n's Schedule Trigger. Using 1d was causing emails to get missed.
JSON.stringify() around the prompt — prevents special characters in email content from breaking the JSON payload to the Anthropic API.
HTML conversion order — markdown links must be converted to <a> tags before newlines are converted to <br>. Reversing this order breaks link rendering.

**Setup**

Import the workflow JSON into your n8n instance
Connect your Gmail OAuth2 credential
Add your Anthropic API key as a Header Auth credential (x-api-key)
Update the Gmail filter with your newsletter sender addresses
Update the prompt with your own context and priorities
Set your Schedule Trigger timezone to your local timezone

**Customization**
The prompt is the most important part. Tune it to your role, your signal priorities, and how you want the output structured. The more specific your context, the more useful the digest.
