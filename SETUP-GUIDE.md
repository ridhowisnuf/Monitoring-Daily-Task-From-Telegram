# 📖 Detailed Setup Guide

This guide will walk you through setting up the AI-Powered Telegram Task Management Bot step-by-step.

---

## 📋 Prerequisites Checklist

Before you begin, ensure you have:

- [ ] n8n instance (Sumopod, self-hosted, or n8n Cloud)
- [ ] Notion account
- [ ] Telegram account
- [ ] Computer with LM Studio installed (Mac/Windows/Linux)
- [ ] Ngrok account (free)
- [ ] PDF.co account (free)
- [ ] Basic knowledge of APIs and webhooks

**Estimated Setup Time:** 30-45 minutes

---

## 🔧 Step-by-Step Installation

### Part 1: Notion Database Setup (10 minutes)

#### 1.1 Create Database

1. Open Notion
2. Click **"+ New Page"**
3. Select **"Table"** → **"Database - Inline"**
4. Name it: **"Daily Tasks"** or **"To-Do List"**

#### 1.2 Add Required Properties

Add these columns to your database:

| Property Name | Type | Options |
|---------------|------|---------|
| Name | Title | (default) |
| Status | Select | To do, In progress, Done |
| Priority | Select | High, Medium, Low |
| Category | Select | Work, Personal |
| Due Date | Date | (enable time if needed) |

**Visual Guide:**
```
| Name (Title) | Status (Select) | Priority (Select) | Category (Select) | Due Date (Date) |
|--------------|-----------------|-------------------|-------------------|-----------------|
| Task 1       | To do          | High              | Work              | 2026-02-20      |
```

#### 1.3 Get Notion Integration Token

1. Go to https://www.notion.so/my-integrations
2. Click **"+ New integration"**
3. Fill in:
   - Name: "n8n Task Bot"
   - Associated workspace: Select your workspace
   - Type: Internal
