# 📲 iPhone → Zoho CRM Contact Sync  
*(Built with iOS Shortcuts + Cloudflare Worker)*

This project lets you automatically sync new iPhone contacts to Zoho CRM using:

- An **iOS Shortcut** (`Sync New Contacts.Template`)
- A **Cloudflare Worker** that receives the contact and talks to the Zoho API
- A small marker (`[ZOHO_SYNCED]`) saved in the iOS contact’s **Notes** so you don’t get duplicates
- A **shared secret** 🔐 to securely lock down the Worker so only *your* Shortcut can call it

> You can swap Zoho for another CRM later by changing the Worker code and field names.

---

## 🧠 Step 0 – Start with ChatGPT (highly recommended)

Most people using this will **not** be developers (I wasn’t either 😊).

Before doing anything else, open ChatGPT and paste this:

> I want to set up Cristin’s **iPhone → Zoho CRM Contact Sync** Shortcut from GitHub.  
> My goal is to sync new iPhone contacts to my CRM and mark them as `[CRM_SYNCED]` in the Notes field.  
> Here is the repo link:  
> https://github.com/Avant-Garde-Haus/icloud-zoho-contact-sync-shortcut  
> Please walk me through:  
> 1️⃣ Creating and configuring the Cloudflare Worker  
> 2️⃣ Importing and editing the Shortcut on my iPhone  
> 3️⃣ Setting up the automation so it runs every day  
> I have almost zero coding experience. Please talk to me like I’m new to this.

ChatGPT will then act as your “live coach” and walk you through everything step-by-step.

Everything else below is for the nerds. 😝

---

## 🧩 What this setup does

Once everything is configured:

- It finds iPhone contacts created in roughly the last **24–48 hours** whose **Notes** do **not** contain `[ZOHO_SYNCED]`.
- It sends each of those contacts to your **Cloudflare Worker**, including:
  - `firstName`
  - `lastName`
  - `phone`
  - `email`
  - `company`
- The Shortcut also sends a **secret header** (`x-sync-secret`) so only your Shortcut can use the Worker.
- If the Worker responds with:  
  ```json
  {"message":"Contact synced successfully","status":"SUCCESS","ok":true}
then the Shortcut:

Appends [ZOHO_SYNCED] to the contact’s Notes on your phone

Increases a counter and shows a notification like:
1 contact(s) to Zoho.

You can change the time window (24h / 48h) and the note tag later if you like.

## 🪄 What you need

You’ll need:

An iPhone or iPad with the Shortcuts app

A Zoho CRM account with API access (or another CRM if you adapt the Worker)

A Cloudflare account (free is fine) to run the Worker

## 🛠 Setup overview
1️⃣ 🚀 Create and secure the Cloudflare Worker

With ChatGPT’s help, you’ll:

Go to your Cloudflare Dashboard → Workers & Pages → Create Application → Worker

Create a new Worker named something like icloud-zoho-sync

Paste in the contents of worker.js from this repo (the security-hardened version)

Click Settings → Variables and add these Environment Variables:

Variable	Value / Description
ZOHO_REFRESH_TOKEN	Your Zoho OAuth refresh token
ZOHO_CLIENT_ID	Your Zoho OAuth client ID
ZOHO_CLIENT_SECRET	Your Zoho OAuth client secret
ZOHO_ACCOUNTS_DOMAIN	(optional) e.g. https://accounts.zoho.eu, .in, etc.
ZOHO_API_DOMAIN	(optional) e.g. https://www.zohoapis.eu, .in, etc.
SYNC_SHARED_SECRET	🔐 A long random string (your shared secret – used to authenticate your Shortcut to the Worker)

💡 Generate SYNC_SHARED_SECRET from a password manager (something long & random).
⚠️ Do not commit this value to GitHub or share it in screenshots.

Click Save and Deploy.

Copy the Worker URL (something like):
https://your-worker-name.your-subdomain.workers.dev
You’ll paste this into your Shortcut next.

##2️⃣ 🔁 Install and connect the Shortcut

Download Sync_New_Contacts.shortcut from this GitHub repo to your iPhone.

Open it in the Shortcuts app.

Edit the Shortcut as follows:

🧩 a) Point it at your Worker URL

Find the “Get contents of URL” action.

Replace the placeholder URL with your Cloudflare Worker URL.

🧩 b) Make sure it sends the right JSON

In the “Get contents of URL” action:

Method: POST

Request Body: JSON

JSON keys should include:

firstName

lastName

phone

email

company

(Your Shortcut template may already be wired this way — just confirm the keys.)

🧩 c) 🔐 Add the shared secret header

Still inside “Get contents of URL”:

Scroll to Headers → Add new header.

Add:

Header Key	Header Value
x-sync-secret	(exact same string as SYNC_SHARED_SECRET in Cloudflare)

✅ This header is how your Shortcut proves it’s authorized.
❌ If it doesn’t match, the Worker will return 401 Unauthorized.

🧩 d) Confirm the contact filter

At the top of the Shortcut, confirm the filter is set to:

Creation Date → is in the last → 24 (or 48) hours

Notes → does not contain → [ZOHO_SYNCED]

This ensures it only syncs new contacts that haven’t been tagged yet.

## 3️⃣ ✨ Create the daily automation

On your iPhone:

Open Shortcuts → Automation → Create Personal Automation

Choose Time of Day (for example, 8:00 PM)

Set it to repeat Daily

Add the action: Run Shortcut → Sync New Contacts

Turn Ask Before Running OFF

(Optional) Turn Notify When Run ON if you want confirmation each day

Now your phone will quietly run the sync every day and show a small notification with how many contacts were synced. 🎯

## 🔒 Security notes

This setup is designed so that:

Your Zoho tokens and secrets live only in Cloudflare environment variables (not in the Shortcut or repo)

Your Worker requires the x-sync-secret header to match SYNC_SHARED_SECRET

If the header is missing or wrong → returns 401 Unauthorized

This prevents random people from spamming your CRM via your Worker URL

Only the minimal contact fields are sent from iPhone → Worker → Zoho

If you ever think your secret might have leaked:

Change SYNC_SHARED_SECRET in Cloudflare to a new random string (i.e. text)

Update the x-sync-secret header in your Shortcut

Redeploy the Worker

💥 Old values will immediately stop working.

## 🧭 Troubleshoot or customize with ChatGPT

If you get stuck or want to adapt this for a different CRM, paste this prompt into ChatGPT:

I'm using Cristin's iPhone → Zoho CRM Contact Sync Shortcut from GitHub
(https://github.com/Avant-Garde-Haus/icloud-zoho-contact-sync-shortcut
)
My goal is to sync new iPhone contacts to my CRM and mark them as [CRM_SYNCED] in Notes.
Here is my current Shortcut setup and Worker code (pasted or attached).
Please help me debug or customize this. I am not a developer.

ChatGPT can then guide you through debugging, CRM swaps, or field expansions —
whatever you want to build next. 💫

---

## 🪞 About Garde Haus Marketing  

This automation is courtesy of **Garde Haus Marketing**, founded by **Cristin Padgett** — an avant-garde marketer and fractional CMO who blends creative storytelling with data-driven growth strategy.  

At Garde Haus, we believe in balancing **Garde (creativity)** with **Haus (structure)** — building systems that scale while keeping the human spark intact. From custom automations and GTM frameworks to brand positioning and performance marketing, our goal is to help founders and teams turn vision into measurable traction.  

✨ Learn more at [**GardeHaus.Marketing**](https://gardehaus.marketing)
