# SalesTrack — Complete Setup Guide

## Overview

SalesTrack is a web app (PWA) that runs in a browser on any mobile phone — no app store or APK needed. It tracks your sales team's:
- ✅ Daily check-in / check-out with GPS
- 🏪 Customer visits with exact GPS coordinates
- 🆕 New prospect information with GPS
- 🏍️ Travel routes with full GPS breadcrumbs (captured every 2 minutes)

All data goes straight into your Google Sheet.

---

## STEP 1 — Create a Google Cloud Project

1. Go to [https://console.cloud.google.com](https://console.cloud.google.com)
2. Sign in with **your Google account** (the same one that owns the Google Sheet)
3. Click **"Select a project"** at the top → **"New Project"**
4. Name it: `SalesTrack` → Click **"Create"**

---

## STEP 2 — Enable the Google Sheets API

1. In your new project, go to **"APIs & Services"** → **"Library"**
2. Search for **"Google Sheets API"** → Click it → Click **"Enable"**

---

## STEP 3 — Create OAuth 2.0 Credentials

1. Go to **"APIs & Services"** → **"Credentials"**
2. Click **"+ Create Credentials"** → **"OAuth client ID"**
3. If prompted to configure Consent Screen:
   - Choose **"External"** → Fill in App Name: `SalesTrack`, your email → Save
   - Under **"Scopes"** → Add scope: `https://www.googleapis.com/auth/spreadsheets`
   - Under **"Test users"** → Add all your salespeople's Google emails
4. Back in Credentials → **"+ Create Credentials"** → **"OAuth client ID"**
5. Application type: **"Web application"**
6. Name: `SalesTrack Web`
7. Under **"Authorized JavaScript origins"** → Add your app URL (see Step 5 for hosting)
   - Example: `https://yourusername.github.io`
   - For local testing: `http://localhost:8080`
8. Click **"Create"**
9. **Copy the Client ID** — you'll need it in the app (looks like: `xxxxxxxx.apps.googleusercontent.com`)

---

## STEP 4 — Create Your Google Sheet

1. Go to [https://sheets.google.com](https://sheets.google.com)
2. Create a new blank spreadsheet
3. Name it: `SalesTrack Data`
4. **Share it with all your salespeople** (Editor access):
   - Click **"Share"** → Add each person's Google email → set to **"Editor"**
5. Copy the **Spreadsheet ID** from the URL:
   - URL looks like: `https://docs.google.com/spreadsheets/d/`**`1BxiMVs0XRA5n...`**`/edit`
   - The bold part is the ID

> **Note:** When your sales staff first signs in and logs an activity, the app will automatically create 4 sheet tabs with the correct headers:
> - `Daily_Logs`
> - `Customer_Visits`
> - `New_Prospects`
> - `Travel_Logs`

---

## STEP 5 — Host the App (Free with GitHub Pages)

The app must be hosted on HTTPS for GPS to work on mobile phones.

### Option A: GitHub Pages (Recommended — Free)

1. Create a free account at [https://github.com](https://github.com)
2. Click **"New repository"** → Name it `salestrack` → Set to **Public** → Create
3. Upload the 4 files: `index.html`, `manifest.json`, `sw.js`, `icon.svg`
4. Go to repository **Settings** → **Pages** → Source: **"Deploy from branch: main"**
5. Your app URL will be: `https://yourusername.github.io/salestrack`
6. Go back to Google Cloud Console → Credentials → Edit your OAuth client
7. Add `https://yourusername.github.io` to **Authorized JavaScript Origins** → Save

### Option B: Netlify (Even Easier)

1. Go to [https://app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag and drop the entire `salestrack` folder into the browser
3. Netlify gives you an instant HTTPS URL like: `https://random-name.netlify.app`
4. Add that URL to your Google OAuth authorized origins

---

## STEP 6 — Configure the App

1. Open the app URL on your phone
2. The **Setup screen** will appear
3. Enter your **Google Client ID** (from Step 3)
4. Enter your **Spreadsheet ID** (from Step 4)
5. Tap **"Save & Continue"**
6. Tap **"Sign in with Google"** and sign in with a salespeople's account

---

## STEP 7 — Install on Mobile Phone

### Android (Chrome):
1. Open the app URL in Chrome
2. Tap the **⋮ menu** → **"Add to Home screen"**
3. Or wait for Chrome to show the install banner at the bottom

### iPhone/iPad (Safari):
1. Open the app URL in **Safari** (not Chrome — Safari is required for iOS)
2. Tap the **Share button** (box with arrow)
3. Tap **"Add to Home Screen"**
4. Tap **"Add"**

The app icon will appear on the home screen and open like a native app!

---

## STEP 8 — Share with Your Team

Send each salesperson:
1. The app URL
2. Instructions to install it (Step 7)
3. Their Google email must be added to the Google Sheet as Editor (Step 4)

---

## Understanding Your Google Sheet Data

### Daily_Logs Tab
| Column | Description |
|--------|-------------|
| Date | The date |
| Salesperson | Name from Google account |
| Type | "Check-In" or "Check-Out" |
| Time | Time of check-in/out |
| Latitude / Longitude | Exact GPS coordinates |
| Maps Link | Click to see location on Google Maps |
| Hours Worked | Calculated for Check-Out rows |

### Customer_Visits Tab
| Column | Description |
|--------|-------------|
| Customer Name | Business visited |
| Visit Time | When they arrived |
| Latitude / Longitude | **Exact GPS of the visit location** |
| Maps Link | Click to verify location |
| Outcome | Order Received / Follow Up / etc. |

### New_Prospects Tab
Includes all customer info plus GPS coordinates for each new prospect location.

### Travel_Logs Tab
| Column | Description |
|--------|-------------|
| Start/End Lat/Lng | GPS of start and end points |
| Duration (min) | Total travel time |
| **GPS Route Points** | **Full breadcrumb trail — every point captured during travel** |
| **Route Maps Link** | **Click to see the full route on Google Maps** |

---

## Useful Google Sheets Formulas

### Total visits per person this week:
```
=COUNTIFS(Customer_Visits!B:B,"John",Customer_Visits!A:A,">="&TODAY()-7)
```

### Total hours worked this month:
```
=SUMPRODUCT((Daily_Logs!B:B="John")*(Daily_Logs!D:D="Check-Out")*(MONTH(Daily_Logs!A:A)=MONTH(TODAY()))*VALUE(Daily_Logs!J:J))
```

### Count new Hot prospects this week:
```
=COUNTIFS(New_Prospects!B:B,"John",New_Prospects!L:L,"Hot",New_Prospects!A:A,">="&TODAY()-7)
```

---

## Troubleshooting

**"Sign-in failed: popup_closed_by_user"**
→ Allow popups for the app URL in your browser settings.

**"Location denied"**
→ Go to browser Settings → Site Permissions → Location → Allow for the app URL.

**"API error: 403"**
→ Make sure the Google Sheet is shared with the salesperson's email as Editor.

**"API error: invalid_client"**
→ Check the Client ID is correct and the app URL is in Authorized JavaScript Origins.

**GPS not capturing during travel**
→ Keep the app open and screen awake during travel. On Android, disable battery optimization for Chrome.

---

## Data You Can Track in Google Sheets (for your dashboard)

- 📍 Where each sales rep was at any time (GPS coordinates with Maps links)
- ⏱️ How many hours each rep worked per day
- 🏪 How many customer visits per rep per week
- 🆕 How many new prospects per rep per week
- 🔥 Quality of leads (Hot/Warm/Cold breakdown)
- 🏍️ Total travel time per rep
- 🗺️ Full route maps for each travel session

---

*SalesTrack v1.0 — Built for soap sales teams*
