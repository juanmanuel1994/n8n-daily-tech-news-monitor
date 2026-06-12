# Daily Tech & AI News Monitor

An n8n workflow that automatically collects tech and AI news from multiple RSS feeds every day, filters the most relevant articles, and sends a styled HTML email digest. Every run is logged to a Google Sheet for tracking.

---

## What It Does

1. **Triggers once a day** at 8am via the Schedule node
2. **Fetches articles** from 3 free RSS sources simultaneously
3. **Filters** articles matching keywords like AI, machine learning, ChatGPT, automation
4. **Removes duplicates** and **sorts** by most recent
5. **Builds a styled HTML email** with article titles, links, and summaries
6. **Checks** whether any articles were found (branching logic)
7. **Sends the digest** via Gmail if articles exist
8. **Logs every run** to Google Sheets — whether it sent or not

---

## Workflow Nodes (15 total)

| # | Node | Type | Purpose |
|---|------|------|---------|
| 1 | Daily Trigger 8am | Schedule Trigger | Fires the workflow every 24 hours |
| 2 | RSS - BBC Technology | RSS Feed Read | Pulls tech news from BBC |
| 3 | RSS - Reddit Technology | RSS Feed Read | Pulls posts from r/technology |
| 4 | RSS - Hacker News | RSS Feed Read | Pulls Hacker News front page |
| 5 | Merge Feeds | Merge | Combines all 3 feeds into one stream |
| 6 | Filter by Keywords | Filter | Keeps only AI/tech-relevant articles (regex) |
| 7 | Remove Duplicates | Remove Duplicates | Deduplicates by article title |
| 8 | Sort by Date | Sort | Orders articles newest first |
| 9 | Aggregate News Items | Aggregate | Converts individual items into arrays |
| 10 | Build Email HTML | Code | Generates styled HTML email body |
| 11 | Are There Articles? | IF | Branches: articles found vs. none |
| 12 | Send Email Digest | Gmail | Sends the HTML digest email |
| 13 | Log to Google Sheets | Google Sheets | Records date, count, and status "Sent" |
| 14 | Log - No Articles Found | Code | Logs a message when filter matches nothing |
| 15 | Log Empty Run | Google Sheets | Records date, count 0, and status "No articles" |

---

## Services Used

All free — no paid APIs required.

| Service | Purpose | Cost |
|---------|---------|------|
| BBC Technology RSS | News source | Free |
| Reddit RSS | News source | Free |
| Hacker News RSS | News source | Free |
| Gmail (OAuth2) | Send the email digest | Free |
| Google Sheets | Run history log | Free |

---

## Setup Instructions

### 1. Import the workflow
In n8n go to **Workflows → Import from file** and select `daily-news-monitor.json`.

### 2. Configure Gmail credentials
- In n8n go to **Credentials → New → Gmail OAuth2**
- Follow the OAuth flow with your Google account
- Assign the credential to the **Send Email Digest** node

### 3. Configure Google Sheets credentials
- In n8n go to **Credentials → New → Google Sheets OAuth2**
- Follow the OAuth flow
- Assign it to both **Log to Google Sheets** and **Log Empty Run** nodes

### 4. Create your Google Sheet
Create a new Google Sheet with a tab named `Log` and three columns:

| date | total_articles | status |
|------|---------------|--------|

Copy the Sheet ID from the URL:
```
https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID_HERE/edit
```

Replace `YOUR_GOOGLE_SHEET_ID` in both Sheets nodes with your actual ID.

### 5. Set your email address
In the **Send Email Digest** node, update `fromEmail` and `toEmail` with your Gmail address.

### 6. Activate the workflow
Toggle the workflow to **Active**. It will run automatically every day at 8am.

---

## Customization

**Change the keywords:** Edit the **Filter by Keywords** node and update the regex:
```
AI|artificial intelligence|machine learning|ChatGPT|automation|robot
```

**Add more RSS feeds:** Duplicate any RSS node, point it to a new feed URL, and connect it to the **Merge Feeds** node on a new input index.

**Change the schedule:** Open **Daily Trigger 8am** and adjust the interval or time.

**Cap the number of articles:** In **Build Email HTML**, change `Math.min(titles.length, 15)` to any number you want.

---

## Folder Structure

```
daily-news-monitor/
├── daily-news-monitor.json   # n8n workflow file
└── README.md                 # This file
```
