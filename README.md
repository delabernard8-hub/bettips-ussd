# ⚽ BetTips GH — MTN USSD Football Predictions
### Short Code: *714#

A production-ready USSD service for MTN Ghana that serves free and premium (VIP) football predictions, with MTN Mobile Money payment integration.

---

## 📁 Project Structure

```
ussd-backend/
├── server.js                  # Entry point
├── .env.example               # Environment variables template
├── package.json
├── config/
│   └── models.js              # MongoDB schemas (User, Subscription, Prediction, Session)
├── services/
│   ├── ussdHandler.js         # Core USSD menu logic
│   ├── sessionService.js      # Session & user helpers
│   └── momoService.js         # MTN Mobile Money API integration
└── routes/
    ├── ussd.js                # POST /ussd  ← MTN gateway hits this
    ├── momo.js                # POST /api/momo/callback ← MoMo webhook
    └── admin.js               # POST/GET /api/admin/* ← manage predictions
```

---

## 🚀 Setup & Installation

### 1. Install dependencies
```bash
npm install
```

### 2. Configure environment
```bash
cp .env.example .env
# Fill in your values in .env
```

### 3. Start MongoDB
```bash
mongod --dbpath /data/db
```

### 4. Run the server
```bash
# Development (with auto-reload)
npm run dev

# Production
npm start
```

---

## 📞 USSD Menu Flow

```
*714#
├── 1. Free Daily Tips
│   └── [Select match] → Shows free tip + odds
├── 2. VIP Predictions
│   ├── 1. View VIP Tips (requires active subscription)
│   │   └── [Select match] → Shows correct score + confidence %
│   └── 2. Subscribe
│       ├── 1. Daily   GHS 2.00  (1 day)
│       ├── 2. Weekly  GHS 10.00 (7 days)
│       └── 3. Monthly GHS 35.00 (30 days)
│           └── Confirm → MTN MoMo payment prompt sent
├── 3. My Account → Shows VIP status & expiry
└── 4. Help / Contact
```

---

## 💳 MTN Mobile Money Integration

### How payments work:
1. User selects a package and confirms on USSD
2. Server calls MTN MoMo `POST /collection/v1_0/requesttopay`
3. User receives a **mobile money prompt** on their phone
4. After approval, MTN calls your `/api/momo/callback` webhook
5. Server activates VIP for the user automatically

### Getting MTN MoMo API credentials (Ghana):
1. Go to [https://momodeveloper.mtn.com](https://momodeveloper.mtn.com)
2. Create an account and subscribe to the **Collections** product
3. Get your Subscription Key from the dashboard
4. Create API User & API Key using the sandbox provisioning API
5. Switch to production after testing

---

## 📊 Admin API

Manage predictions via REST API. All endpoints require header:
```
X-Admin-Secret: your_admin_secret_here
```

### Add a prediction
```bash
curl -X POST https://yourdomain.com/api/admin/predictions \
  -H "X-Admin-Secret: your_secret" \
  -H "Content-Type: application/json" \
  -d '{
    "match": "Real Madrid vs Barcelona",
    "league": "La Liga",
    "tipFree": "Both teams to score",
    "tipVIP": "Correct Score: 2-1",
    "oddsFree": "1.65",
    "oddsVIP": "7.50",
    "confidence": "89%",
    "matchDate": "2026-05-23T19:00:00.000Z"
  }'
```

### Get stats dashboard
```bash
curl https://yourdomain.com/api/admin/stats \
  -H "X-Admin-Secret: your_secret"
```

---

## 🌍 Registering Your USSD Short Code with MTN Ghana

1. **Contact MTN Ghana Business**: Call 100 or visit [mtnbusiness.com.gh](https://mtnbusiness.com.gh)
2. **Apply for a short code** — provide your business registration, service description
3. **Share your endpoint**: Your server URL e.g. `https://yourdomain.com/ussd`
4. **MTN will configure** their USSD gateway to forward `*714#` requests to your server
5. **Test in sandbox** before going live

---

## ☁️ Deployment (Recommended: DigitalOcean / AWS / Render)

```bash
# Using PM2 for production process management
npm install -g pm2
pm2 start server.js --name "bettips-ussd"
pm2 save
pm2 startup
```

Your server must have:
- ✅ Public HTTPS URL (MTN requires HTTPS)
- ✅ SSL certificate (use Let's Encrypt / Certbot)
- ✅ Port 443 open
- ✅ MongoDB running (or use MongoDB Atlas)

---

## ⚠️ Important Notes

- Responses must start with `CON ` (continue) or `END ` (end session)
- USSD sessions timeout after ~3 minutes of inactivity
- Keep messages short — USSD screens fit ~182 characters
- Always end payment flows with `END` to avoid double charges
- Test thoroughly in MTN sandbox before going live
- Include responsible gambling disclaimers (legal requirement)

---

## 📞 Support
For setup help, call 0244000000 or WhatsApp 0554000000.
