 🏠 HomeTrack - Dignified Housing for All Ghanaians

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Ghana Data Protection Act Compliant](https://img.shields.io/badge/GDPA-Compliant-green.svg)](https://www.dataprotection.org.gh/)
[![Mobile Money Integration](https://img.shields.io/badge/MoMo-Integrated-orange.svg)](https://www.mtn.com.gh/personal/mobile-money)
[![Accessibility](https://img.shields.io/badge/A11y-WCAG%202.1%20AA-blue.svg)](https://www.w3.org/WAI/WCAG21/AA/)

> **Your path to dignified housing starts here.** Build credit, save smart, and unlock housing opportunities through alternative data and partner vouchers.

## 🌟 Overview

HomeTrack is a comprehensive housing finance platform designed specifically for Ghana's unique market. We help users build credit scores using alternative data (Mobile Money, utility bills, SSNIT contributions), set and track housing goals, and access partner vouchers to reduce housing costs.

### 🎯 Key Features

- **🏠 Housing Goal Tracking** - Set realistic goals with smart progress calculation
- **📈 Alternative Credit Scoring** - Build credit using MoMo, bills, and rent history  
- **🎫 Partner Voucher System** - Redeem benefits from MTN, Government, and NGOs
- **🌍 Diaspora Investment Portal** - Connect with family abroad for co-investment
- **♿ Accessibility First** - Voice assistance, high contrast, large text support
- **🔒 Security & Privacy** - Ghana Data Protection Act compliant with AES-256 encryption

## 🚀 Quick Start

### Prerequisites

- Node.js 16+ or any modern web server
- Modern web browser with JavaScript enabled
- (Optional) HTTPS certificate for production deployment

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/hometrack.git
cd hometrack

# For development - serve the HTML file
npx serve .

# Or use any web server
python -m http.server 8000

Demo Access

Visit the live demo: 
https://hometrack-demo.vercel.app

Test Credentials:

Phone: 
0244123456
 (any valid Ghana number format)
MoMo OTP: 
123456
 (demo mode)
Voucher PIN: 
1234
 (demo mode)
🛠️ Developer Integration Guide

🔧 API Endpoints

Authentication & User Management

POST /auth/login              // User authentication
POST /user/profile            // Create user profile  
PATCH /user/profile           // Update user profile
GET /user/profile             // Get user profile

Credit Score Management

GET /credit/score             // Get current credit score
POST /credit/refresh          // Trigger score recalculation
POST /integrations/momo/link  // Link MoMo account
POST /integrations/momo/send-otp // Send MoMo OTP

Housing Goals

POST /housing/goals           // Set housing goal
GET /housing/goals            // Get housing progress
PATCH /housing/goals          // Update housing goal

Voucher Management

GET /vouchers/available       // Get available vouchers
POST /vouchers/redeem         // Redeem voucher (requires PIN)
GET /vouchers/history         // Get redemption history

🔐 Security Implementation

Data Encryption

// Example: Encrypt sensitive data before API calls
const encryptedData = SecurityManager.encryptSensitiveData({
  momoNumber: "0244123456",
  ghanaCardNumber: "GHA-123456789-1"
});

// API call with encrypted payload
await APIService.request('/user/profile', {
  method: 'POST',
  body: JSON.stringify({ profile: encryptedData })
});

Ghana Data Protection Act Compliance

// Ensure user consent before data processing
const consent = localStorage.getItem('ht_data_consent');
if (!consent) {
  // Show consent dialog
  const userConsent = await showConsentDialog();
  if (userConsent) {
    localStorage.setItem('ht_data_consent', 'granted');
    localStorage.setItem('ht_consent_date', new Date().toISOString());
  }
}

PIN Verification for Vouchers

// Secure voucher redemption with PIN
async function redeemVoucher(voucherId, pin) {
  // Validate PIN format
  if (!/^\d{4}$/.test(pin)) {
    throw new Error('Invalid PIN format');
  }
  
  // Hash PIN before sending (use bcrypt in production)
  const hashedPin = await bcrypt.hash(pin, 12);
  
  return APIService.request('/vouchers/redeem', {
    method: 'POST',
    body: JSON.stringify({ 
      voucher_id: voucherId, 
      pin_hash: hashedPin 
    })
  });
}

📊 Database Schema

Users Table

CREATE TABLE users (
  user_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  full_name TEXT ENCRYPTED NOT NULL,
  phone_number TEXT ENCRYPTED NOT NULL,
  ghana_card_number TEXT ENCRYPTED,
  location VARCHAR(100),
  income_range VARCHAR(50),
  momo_provider VARCHAR(50),
  verification_status JSONB DEFAULT '{}',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

Housing Goals Table

CREATE TABLE housing_goals (
  goal_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(user_id),
  preferred_location VARCHAR(100),
  housing_type VARCHAR(50),
  monthly_budget INTEGER,
  timeline VARCHAR(50),
  progress_percentage INTEGER DEFAULT 0,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

Credit Scores Table

CREATE TABLE credit_scores (
  score_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(user_id),
  score_value INTEGER,
  payment_history INTEGER,
  financial_stability INTEGER,
  housing_readiness INTEGER,
  data_sources JSONB,
  calculated_at TIMESTAMP DEFAULT NOW()
);

Vouchers Table

CREATE TABLE vouchers (
  voucher_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(user_id),
  partner_name VARCHAR(100),
  amount INTEGER,
  voucher_type VARCHAR(50),
  status VARCHAR(20) DEFAULT 'available',
  redeemed_at TIMESTAMP,
  expires_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW()
);

Audit Logs Table

CREATE TABLE audit_logs (
  log_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(user_id),
  action_type VARCHAR(100),
  resource_id UUID,
  ip_address INET,
  user_agent TEXT,
  timestamp TIMESTAMP DEFAULT NOW()
);

🤝 Partner Integrations

MTN Mobile Money Integration

// MTN MoMo API Configuration
const MTN_CONFIG = {
  baseURL: 'https://sandbox.momodeveloper.mtn.com',
  subscriptionKey: process.env.MTN_SUBSCRIPTION_KEY,
  environment: process.env.NODE_ENV === 'production' ? 'live' : 'sandbox'
};

// Link MoMo account with OTP verification
async function linkMTNMoMo(phoneNumber) {
  const response = await fetch(`${MTN_CONFIG.baseURL}/collection/v1_0/requesttopay`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${await getMTNAccessToken()}`,
      'X-Reference-Id': generateUUID(),
      'X-Target-Environment': MTN_CONFIG.environment,
      'Ocp-Apim-Subscription-Key': MTN_CONFIG.subscriptionKey
    },
    body: JSON.stringify({
      amount: "0",
      currency: "GHS",
      externalId: generateExternalId(),
      payer: {
        partyIdType: "MSISDN",
        partyId: phoneNumber
      },
      payerMessage: "HomeTrack account verification",
      payeeNote: "Account linking verification"
    })
  });
  
  return response.json();
}

Ghana Card System Integration

// Ghana Card API Integration (Hypothetical - actual API may differ)
async function verifyGhanaCard(cardNumber) {
  const response = await fetch('https://api.nia.gov.gh/verify', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.NIA_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      cardNumber: cardNumber,
      purpose: 'financial_services_verification'
    })
  });
  
  if (response.ok) {
    const data = await response.json();
    return {
      verified: data.status === 'valid',
      fullName: data.fullName,
      dateOfBirth: data.dateOfBirth
    };
  }
  
  throw new Error('Ghana Card verification failed');
}

Credit Bureau Integration (myCreditScore)

// myCreditScore API Integration
async function getMyCreditScore(userId) {
  const response = await fetch('https://api.mycreditscore.com.gh/v1/score', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.MYCREDITSCORE_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      userId: userId,
      dataTypes: ['utility_bills', 'ssnit_contributions', 'bank_statements']
    })
  });
  
  return response.json();
}

🌍 Accessibility Features

Voice Assistant Integration

// Voice commands for low-literacy users
const voiceCommands = {
  'start': () => startOnboarding(),
  'show credit score': () => showSection('creditBuilder'),
  'track goal': () => showSection('housingGoal'),
  'redeem voucher': () => showSection('vouchers'),
  'help': () => speak('Available commands: start, show credit score, track goal, redeem voucher')
};

// Speech recognition setup
if ('webkitSpeechRecognition' in window) {
  const recognition = new webkitSpeechRecognition();
  recognition.lang = 'en-GH'; // Ghana English
  recognition.onresult = (event) => {
    const command = event.results[0][0].transcript.toLowerCase();
    if (voiceCommands[command]) {
      voiceCommands[command]();
    }
  };
}

Multi-language Support

// Language translations for local languages
const translations = {
  'tw': { // Twi
    'Get Started': 'Fi Ase',
    'Credit Score': 'Credit Score',
    'Housing Goal': 'Ofi Botae'
  },
  'ga': { // Ga
    'Get Started': 'Bɛɛ',
    'Credit Score': 'Credit Score',
    'Housing Goal': 'Ofi Goal'
  },
  'ew': { // Ewe
    'Get Started': 'Dze Egɔme',
    'Credit Score': 'Credit Score',
    'Housing Goal': 'Aƒe Taɖodzinu'
  }
};

🚀 Deployment Guide

Production Environment Setup

# Environment variables
export NODE_ENV=production
export DATABASE_URL=postgresql://user:pass@localhost/hometrack
export REDIS_URL=redis://localhost:6379
export MTN_SUBSCRIPTION_KEY=your_mtn_key
export MYCREDITSCORE_API_KEY=your_credit_api_key
export ENCRYPTION_KEY=your_32_byte_encryption_key
export JWT_SECRET=your_jwt_secret

Docker Deployment

FROM node:16-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
EXPOSE 3000

CMD ["npm", "start"]

Kubernetes Configuration

apiVersion: apps/v1
kind: Deployment
metadata:
  name: hometrack-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hometrack-api
  template:
    metadata:
      labels:
        app: hometrack-api
    spec:
      containers:
      - name: hometrack-api
        image: hometrack/api:latest
        ports:
        - containerPort: 3000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: hometrack-secrets
              key: database-url

Security Checklist

[ ] HTTPS/TLS 1.3 enabled
[ ] Database encryption at rest
[ ] API rate limiting (100 req/min per user)
[ ] Input validation and sanitization
[ ] CORS properly configured
[ ] Security headers implemented
[ ] Regular security audits scheduled
[ ] Backup and disaster recovery tested
📱 Mobile App Integration

Progressive Web App (PWA) Setup

// Service Worker for offline functionality
self.addEventListener('fetch', (event) => {
  if (event.request.url.includes('/api/')) {
    event.respondWith(
      fetch(event.request)
        .catch(() => caches.match('/offline-fallback.json'))
    );
  }
});

// App manifest for mobile installation
{
  "name": "HomeTrack",
  "short_name": "HomeTrack",
  "description": "Your path to dignified housing",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#667eea",
  "theme_color": "#4f46e5",
  "icons": [
    {
      "src": "/icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    }
  ]
}

🧪 Testing

Unit Tests

# Run unit tests
npm test

# Run with coverage
npm run test:coverage

# Run integration tests
npm run test:integration

API Testing

// Example API test
describe('Credit Score API', () => {
  test('should calculate credit score correctly', async () => {
    const mockUser = {
      momoTransactions: 50,
      utilityPayments: 12,
      ssnit: true
    };
    
    const score = await calculateCreditScore(mockUser);
    expect(score).toBeGreaterThan(600);
    expect(score).toBeLessThan(850);
  });
});

📊 Monitoring & Analytics

Performance Monitoring

// Performance tracking
const performanceObserver = new PerformanceObserver((list) => {
  list.getEntries().forEach((entry) => {
    if (entry.entryType === 'navigation') {
      analytics.track('page_load_time', {
        duration: entry.loadEventEnd - entry.loadEventStart,
        page: window.location.pathname
      });
    }
  });
});

performanceObserver.observe({ entryTypes: ['navigation'] });

Error Tracking

// Error monitoring with Sentry
import * as Sentry from '@sentry/browser';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  beforeSend(event) {
    // Filter out sensitive data
    if (event.extra && event.extra.momoNumber) {
      delete event.extra.momoNumber;
    }
    return event;
  }
});

🤝 Contributing

We welcome contributions! Please see our 
CONTRIBUTING.md
 for details.

Development Setup

# Fork and clone the repository
git clone https://github.com/your-username/hometrack.git
cd hometrack

# Install dependencies
npm install

# Start development server
npm run dev

# Run tests
npm test

Code Style

We use Prettier and ESLint for code formatting:

# Format code
npm run format

# Lint code
npm run lint

📄 License

This project is licensed under the MIT License - see the 
LICENSE
 file for details.

🆘 Support

Documentation
: 
https://docs.hometrack.gh
Community
: 
https://discord.gg/hometrack
Issues
: 
https://github.com/your-org/hometrack/issues
Email
: support@hometrack.gh
Phone
: +233 123 456 789
🙏 Acknowledgments

Ghana Government
 - For housing policy support
MTN Ghana
 - Mobile Money integration partnership
myCreditScore
 - Credit bureau data partnership
Ghana Tech Community
 - For development support
Housing Finance Partners
 - For voucher programs
🗺️ Roadmap

Q1 2024

[ ] Full MTN MoMo API integration
[ ] Ghana Card verification system
[ ] Credit bureau partnerships
[ ] Mobile app launch (iOS/Android)
Q2 2024

[ ] Vodafone Cash integration
[ ] Property marketplace integration
[ ] Landlord portal
[ ] Advanced analytics dashboard
Q3 2024

[ ] International remittance integration
[ ] Blockchain-based property records
[ ] AI-powered housing recommendations
[ ] Expansion to other West African countries
Made with ❤️ for Ghana's housing future

HomeTrack is committed to making dignified housing accessible to all Ghanaians through technology, partnerships, and financial inclusion.
