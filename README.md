🧠 MINI CRM – Backend (Node.js, MongoDB, Google OAuth, Campaign Engine)

A lightweight CRM system built with Node.js, Express, MongoDB, and Google OAuth 2.0.
Supports customer management, order tracking, campaign creation, rule-based audience filtering, AI-ready messaging architecture, and a vendor simulation pipeline for delivery tracking.

🚀 Features
1. Customer & Order Management

Add customers

Track orders

MongoDB collections:

customer1

orders1

2. Campaign Management

Create marketing campaigns

Apply rule-based audience filtering

Supported rules:

min_spend

inactive_days

Only customers matching the rules receive campaign messages

3. Communication Log Engine

For every campaign:

One log per matched customer

Stores:

campaign_id

customer_id

personalized message

status (PENDING, SENT, FAILED)

vendor message ID

MongoDB collection:

communication_logs1

4. Vendor Delivery Simulation

A fake vendor API simulates message delivery:

90% chance → SENT

10% chance → FAILED

Vendor calls back a delivery receipt endpoint

Communication logs are updated accordingly

5. Google OAuth 2.0 Authentication

Implemented using:

Passport.js

Google OAuth Strategy

express-session

Flow:

/auth/google → redirect to Google

/auth/google/callback → Google returns user

Session is created

Protected routes require login

Protected areas:

Campaign creation

Campaign logs

🛠 Tech Stack

Backend: Node.js, Express
Database: MongoDB Atlas
Auth: Google OAuth 2.0 (Passport.js)
Vendor Simulation: Custom Express Route
AI-ready: OpenAI API integration supported (optional)

📦 Installation
1. Clone the project
git clone <repo-url>
cd Backend

2. Install dependencies
npm install

3. Create .env file
MONGO_URI=your-mongodb-uri
GOOGLE_CLIENT_ID=your-client-id
GOOGLE_CLIENT_SECRET=your-secret
SESSION_SECRET=mini-crm-secret
FRONTEND_URL=http://localhost:3000

▶️ Run the Server
npm run dev


Backend runs at:

http://localhost:5000

🔐 Authentication Routes
Method	Endpoint	Description
GET	/auth/google	Start Google Login
GET	/auth/google/callback	Google OAuth Callback
GET	/auth/me	Get logged-in user
GET	/auth/logout	Logout
📁 API Endpoints
Customers
Method	Endpoint	Description
POST	/customers	Add customer
GET	/customers	Get all customers
Orders
Method	Endpoint	Description
POST	/orders	Add order
GET	/orders	Get all orders
Campaigns (Requires Login)
Method	Endpoint	Description
POST	/api/campaigns	Create campaign + generate logs
GET	/api/campaigns	List campaigns
GET	/api/campaigns/:id/logs	Get logs for a campaign
Vendor Simulation
Method	Endpoint	Description
POST	/api/vendor/send	Vendor receives request to send message
Delivery Receipts
Method	Endpoint	Description
POST	/api/receipts/delivery	Vendor updates message status
🎯 How Campaign Delivery Works

User creates campaign

System filters customers based on rules

Creates communication logs (PENDING)

Sends each log to vendor API

Vendor randomly marks as SENT or FAILED

Vendor calls receipt endpoint

Communication log is updated

🤖 Optional: OpenAI Integration (AI Messages)

The project supports adding ChatGPT for:

Personalized campaign messages

Customer summaries

AI-powered dashboards

A utility function can use OpenAI’s API to generate messages dynamically.

📌 Folder Structure
Backend/
│── config/
│   └── passport.js
│── routes/
│   ├── customerRoutes.js
│   ├── orderRoutes.js
│   ├── campaignRoutes.js
│   ├── vendor.js
│   ├── receipts.js
│   └── auth.js
│── models/
│   ├── Customer.js
│   ├── Order.js
│   ├── Campaign.js
│   └── CommunicationLog.js
│── utils/
│   └── ai.js (optional)
│── middleware/
│   └── isAuthenticated.js
│── server.js
└── .env

💡 Future Enhancements

Add React frontend

Add more audience filtering rules:

max_spend

recent_signup

email domain

Implement AI-powered campaign generation

Add analytics dashboard

Add role-based access (admin / staff)
