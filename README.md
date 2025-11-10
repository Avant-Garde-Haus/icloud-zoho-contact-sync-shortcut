# icloud-zoho-contact-sync-shortcut
Built in just a few hours by someone with zero coding experience, this project connects iPhone Contacts to Zoho CRM using an iOS Shortcut and Cloudflare Worker. It automatically syncs new contacts, prevents duplicates with smart notes, and proves what’s possible with ChatGPT-guided no-code development.

# iPhone → Zoho CRM Contact Sync  
*(Built with iOS Shortcuts + Cloudflare Worker)*

This project lets you automatically sync new iPhone contacts to Zoho CRM using:
- An **iOS Shortcut** (`Sync New Contacts`)
- A **Cloudflare Worker** that receives the contact and talks to the Zoho API
- A small marker (`[ZOHO_SYNCED]`) saved in the iOS contact’s Notes so you don’t get duplicates

> You can swap Zoho for another CRM later by changing the Worker code and field names.

---

## Step 0 – Start with ChatGPT (highly recommended)

Most people using this will **not** be developers (I wasn’t either 😊).

Before doing anything else, open ChatGPT and paste this:

> I want to set up Cristin’s **iPhone → Zoho CRM Contact Sync** Shortcut from GitHub.  
> My goal is to sync new iPhone contacts to my CRM and mark them as `[ZOHO_SYNCED]` in the Notes field.  
> Here is the repo link:  
> (paste this GitHub link here)  
> Please walk me through:  
> 1) Creating and configuring the Cloudflare Worker  
> 2) Importing and editing the Shortcut on my iPhone  
> 3) Setting up the automation so it runs every day  
> I have almost zero coding experience. Please talk to me like I’m new to this.

ChatGPT will then act as your “live coach” and walk you through everything step-by-step.

Everything else is for the nerds. 😝

---

## What this setup does

Once everything is configured:

- It finds iPhone contacts created in roughly the last **24–48 hours** whose **Notes** do **not** contain `[ZOHO_SYNCED]`.
- It sends each of those contacts to your Cloudflare Worker.
- If the Worker responds with:  
  `{"message":"Contact synced successfully","status":"SUCCESS","ok":true}`  
  then the Shortcut:
  - Appends `[ZOHO_SYNCED]` to the contact’s Notes on your phone
  - Increases a counter and shows a notification like:  
    `1 contact(s) to Zoho.`

You can change the time window (24h / 48h) and the note tag later if you like.

---

## What you need

You’ll need:

- An **iPhone or iPad** with the **Shortcuts** app
- A **Zoho CRM** account with API access (or another CRM if you adapt the Worker)
- A **Cloudflare** account (free is fine) to run the Worker

---

## Setup overview

### 1. Create and deploy the Cloudflare Worker

With ChatGPT’s help, you’ll:

- Log in to Cloudflare and create a **Worker**.
- Paste in `worker.js` from this repo.
- Update:
  - Your Zoho (or other CRM) API URL
  - Your auth token / refresh token (never share these publicly)
  - How the incoming fields (`firstName`, `lastName`, `email`, `phone`, `company`) map into your CRM’s fields
- Deploy the Worker and copy its **URL**.

> 🔐 Important: this Worker URL is personal.  
> Don’t publish your real URL inside shared shortcuts. In the shared shortcut, I use a placeholder URL so each person can paste their own.

### 2. Install and connect the Shortcut

- Download `Sync_New_Contacts.shortcut` from this GitHub repo to your iPhone.
- Open it in the **Shortcuts** app.
- Edit the Shortcut:
  - Find the **“Get contents of URL”** action.
  - Replace the placeholder URL with **your** Cloudflare Worker URL.
  - In the same action, make sure the request body sends these fields (as JSON):
    - `firstName`
    - `lastName`
    - `phone`
    - `email`
    - `company`
  - At the top of the Shortcut, confirm the filter is:
    - *Creation Date* **is in the last** 24–48 hours  
    - *Notes* **does not contain** `[ZOHO_SYNCED]`

This means it only syncs brand-new contacts that haven’t been tagged yet.

### 3. Create the daily automation

On your iPhone:

- Open **Shortcuts → Automation → Create Personal Automation**.
- Choose **Time of Day** (for example, 8 PM Daily).
- Add the action: **Run Shortcut → Sync New Contacts**.
- Turn **Ask Before Running** OFF and confirm.

Now your phone will quietly run the sync every day and show a little notification with how many contacts were synced.

---

## Get help from ChatGPT (again)

If you get stuck or want to adapt this for a different CRM, you can paste this prompt into ChatGPT:

> I'm using Cristin's **iPhone → Zoho CRM Contact Sync** Shortcut from GitHub  
> (paste the repo link here).  
> My goal is to sync new iPhone contacts to my CRM and mark them as `[ZOHO_SYNCED]` in Notes.  
> Here is my current Shortcut setup and my Worker code:  
> [paste screenshots and/or code]  
> Please help me debug or customize this. I am not a developer.

ChatGPT can then walk you through troubleshooting just like it did for me.
