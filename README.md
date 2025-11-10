📱 iCloud → Zoho CRM Contact Sync

(iOS Shortcut + Cloudflare Worker — No-Code Automation)

Built in just two days by someone with zero coding experience, this project automatically syncs new iPhone contacts to your CRM — using nothing but an iOS Shortcut, a free Cloudflare Worker, and a few minutes of ChatGPT guidance.

✨ What It Does

Finds iOS contacts created in the last 24–48 hours whose Notes don’t contain [ZOHO_SYNCED]

Sends them securely to your Cloudflare Worker

Worker talks to the Zoho CRM API and creates or updates matching contacts

On success, the Shortcut:
✅ Appends [ZOHO_SYNCED] to the Notes field
✅ Increments a counter
✅ Shows a notification like: 3 contact(s) to Zoho.

No duplicate contacts. No manual imports. Just… magic. ✨

(You can adapt this for any CRM by adjusting the Worker’s API fields.)

🧩 How It Works

This system has two lightweight parts:

Component	Purpose
🧠 Cloudflare Worker	Receives contact data and sends it to Zoho CRM via API
🔁 iOS Shortcut “Sync New Contacts”	Finds unsynced contacts and calls the Worker
🪄 Requirements

iPhone or iPad with the Shortcuts app

Zoho CRM account + API credentials

Cloudflare account (Workers are free)

15 minutes of setup time

🚀 Setup
1️⃣ Deploy the Cloudflare Worker

Go to your Cloudflare Dashboard → Workers & Pages → Create Application → Worker.

Create a new Worker named something like icloud-zoho-sync.

Paste in the contents of worker.js from this repo.

Click Settings → Variables and add:

Variable	Value
ZOHO_REFRESH_TOKEN	Your Zoho OAuth refresh token
ZOHO_CLIENT_ID	Your Zoho OAuth client ID
ZOHO_CLIENT_SECRET	Your Zoho OAuth client secret
ZOHO_ACCOUNTS_DOMAIN	(optional) https://accounts.zoho.eu, .in, etc.
ZOHO_API_DOMAIN	(optional) https://www.zohoapis.eu, .in, etc.

Click Save and Deploy.

Copy the Worker URL — you’ll paste this into your Shortcut next.

2️⃣ Install the iOS Shortcut

Download Sync_New_Contacts.shortcut from this repo to your iPhone.

Open it in the Shortcuts app → tap the ⋯ (edit) icon.

Find the “Get contents of URL” action.

Replace the placeholder URL:

https://YOUR-WORKER-URL-HERE.example

with your actual Worker URL from Cloudflare.

Confirm that in that same action:

Method = POST

Headers include: Content-Type : application/json

Request Body = JSON, sending fields like:

firstName

lastName

email

phone

company

At the top of the Shortcut, confirm the filter is roughly:

Creation Date is in the last 24–48 hours

Notes does not contain [ZOHO_SYNCED]

Tap Done to save.

💡 Security Note:
The Shortcut file you downloaded is “sterilized” — it does not include anyone else’s Worker URL or secrets. You’ll paste your own Worker URL during setup.

3️⃣ Automate the Sync (So You Don’t Have to Tap It)

Once your Shortcut runs correctly, set up an iOS Automation to run it automatically:

Open Shortcuts → Automation → + → Create Personal Automation.

Choose Time of Day.

Select a time (for example, 8:00 PM) → Repeat Daily.

Tap Next → Add Action → Run Shortcut → choose “Sync New Contacts.”

Tap Next, turn Ask Before Running → OFF, then Don’t Ask → Done.

✅ Done! Your iPhone will now sync new contacts to Zoho every day in the background.
You’ll see a small notification like:

2 contact(s) to Zoho.

🧠 Optional bonus: You can also create a second automation:

When a Contact is Added → Run Shortcut → Sync New Contacts

for near-instant syncing whenever you add someone new.

🔐 What You Can Change in the Worker

Most people don’t need to edit the code at all — just set environment variables in Cloudflare.
But here’s what’s tweakable if you’re curious:

Part	Purpose	Example Change
duplicate_check_fields: ["Email"]	Defines what counts as a duplicate	Use ["Phone"] or both
zohoContact object	Maps fields from iPhone → Zoho	Add address, notes, etc.
/crm/v2/Contacts/upsert	Target Zoho module	Change to /Leads/upsert

If you change your CRM or use custom fields, you’ll adjust the mapping in this part of worker.js:

const zohoContact = {
  First_Name: contact.firstName || "",
  Last_Name: contact.lastName || "Unknown",
  Email: contact.email || "",
  Phone: contact.phone || "",
  Mobile: contact.mobile || "",
  Account_Name: contact.company || undefined,
  Title: contact.title || undefined,
};

🧭 Troubleshooting & Customization (with ChatGPT)

If you hit an error or want to adapt this for a different CRM, you can paste this prompt into ChatGPT:

I'm using Cristin's iCloud → Zoho CRM Contact Sync project from GitHub.
My goal is to sync new iPhone contacts to my CRM and tag them in Notes as [ZOHO_SYNCED].
Here’s my current Shortcut setup and Worker code:
[paste screenshots or code]
Please help me debug or customize it. I am not a developer.

ChatGPT can then walk you through setup and debugging step-by-step — exactly how this project was built.

💬 Project Summary (for GitHub description)

Built in two days by someone with zero coding experience, this project connects iPhone Contacts to Zoho CRM using an iOS Shortcut and Cloudflare Worker. It automatically syncs new contacts, prevents duplicates with note tagging, and shows what’s possible with ChatGPT-guided no-code development.

🧡 Credits & Inspiration

Created by Cristin Padgett — proof that anyone can build useful automations with a bit of curiosity and a conversational AI co-pilot.