4. Click **"Submit"**
5. **Copy the "Internal Integration Token"** (starts with `secret_`)
6. Save it somewhere safe (you'll need it in n8n)

#### 1.4 Share Database with Integration

1. Open your "Daily Tasks" database
2. Click **"Share"** (top right)
3. Click **"Invite"**
4. Search for your integration name: "n8n Task Bot"
5. Select it and click **"Invite"**

#### 1.5 Get Database ID

1. Open your database as a **full page** (click "Open as page" if inline)
2. Look at the URL in your browser:
   ```
   https://www.notion.so/workspace-name/DATABASE_ID?v=...
   ```
3. **Copy the DATABASE_ID** (32 characters, alphanumeric)
4. Example: `a1b2c3d4e5f6789012345678901234ab`

**✅ Notion Setup Complete!**

---

### Part 2: Telegram Bot Setup (5 minutes)

#### 2.1 Create Bot

1. Open Telegram
2. Search for **@BotFather**
3. Start conversation: `/start`
4. Create new bot: `/newbot`
5. Choose bot name: `My Task Manager Bot`
6. Choose username: `mytaskmanager_bot` (must end with _bot)
7. **Copy the Bot Token** (looks like: `123456789:ABCdefGHI...`)

#### 2.2 Get Your Chat ID

**Method 1: Using Bot**
1. Send a message to your bot (e.g., "Hello")
2. Open in browser:
   ```
   https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates
   ```
3. Look for:
   ```json
   "chat": {
     "id": 123456789,
     ...
   }
   ```
4. **Copy the chat ID** number

**Method 2: Using @userinfobot**
1. Search for @userinfobot on Telegram
2. Start the bot
3. It will show your ID

**✅ Telegram Setup Complete!**

---

### Part 3: LM Studio & AI Model Setup (15 minutes)

#### 3.1 Install LM Studio

**For macOS:**
1. Download from https://lmstudio.ai/
2. Open the .dmg file
3. Drag LM Studio to Applications
4. Open LM Studio

**For Windows:**
1. Download installer from https://lmstudio.ai/
2. Run the .exe file
3. Follow installation wizard
4. Launch LM Studio

**For Linux:**
1. Download AppImage from https://lmstudio.ai/
2. Make it executable: `chmod +x LMStudio.AppImage`
3. Run: `./LMStudio.AppImage`

#### 3.2 Download AI Model

1. Open LM Studio
2. Click **"Discover"** tab (left sidebar)
3. Search: **"llama-3.2-3b-instruct"**
4. Find the model (should be ~2GB)
5. Click **"Download"**
6. Wait for download to complete (~5-10 minutes depending on connection)

#### 3.3 Start Local Server

1. Click **"Local Server"** tab (left sidebar, looks like a globe)
2. In "Model to load" dropdown, select: **llama-3.2-3b-instruct**
3. Click **"Start Server"**
4. You should see:
   - ✅ **Status: Running**
   - 🌐 **Server URL:** `http://localhost:1234`

#### 3.4 Test LM Studio

Open Terminal/Command Prompt and run:

```bash
curl http://localhost:1234/v1/models
```

**Expected response:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "llama-3.2-3b-instruct",
      ...
    }
  ]
}
```

**✅ LM Studio Setup Complete!**

---

### Part 4: Ngrok Setup (5 minutes)

Ngrok creates a secure tunnel from the internet to your local LM Studio server.

#### 4.1 Install Ngrok

**macOS (via Homebrew):**
```bash
brew install ngrok
```

**Windows/Linux:**
1. Download from https://ngrok.com/download
2. Extract to a folder
3. Add to PATH or run from folder

#### 4.2 Create Ngrok Account

1. Go to https://ngrok.com/signup
2. Sign up (free account)
3. Verify your email

#### 4.3 Get Auth Token

1. Log in to https://dashboard.ngrok.com
2. Go to **"Your Authtoken"**
3. **Copy your authtoken**

#### 4.4 Authenticate Ngrok

```bash
ngrok config add-authtoken YOUR_AUTH_TOKEN_HERE
```

#### 4.5 Start Tunnel

```bash
ngrok http 1234
```

**You should see:**
```
Session Status    online
Forwarding        https://abc123xyz.ngrok-free.app -> http://localhost:1234
```

**IMPORTANT:** 
- ✅ **Copy the HTTPS URL** (e.g., `https://abc123xyz.ngrok-free.app`)
- ⚠️ Keep the terminal window open! If you close it, the tunnel stops
- 💡 Free tier: URL changes each time you restart ngrok

#### 4.6 Test Ngrok Tunnel

```bash
curl https://YOUR_NGROK_URL.ngrok-free.app/v1/models
```

Should return the same response as the localhost test.

**✅ Ngrok Setup Complete!**

---

### Part 5: PDF.co API Setup (3 minutes)

#### 5.1 Create Account

1. Go to https://app.pdf.co/signup
2. Sign up (free account)
3. Verify your email

#### 5.2 Get API Key

1. Log in to https://app.pdf.co
2. Go to https://app.pdf.co/api-keys
3. **Copy your API key**
4. Free tier: 300 API calls/month

**✅ PDF.co Setup Complete!**

---

### Part 6: n8n Workflow Import (10 minutes)

#### 6.1 Import Workflows

1. Open your n8n instance
2. Go to **Workflows** (left sidebar)
3. Click **"Add workflow" → "Import from File"**

**Import Main Workflow:**
1. Click **"Select file"**
2. Choose `Telegram_Workflow_1.json`
3. Click **"Import"**

**Import Callback Workflow:**
1. Click **"Add workflow" → "Import from File"** again
2. Choose `Sub__Telegram_Workflow_1__-_1.json`
3. Click **"Import"**

#### 6.2 Configure Credentials

You need to add credentials for:
- Notion
- Telegram (via HTTP Header Auth for bot token)

**Add Notion Credentials:**
1. Click any Notion node
2. Click **"Create New Credential"**
3. Enter:
   - **API Key:** Your Notion Integration Token (`secret_...`)
4. Click **"Save"**

**No separate Telegram credential needed** (we use bot token in URL)

#### 6.3 Update Configuration Variables

You need to update these in the workflows:

