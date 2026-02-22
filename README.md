# Email Genie - CorrLinks to SMS Forwarding

A complete, free solution for forwarding CorrLinks emails to SMS text messages.

## Features

- ✅ **7-Day Free Trial** - No credit card required
- ✅ **Instant Setup** - Get your unique bridge email in seconds  
- ✅ **Works on Any Phone** - Flip phones, smartphones, pagers
- ✅ **FREE SMS Delivery** - Uses carrier email-to-SMS gateways (no API costs!)
- ✅ **Admin Dashboard** - Manage users, payments, and messages
- ✅ **Secure & Private** - SQLite database, no data sharing

## How It Works

1. User signs up and gets a unique bridge email (e.g., `user-abc123@bridge.emailgenie.app`)
2. User adds this bridge email as a contact in their CorrLinks account
3. When an inmate sends an email to the bridge email, our server receives it
4. The server forwards the email content as an SMS to the user's phone using free carrier gateways

## Tech Stack

| Layer | Technology | Hosting |
|-------|-----------|---------|
| Frontend | React + TypeScript + Vite + Tailwind | Netlify (Free) |
| Backend | Node.js + Express | Railway (Free) |
| Database | SQLite | Railway (Persistent) |
| SMS | Gmail + Carrier Gateways | Free |

## Free Carrier SMS Gateways

| Carrier | SMS Email Format |
|---------|-----------------|
| Verizon | number@vtext.com |
| AT&T | number@txt.att.net |
| T-Mobile | number@tmomail.net |
| Sprint | number@messaging.sprintpcs.com |
| Cricket | number@sms.cricketwireless.net |
| Boost | number@sms.myboostmobile.com |
| Metro PCS | number@mymetropcs.com |
| US Cellular | number@email.uscc.net |

**Cost: $0** - Just uses Gmail to send emails to these addresses!

## Quick Start

### 1. Clone & Install

```bash
git clone https://github.com/YOUR_USERNAME/email-genie.git
cd email-genie

# Install frontend dependencies
cd frontend
npm install

# Install backend dependencies
cd ../backend
npm install
```

### 2. Configure Environment Variables

**Frontend** (`frontend/.env`):
```env
VITE_API_URL=http://localhost:3001
```

**Backend** (`backend/.env`):
```env
EMAIL_USER=your-email@gmail.com
EMAIL_PASS=your-gmail-app-password
ADMIN_TOKEN=your-secure-admin-password
FRONTEND_URL=http://localhost:5173
```

### 3. Run Locally

```bash
# Terminal 1 - Backend
cd backend
npm run dev

# Terminal 2 - Frontend
cd frontend
npm run dev
```

- Frontend: http://localhost:5173
- Backend: http://localhost:3001
- Admin: http://localhost:5173/admin

## Deployment

See [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md) for detailed instructions on deploying to Netlify + Railway.

### Quick Deploy

1. **Push to GitHub**
   ```bash
   git add .
   git commit -m "Initial commit"
   git push
   ```

2. **Deploy Backend to Railway**
   - Connect GitHub repo to Railway
   - Set environment variables
   - Railway auto-deploys

3. **Deploy Frontend to Netlify**
   - Connect GitHub repo to Netlify
   - Set `VITE_API_URL` to Railway URL
   - Netlify auto-builds and deploys

## Project Structure

```
email-genie/
├── frontend/              # React frontend
│   ├── src/
│   │   ├── sections/
│   │   │   ├── LandingPage.tsx      # Main landing page
│   │   │   └── AdminDashboard.tsx   # Admin panel
│   │   ├── components/ui/           # shadcn/ui components
│   │   ├── App.tsx
│   │   └── ...
│   ├── package.json
│   └── vite.config.ts
├── backend/               # Node.js API
│   ├── server.js          # Main server file
│   ├── package.json
│   └── .env.example
├── DEPLOYMENT_GUIDE.md    # Detailed deployment guide
└── README.md              # This file
```

## API Endpoints

### Public Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/signup` | Create new user account |
| POST | `/api/webhook/email` | Receive forwarded emails |
| GET | `/health` | Health check |

### Admin Endpoints (Requires Bearer Token)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/admin/users` | List all users |
| GET | `/api/admin/messages` | List recent messages |
| GET | `/api/admin/stats` | Get dashboard stats |
| PUT | `/api/admin/users/activate` | Mark user as paid |
| PUT | `/api/admin/users/deactivate` | Deactivate user |

**Admin Token**: Set via `ADMIN_TOKEN` environment variable

## Database Schema

### users
- `id`, `name`, `email`, `phone`, `carrier`
- `bridge_email` - Unique forwarding email
- `sms_email` - Computed SMS gateway email
- `status`, `plan`, `payment_status`
- `trial_ends_at`, `created_at`

### messages
- `id`, `user_id`, `from_email`, `subject`, `body`
- `sms_sent`, `created_at`

### payments
- `id`, `user_id`, `amount`, `payment_method`
- `transaction_id`, `notes`, `created_at`

## Payment Processing

This system uses simple manual payment verification:

1. User pays via Cash App, Venmo, PayPal, or Zelle
2. You receive the payment notification
3. Go to Admin Dashboard → Pending Payment
4. Click "Mark as Paid" and record the payment method
5. User's account is immediately activated

## Setting Up Email Receiving

To receive emails from CorrLinks, set up email forwarding to your webhook:

### Option 1: ImprovMX (Recommended - Free)

1. Sign up at https://improvmx.com (5,000 emails/month free)
2. Add your domain and configure MX records
3. Set webhook URL: `https://your-railway-url.railway.app/api/webhook/email`

### Option 2: Zapier (Free Tier)

1. Set up Gmail → Webhook Zap
2. Forward emails to your Railway webhook

### Option 3: Mailgun (Free Tier)

1. Sign up at https://mailgun.com (5,000 emails/month free)
2. Create route to forward to webhook

## Cost Breakdown

| Service | Cost | Limit |
|---------|------|-------|
| Netlify | Free | 100GB/month |
| Railway | Free | $5 credit/month |
| Gmail | Free | 500 emails/day |
| ImprovMX | Free | 5,000 emails/month |
| **Total** | **$0** | - |

## Troubleshooting

### Signup shows "Error"

1. Check browser console for details
2. Verify `VITE_API_URL` is set correctly
3. Check Railway logs for backend errors

### SMS not received

1. Check Gmail credentials are correct
2. Verify phone number format (10 digits)
3. Check carrier is supported
4. Review Railway logs

### CORS errors

1. Verify `FRONTEND_URL` matches your Netlify URL exactly
2. Include `https://` prefix
3. No trailing slash

## Environment Variables Reference

### Frontend

| Variable | Required | Description |
|----------|----------|-------------|
| `VITE_API_URL` | Yes | Backend API URL |

### Backend

| Variable | Required | Description |
|----------|----------|-------------|
| `EMAIL_USER` | Yes | Gmail address for sending SMS |
| `EMAIL_PASS` | Yes | Gmail app password |
| `ADMIN_TOKEN` | Yes | Admin dashboard password |
| `FRONTEND_URL` | Yes | Netlify URL for CORS |
| `PORT` | No | Server port (default: 3001) |

## License

MIT - Free to use and modify.

## Support

For questions or issues, check:
- [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md) for deployment help
- Railway docs: https://docs.railway.app
- Netlify docs: https://docs.netlify.com
