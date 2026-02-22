# Email Genie - Deployment Guide

## Architecture

```
┌─────────────────┐         ┌──────────────────┐
│   Netlify       │  CORS   │    Railway       │
│  (Frontend)     │◄───────►│   (Backend)      │
│  React App      │         │  Node.js + SQLite│
└─────────────────┘         └──────────────────┘
       │                            │
       │                            │
       ▼                            ▼
  User's Browser              Gmail API
                              (Free SMS via
                               email gateways)
```

## Step 1: Upload to GitHub

### Create Repository Structure

Your GitHub repo should look like this:

```
email-genie/
├── frontend/          # React app (goes to Netlify)
│   ├── src/
│   ├── package.json
│   ├── vite.config.ts
│   └── ...
├── backend/           # Node.js API (goes to Railway)
│   ├── server.js
│   ├── package.json
│   └── ...
├── .gitignore
└── README.md
```

### .gitignore

Create this file in the root:

```gitignore
# Dependencies
node_modules/

# Environment variables
.env
.env.local

# Database
*.db

# Build output
dist/
build/

# Logs
*.log
npm-debug.log*

# OS files
.DS_Store
Thumbs.db
```

### Push to GitHub

```bash
# Initialize repo
git init

# Add all files
git add .

# Commit
git commit -m "Initial commit - Email Genie fullstack"

# Add remote (replace with your repo URL)
git remote add origin https://github.com/YOUR_USERNAME/email-genie.git

# Push
git push -u origin main
```

---

## Step 2: Deploy Backend to Railway

Railway offers a generous free tier (no credit card required for basic usage).

### 2.1 Sign Up

1. Go to https://railway.app
2. Sign up with GitHub
3. Verify your email

### 2.2 Create Project

1. Click "New Project"
2. Select "Deploy from GitHub repo"
3. Choose your `email-genie` repository
4. Railway will auto-detect the backend

### 2.3 Configure Service

1. Click on the service
2. Go to "Settings" tab
3. Set:
   - **Root Directory**: `backend`
   - **Start Command**: `npm start`

### 2.4 Add Environment Variables

Go to "Variables" tab and add:

| Variable | Value | Description |
|----------|-------|-------------|
| `EMAIL_USER` | `your-email@gmail.com` | Gmail for sending SMS |
| `EMAIL_PASS` | `your-app-password` | Gmail app password |
| `ADMIN_TOKEN` | `your-secure-password` | Admin dashboard password |
| `FRONTEND_URL` | `https://your-site.netlify.app` | Your Netlify URL |

**How to get Gmail App Password:**
1. Go to https://myaccount.google.com
2. Enable 2-Factor Authentication
3. Go to Security → 2-Step Verification → App passwords
4. Select "Mail" and generate password
5. Copy the 16-character password

### 2.5 Deploy

Railway will auto-deploy when you add variables.

**Get your Railway URL:**
- Go to your service
- Look for the public URL (e.g., `https://email-genie-production.up.railway.app`)
- Copy this URL - you'll need it for Netlify

---

## Step 3: Deploy Frontend to Netlify

### 3.1 Connect Repository

1. Go to https://netlify.com
2. Click "Add new site" → "Import an existing project"
3. Choose GitHub
4. Select your `email-genie` repository

### 3.2 Build Settings

Configure these settings:

| Setting | Value |
|---------|-------|
| **Base directory** | `frontend` |
| **Build command** | `npm run build` |
| **Publish directory** | `frontend/dist` |

### 3.3 Add Environment Variables

Go to Site settings → Environment variables and add:

| Variable | Value |
|----------|-------|
| `VITE_API_URL` | `https://your-railway-url.railway.app` |

Replace with your actual Railway URL from Step 2.

### 3.4 Deploy

Click "Deploy site"

Netlify will:
1. Install dependencies
2. Build the React app
3. Deploy to CDN

---

## Step 4: Test Everything

### 4.1 Test Backend

Visit: `https://your-railway-url.railway.app/health`

You should see:
```json
{"status": "ok", "timestamp": "..."}
```

### 4.2 Test Signup

1. Go to your Netlify URL
2. Fill out the signup form
3. Click "Start Free Trial"
4. Check that you get a success message with a bridge email

### 4.3 Test Admin Dashboard

1. Go to `https://your-netlify-url.netlify.app/admin`
2. Enter your admin password (from Railway env vars)
3. You should see the dashboard with your test user

---

## Step 5: Set Up Email Receiving (ImprovMX)

To receive emails from CorrLinks, you need to forward them to your Railway webhook.

### 5.1 Sign Up for ImprovMX

1. Go to https://improvmx.com
2. Sign up for free account (5,000 emails/month)

### 5.2 Add Your Domain

1. Add your domain (e.g., `emailgenie.app`)
2. ImprovMX will give you MX records to add

### 5.3 Configure DNS

Add these MX records at your domain registrar:

```
Type: MX
Host: @
Value: mx1.improvmx.com
Priority: 10

Type: MX
Host: @
Value: mx2.improvmx.com
Priority: 20
```

### 5.4 Set Up Webhook Forwarding

1. In ImprovMX dashboard, go to "Advanced"
2. Enable "Webhook forwarding"
3. Set webhook URL:
   ```
   https://your-railway-url.railway.app/api/webhook/email
   ```

### 5.5 Alternative: Use Catch-All

If webhook isn't available on free plan:

1. Set catch-all forward to a Gmail address
2. Use Zapier (free tier) to forward Gmail → Railway webhook

---

## Cost Breakdown (FREE!)

| Service | Cost | Limit |
|---------|------|-------|
| Netlify | Free | 100GB bandwidth/month |
| Railway | Free | $5 credit/month (~500 hours) |
| Gmail | Free | 500 emails/day |
| ImprovMX | Free | 5,000 emails/month |
| **Total** | **$0** | - |

---

## Troubleshooting

### "Error" on signup

1. Check browser console for error details
2. Verify `VITE_API_URL` is set correctly in Netlify
3. Check Railway logs for backend errors

### CORS errors

1. Verify `FRONTEND_URL` in Railway matches your Netlify URL exactly
2. Check for `https://` vs `http://`
3. No trailing slash

### SMS not received

1. Check Gmail credentials in Railway
2. Verify phone number format (10 digits)
3. Check carrier is supported
4. Check Railway logs for email errors

### Database issues

1. SQLite file is created automatically
2. Data persists on Railway (they use persistent storage)
3. Backup: Download `emailgenie.db` from Railway dashboard

---

## Updating Your Site

### Update Frontend

```bash
# Make changes to frontend/
git add .
git commit -m "Update frontend"
git push

# Netlify auto-deploys!
```

### Update Backend

```bash
# Make changes to backend/
git add .
git commit -m "Update backend"
git push

# Railway auto-deploys!
```

---

## Custom Domain (Optional)

### Netlify Custom Domain

1. Buy domain (Namecheap, Google Domains, etc.)
2. In Netlify: Domain settings → Add custom domain
3. Follow DNS instructions

### Railway Custom Domain

1. In Railway: Settings → Domains
2. Add your domain
3. Configure DNS CNAME record

---

## Security Checklist

- [ ] Changed default `ADMIN_TOKEN` from "admin123"
- [ ] Using Gmail app password (not regular password)
- [ ] Enabled HTTPS (Netlify/Railway do this automatically)
- [ ] Set `FRONTEND_URL` for CORS protection
- [ ] Regular database backups

---

## Need Help?

- Railway docs: https://docs.railway.app
- Netlify docs: https://docs.netlify.com
- Check logs in Railway dashboard for backend errors
- Check browser console for frontend errors