**In Telegram_Workflow_1:**

1. **Notion Nodes:**
   - Database ID: Replace with your database ID
   - Select your Notion credential

2. **HTTP Request - Call LM Studio API:**
   - URL: Replace with `https://YOUR_NGROK_URL.ngrok-free.app/v1/chat/completions`

3. **HTTP Request - Convert HTML to PDF:**
   - Headers → x-api-key: Replace with your PDF.co API key

4. **HTTP Request - Send PDF to Telegram:**
   - URL: Replace with `https://api.telegram.org/bot<YOUR_BOT_TOKEN>/sendDocument`
   - Form parameter chat_id: Replace with your Telegram chat ID

**In Sub__Telegram_Workflow_1__-_1:**

1. **Notion Update nodes:**
   - Database ID: Replace with your database ID
   - Select your Notion credential

2. **HTTP Request - Send Message:**
   - URL: Replace with `https://api.telegram.org/bot<YOUR_BOT_TOKEN>/sendMessage`

#### 6.4 Test Individual Nodes

Before activating:

1. **Test Notion Connection:**
   - Click "Get many database pages1" node
   - Click "Execute node"
   - Should show your tasks

2. **Test LLM Connection:**
   - Click "Call LM Studio API" node
   - Make sure LM Studio and Ngrok are running
   - Check URL is correct
   - Execute node

3. **Test PDF Generation:**
   - Execute "Convert HTML to PDF" node
   - Should return a PDF URL

**✅ n8n Setup Complete!**

---

### Part 7: Activate & Test (5 minutes)

#### 7.1 Activate Workflows

1. Open **Telegram_Workflow_1**
2. Toggle **Active** (top right)
3. You should see "Workflow activated successfully"

4. Open **Sub__Telegram_Workflow_1__-_1**
5. Toggle **Active**

#### 7.2 Test the Bot

1. Open Telegram
2. Go to your bot
3. Send: `/start`

**Expected response:**
```
Welcome! I'm your Task Management Bot.

Commands:
/todo - View To Do tasks
/inprogress - View In Progress tasks
/done - View Done tasks
/report - Generate AI report
```

4. Send: `/todo`

**Expected response:**
List of your To Do tasks with status change buttons

5. Send: `/report`

**Expected response:**
"Generating your report..." followed by a PDF file after ~15-30 seconds

#### 7.3 Troubleshooting First Run

**If bot doesn't respond:**
- ✅ Check workflow is Active
- ✅ Check Telegram bot token is correct
- ✅ Check webhook is registered (n8n does this automatically)

**If report fails:**
- ✅ Check LM Studio is running
- ✅ Check Ngrok tunnel is active
- ✅ Check Ngrok URL in n8n is current
- ✅ Check PDF.co API key is valid

**If tasks don't show:**
- ✅ Check Notion database has tasks
- ✅ Check Notion integration is shared with database
- ✅ Check database ID is correct

**✅ Testing Complete!**

---

## 🎉 You're All Set!

Your AI-Powered Telegram Task Management Bot is now fully operational!

### What's Next?

1. **Add some tasks** in your Notion database
2. **Try all commands** in Telegram
3. **Generate a report** and review the AI insights
4. **Update task statuses** via the interactive buttons

### Daily Usage

1. **Keep LM Studio running** when you want to generate reports
2. **Keep Ngrok tunnel active** (or restart when needed)
3. **Free tier limits:**
   - PDF.co: 300 reports/month
   - Ngrok: URL changes on restart (upgrade for static URL)

### Maintenance

- **Restart Ngrok** if your computer restarts (update URL in n8n)
- **Update Notion database** directly or via Telegram
- **Monitor PDF.co usage** in dashboard
- **Check n8n execution history** for errors

---

## 📞 Getting Help

If you encounter issues:

1. **Check Execution History** in n8n
2. **View error messages** in failed executions
3. **Test each component** individually
4. **Review this guide** for missed steps
5. **Check Prerequisites** are all met

Common issues are covered in the main README.md Troubleshooting section.

---

**Happy Task Managing! 🚀**
