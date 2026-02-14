# AI Saathi - Design Document

## 1. SYSTEM ARCHITECTURE

```
┌─────────────────────────────────────────────────────────────┐
│                    USER CHANNELS                            │
├─────────────────────────────────────────────────────────────┤
│  WhatsApp  │  Phone IVR  │  PWA Web  │  SMS  │  USSD       │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│              API GATEWAY (Node.js/Express)                  │
├─────────────────────────────────────────────────────────────┤
│  • Rate Limiting  • Authentication  • Load Balancing        │
│  • Request Routing  • Logging  • Error Handling             │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│              VOICE PROCESSING LAYER                         │
├─────────────────────────────────────────────────────────────┤
│  Bhashini ASR → Claude API (NLU) → Bhashini TTS            │
│  • Speech-to-Text  • Intent Recognition  • Text-to-Speech  │
│  • Context Management  • Fallback to Google Speech          │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│              BUSINESS LOGIC LAYER                           │
├─────────────────────────────────────────────────────────────┤
│  • Scheme Matching Engine (Eligibility Algorithm)          │
│  • User Profile Manager (CRUD + Validation)                 │
│  • Conversation Manager (State Machine)                     │
│  • Application Tracker (Status + Reminders)                 │
│  • Notification Service (SMS/WhatsApp)                      │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                   DATA LAYER                                │
├─────────────────────────────────────────────────────────────┤
│  PostgreSQL (User + Schemes)  │  Redis Cache  │  S3 Storage │
│  • User Profiles  • Conversations  • Audio Files            │
│  • Scheme Database  • Application Logs                      │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│              EXTERNAL INTEGRATIONS                          │
├─────────────────────────────────────────────────────────────┤
│  MyScheme API  │  Bhashini  │  Twilio  │  Exotel  │  AWS   │
└─────────────────────────────────────────────────────────────┘
```

### Architecture Principles
- **Microservices-ready:** Modular design for future scaling
- **API-first:** All functionality exposed via REST APIs
- **Stateless:** Session state in Redis, not in-memory
- **Fault-tolerant:** Graceful degradation, fallback mechanisms
- **Observable:** Comprehensive logging and monitoring

---

## 2. TECH STACK

### Frontend
- **PWA Framework:** Next.js 14 (React 18)
- **UI Library:** Tailwind CSS + shadcn/ui
- **State Management:** React Context + SWR
- **Offline Support:** Service Workers + IndexedDB
- **Audio Recording:** RecordRTC
- **Mobile-first:** Responsive design, touch-optimized

### Backend
- **Runtime:** Node.js 18 LTS
- **Framework:** Express.js 4.18
- **API Style:** RESTful + WebSockets (for real-time)
- **Validation:** Zod
- **Authentication:** JWT + OTP
- **Job Queue:** Bull (Redis-based)

### Voice Processing
- **ASR (Primary):** Bhashini API (Hindi)
- **ASR (Backup):** Google Speech-to-Text
- **TTS:** Bhashini API (Hindi voices)
- **NLU:** Claude 3.5 Sonnet (Anthropic)
- **Audio Format:** MP3/WAV, 16kHz, mono

### Database
- **Primary DB:** PostgreSQL 15
- **Cache:** Redis 7
- **File Storage:** AWS S3 / DigitalOcean Spaces
- **Backup:** Daily automated backups
- **ORM:** Prisma

### Messaging & Communication
- **WhatsApp:** Twilio API / Gupshup
- **IVR:** Exotel / Knowlarity
- **SMS:** Twilio / MSG91
- **Email:** SendGrid (for notifications)

### Hosting & Infrastructure
- **Cloud Provider:** AWS Mumbai / DigitalOcean Bangalore
- **CDN:** Cloudflare
- **Container:** Docker
- **Orchestration:** Docker Compose (MVP), Kubernetes (future)
- **CI/CD:** GitHub Actions
- **Monitoring:** Datadog / CloudWatch
- **Error Tracking:** Sentry
- **Uptime:** UptimeRobot

---

## 3. DATA MODELS

### User Profile
```json
{
  "user_id": "uuid",
  "phone": "+91XXXXXXXXXX",
  "name": "string",
  "age": 45,
  "gender": "male|female|other",
  "location": {
    "state": "Uttar Pradesh",
    "district": "Varanasi",
    "block": "Pindra",
    "village": "Rampur",
    "pincode": "221206"
  },
  "economic": {
    "annual_income": 80000,
    "bpl_card": true,
    "bpl_card_number": "string",
    "occupation": "farmer|laborer|self_employed|unemployed|other",
    "land_holding": 2.5
  },
  "family": {
    "marital_status": "married|unmarried|widow|widower|divorced",
    "dependents": 3,
    "children_count": 2,
    "disabled_members": 0
  },
  "categories": ["SC", "BPL"],
  "documents": {
    "aadhaar_last4": "1234",
    "ration_card": true,
    "bank_account": true
  },
  "language_preference": "hi",
  "consent_given": true,
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-01-20T14:22:00Z",
  "last_active": "2024-01-25T09:15:00Z"
}
```

### Scheme
```json
{
  "scheme_id": "PM-KISAN-2024",
  "name_hindi": "प्रधानमंत्री किसान सम्मान निधि",
  "name_english": "PM-KISAN",
  "department": "Ministry of Agriculture",
  "category": "agriculture|health|education|pension|housing|other",
  "benefit_type": "cash|subsidy|loan|service|goods",
  "benefit_amount": 6000,
  "benefit_frequency": "annual|monthly|one_time",
  "description_hindi": "किसानों को हर साल ₹6,000 की आर्थिक सहायता",
  "description_english": "Financial assistance of ₹6,000 per year to farmers",
  "eligibility_criteria": {
    "age_min": 18,
    "age_max": null,
    "gender": "any",
    "income_max": null,
    "occupation": ["farmer"],
    "land_holding_max": 2,
    "categories": ["general", "SC", "ST", "OBC"],
    "state_specific": false,
    "states": ["all"],
    "other_conditions": [
      "Must own agricultural land",
      "Aadhaar linked to bank account"
    ]
  },
  "required_documents": [
    "Aadhaar card",
    "Bank passbook",
    "Land ownership papers"
  ],
  "application_process": {
    "method": "online|offline|both",
    "url": "https://pmkisan.gov.in",
    "steps_hindi": ["Step 1", "Step 2"],
    "steps_english": ["Step 1", "Step 2"]
  },
  "contact": {
    "helpline": "155261",
    "email": "pmkisan-ict@gov.in"
  },
  "active": true,
  "last_updated": "2024-01-10T00:00:00Z"
}
```

### Conversation
```json
{
  "conversation_id": "uuid",
  "user_id": "uuid",
  "channel": "whatsapp|ivr|web|sms",
  "status": "active|completed|abandoned",
  "messages": [
    {
      "message_id": "uuid",
      "role": "user|assistant",
      "content_text": "Mujhe kisan yojana chahiye",
      "content_audio_url": "s3://bucket/audio/xyz.mp3",
      "intent": "scheme_search",
      "entities": {
        "scheme_category": "agriculture"
      },
      "timestamp": "2024-01-25T10:30:15Z"
    }
  ],
  "context": {
    "current_step": "profile_building",
    "profile_completion": 60,
    "matched_schemes": ["PM-KISAN-2024"],
    "last_intent": "scheme_search"
  },
  "metadata": {
    "device_type": "mobile",
    "network_quality": "3g",
    "language": "hi"
  },
  "created_at": "2024-01-25T10:30:00Z",
  "updated_at": "2024-01-25T10:35:00Z"
}
```

### Application Tracker
```json
{
  "tracker_id": "uuid",
  "user_id": "uuid",
  "scheme_id": "PM-KISAN-2024",
  "status": "interested|documents_pending|applied|approved|rejected",
  "documents_checklist": {
    "aadhaar": true,
    "bank_passbook": true,
    "land_papers": false
  },
  "application_date": "2024-01-26T00:00:00Z",
  "application_id": "string",
  "notes": "User needs help with land papers",
  "reminders": [
    {
      "reminder_date": "2024-01-30T10:00:00Z",
      "message": "Land papers pending",
      "sent": false
    }
  ],
  "created_at": "2024-01-25T11:00:00Z",
  "updated_at": "2024-01-25T11:00:00Z"
}
```

---

## 4. API ENDPOINTS

### Voice Interface APIs
```
POST /api/voice/input
Body: { audio: File, user_id: string, conversation_id?: string }
Returns: { 
  transcribed_text: string,
  ai_response_text: string,
  ai_response_audio_url: string,
  conversation_id: string
}

POST /api/voice/text
Body: { text: string, user_id: string, conversation_id?: string }
Returns: {
  ai_response_text: string,
  ai_response_audio_url: string,
  conversation_id: string
}

GET /api/voice/audio/:audio_id
Returns: Audio file (MP3)
```

### Scheme APIs
```
GET /api/schemes/match
Query: user_id=string
Returns: {
  matched_schemes: Scheme[],
  eligibility_scores: { scheme_id: number }[]
}

GET /api/schemes/:scheme_id
Returns: Scheme

GET /api/schemes/search
Query: category=string, state=string, keyword=string
Returns: Scheme[]

POST /api/schemes/apply-guide
Body: { scheme_id: string, user_id: string }
Returns: {
  steps: string[],
  required_documents: string[],
  application_url: string,
  nearest_centers: Location[]
}

GET /api/schemes/categories
Returns: { categories: string[] }
```

### User APIs
```
POST /api/users/create
Body: { phone: string, name?: string }
Returns: { user_id: string, otp_sent: boolean }

POST /api/users/verify-otp
Body: { phone: string, otp: string }
Returns: { token: string, user: User }

GET /api/users/:user_id
Headers: Authorization: Bearer <token>
Returns: User

PUT /api/users/:user_id
Headers: Authorization: Bearer <token>
Body: Partial<User>
Returns: User

DELETE /api/users/:user_id
Headers: Authorization: Bearer <token>
Returns: { success: boolean }
```

### Conversation APIs
```
GET /api/conversations/:conversation_id
Returns: Conversation

GET /api/conversations/user/:user_id
Returns: Conversation[]

POST /api/conversations/create
Body: { user_id: string, channel: string }
Returns: Conversation

PUT /api/conversations/:conversation_id/status
Body: { status: string }
Returns: Conversation
```

### Application Tracker APIs
```
POST /api/tracker/create
Body: { user_id: string, scheme_id: string }
Returns: ApplicationTracker

GET /api/tracker/user/:user_id
Returns: ApplicationTracker[]

PUT /api/tracker/:tracker_id
Body: Partial<ApplicationTracker>
Returns: ApplicationTracker

POST /api/tracker/:tracker_id/reminder
Body: { reminder_date: string, message: string }
Returns: { success: boolean }
```

---

## 5. CONVERSATION FLOW

### Step 1: Initial Greeting
```
User: [Opens WhatsApp / Calls / Opens Web]

AI: "Namaste! Main AI Saathi hoon. Main aapko sarkari yojana 
     ke bare mein bataunga. Aap Hindi mein baat kar sakte hain.
     
     Aapka naam kya hai?"

User: "Mera naam Ramesh hai"

AI: "Dhanyavaad Ramesh ji. Main aapko sahi yojana batane ke 
     liye kuch sawal poochunga. Bas 2-3 minute lagenge."
```

### Step 2: Profile Building (5-10 Questions)
```
AI: "Aapki umar kya hai?"
User: "45 saal"

AI: "Aap kya kaam karte hain? Jaise - kisan, mazdoor, ya koi 
     aur kaam?"
User: "Main kisan hoon"

AI: "Aapke paas kitni zameen hai? Acre mein batayein"
User: "2 acre"

AI: "Aapki saalana income kitni hai? Andazan batayein"
User: "80 hazar rupaye"

AI: "Kya aapke paas BPL card hai?"
User: "Haan hai"

AI: "Aap kahan rehte hain? Gaon aur district ka naam?"
User: "Rampur gaon, Varanasi"

AI: "Aapke parivar mein kitne log hain?"
User: "5 log - main, meri patni, aur 3 bachche"

AI: "Bahut achha. Ab main aapke liye sahi yojana dhundh raha hoon..."
```

### Step 3: Scheme Matching & Presentation
```
AI: "Ramesh ji, aapke liye 5 yojana suitable hain:

     1. PM-KISAN: ₹6,000 har saal, 3 installments mein
     2. Fasal Bima Yojana: 90% premium subsidy
     3. KCC Loan: 4% interest par loan
     4. Soil Health Card: Muft mitti ki janch
     5. Muft beej yojana: Sarkari beej muft
     
     Sabse pehle kis yojana ke bare mein janana chahenge?"

User: "PM-KISAN"
```

### Step 4: Detailed Scheme Information
```
AI: "PM-KISAN ek bahut achhi yojana hai. Isme aapko har saal 
     ₹6,000 milte hain, 3 baar mein - ₹2,000 har 4 mahine mein.
     
     Yeh paise seedha aapke bank account mein aayenge.
     
     Zaruri documents:
     1. Aadhaar card
     2. Bank passbook (Aadhaar se linked)
     3. Zameen ke kagaz (ownership proof)
     
     Kya aapke paas yeh sab documents hain?"

User: "Aadhaar aur bank passbook hai, par zameen ke kagaz 
       nahi mil rahe"

AI: "Koi baat nahi. Zameen ke kagaz ke liye aap:
     1. Tehsil office jaiye
     2. Ya patwari se miliye
     3. Ya online land records dekh sakte hain
     
     Kya main aapko online land records kaise dekhen, 
     yeh bataun?"
```

### Step 5: Application Guidance
```
User: "Haan batayein"

AI: "Application 3 tarike se kar sakte hain:
     
     OPTION 1 - Online (agar smartphone hai):
     1. pmkisan.gov.in website kholein
     2. 'Farmer Corner' par click karein
     3. 'New Farmer Registration' select karein
     4. Aadhaar number dalein
     5. Details bharein
     6. Documents upload karein
     
     OPTION 2 - CSC Center (sabse aasan):
     Aapke paas ka CSC center: Ram CSC, Main Road, Rampur
     Phone: 9876543210
     Wahan 30-50 rupaye mein application ho jayegi
     
     OPTION 3 - Gram Panchayat:
     Apne gram panchayat office mein VLE se madad le sakte hain
     
     Kaunsa tarika aapko theek lagta hai?"

User: "Main CSC center jaunga"

AI: "Bahut achha! Main aapko ek reminder bhej dunga 3 din baad.
     
     Kya aapko kisi aur yojana ke bare mein janana hai?"
```

### Step 6: Follow-up & Reminders
```
[3 days later - WhatsApp message]

AI: "Namaste Ramesh ji! Kya aapne PM-KISAN ke liye apply 
     kar diya?
     
     Agar koi problem hai toh batayein, main madad karunga."

User: "Haan apply kar diya, thank you"

AI: "Bahut badhiya! Application number note kar lena.
     
     Status check karne ke liye:
     - pmkisan.gov.in par 'Beneficiary Status' dekh sakte hain
     - Ya helpline 155261 par call karein
     
     Agar aur koi yojana chahiye toh mujhe message karein!"
```

---

## 6. SECURITY DESIGN

### Data Protection
- **Encryption at Rest:** AES-256 for database
- **Encryption in Transit:** TLS 1.3 for all API calls
- **Password Hashing:** bcrypt (if passwords used)
- **Aadhaar Masking:** Store only last 4 digits
- **Audio Files:** Encrypted in S3, auto-delete after 30 days
- **PII Handling:** Minimal collection, pseudonymization where possible

### Authentication & Authorization
- **OTP-based Login:** 6-digit OTP via SMS
- **JWT Tokens:** 24-hour expiry, refresh tokens for 30 days
- **Rate Limiting:** 
  - 10 requests/minute per user
  - 100 requests/minute per IP
  - 1000 OTP requests/day per phone
- **API Keys:** For internal services, rotated monthly
- **Role-based Access:** User, Admin, Support roles (future)

### Privacy & Compliance
- **Consent Management:** Explicit opt-in for data collection
- **Data Minimization:** Collect only necessary information
- **Right to Delete:** Users can request data deletion
- **Anonymized Analytics:** No PII in analytics data
- **Audit Logs:** All data access logged
- **Aadhaar Act Compliance:** 
  - No storage of Aadhaar XML
  - No Aadhaar in logs
  - Masked display only
- **GDPR-like Standards:** Even though not EU, follow best practices

### Security Monitoring
- **Intrusion Detection:** AWS GuardDuty / Fail2ban
- **Vulnerability Scanning:** Weekly automated scans
- **Penetration Testing:** Quarterly external audits
- **DDoS Protection:** Cloudflare
- **WAF:** Web Application Firewall rules
- **Security Headers:** HSTS, CSP, X-Frame-Options

---

## 7. DEPLOYMENT ARCHITECTURE

### Environment Setup
```
Development → Staging → Production

Development:
- Local Docker Compose
- PostgreSQL + Redis containers
- Mock external APIs
- Hot reload enabled

Staging:
- AWS EC2 t3.small (1 instance)
- RDS PostgreSQL db.t3.micro
- ElastiCache Redis
- Real API integrations
- Staging domain: staging.aisaathi.in

Production:
- AWS EC2 t3.large (2 instances)
- Application Load Balancer
- RDS PostgreSQL db.t3.medium (Multi-AZ)
- ElastiCache Redis (cluster mode)
- S3 for audio files
- CloudFront CDN
- Route 53 DNS
- Production domain: aisaathi.in
```

### Infrastructure as Code
```yaml
# docker-compose.yml (simplified)
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/aisaathi
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
  
  db:
    image: postgres:15
    volumes:
      - postgres_data:/var/lib/postgresql/data
  
  redis:
    image: redis:7
    volumes:
      - redis_data:/data
```

### CI/CD Pipeline
```
GitHub Push → GitHub Actions → Build → Test → Deploy

Steps:
1. Lint & Format check
2. Unit tests
3. Integration tests
4. Build Docker image
5. Push to ECR
6. Deploy to staging (auto)
7. Run smoke tests
8. Deploy to production (manual approval)
9. Health check
10. Rollback if failed
```

### Monitoring & Observability
```
Logs:
- Application: Winston → CloudWatch Logs
- Access: Nginx logs → S3
- Error: Sentry (real-time alerts)

Metrics:
- System: CPU, Memory, Disk (CloudWatch)
- Application: Response time, Error rate (Datadog)
- Business: User signups, Conversations (Mixpanel)

Alerts:
- Error rate > 5%: PagerDuty
- Response time > 5s: Slack
- Uptime < 99%: Email + SMS
- API failures: Slack

Dashboards:
- System health (Datadog)
- User analytics (Mixpanel)
- Business metrics (Metabase)
```

---

## 8. COST ESTIMATION

### Monthly Costs (10K Users)

#### API Costs
```
Bhashini API: ₹0 (Free for government projects)
Claude API: ₹20,000
  - 10K users × 2 conversations/month × 10 messages
  - 200K messages × ₹0.10/message
Twilio WhatsApp: ₹15,000
  - 10K users × 15 messages/month × ₹0.10/message
Exotel IVR: ₹10,000
  - 1000 calls × 5 min avg × ₹2/min
SMS (MSG91): ₹3,000
  - 10K OTPs × ₹0.20/SMS
  - 5K reminders × ₹0.20/SMS
```

#### Infrastructure Costs
```
AWS EC2 (2× t3.large): ₹6,000
RDS PostgreSQL (db.t3.medium): ₹4,000
ElastiCache Redis: ₹2,000
S3 Storage (audio files): ₹1,000
CloudFront CDN: ₹1,500
Load Balancer: ₹1,500
Route 53: ₹500
```

#### Tools & Services
```
Datadog Monitoring: ₹2,000
Sentry Error Tracking: ₹1,000
UptimeRobot: ₹500
SendGrid Email: ₹500
GitHub: ₹0 (Free tier)
Domain & SSL: ₹200
```

#### Total Monthly Cost
```
API Costs:        ₹48,000
Infrastructure:   ₹16,500
Tools:            ₹4,200
──────────────────────────
TOTAL:            ₹68,700/month
Per User:         ₹6.87/user/month
```

### 6-Month Budget Breakdown
```
Development (2 months):     ₹6,00,000
API Costs (6 months):       ₹2,88,000
Infrastructure (6 months):  ₹99,000
Tools (6 months):           ₹25,200
Testing & QA:               ₹1,50,000
Marketing (pilot):          ₹50,000
Contingency (15%):          ₹1,87,800
────────────────────────────────────
TOTAL:                      ₹15,00,000
```

---

## 9. RISKS & MITIGATION

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Bhashini API downtime | High | Medium | Backup: Google Speech API, cache common responses |
| Claude API rate limits | High | Medium | Queue system, Redis cache, fallback to rule-based |
| Poor 2G network quality | High | High | Offline mode, SMS fallback, audio compression |
| Wrong scheme information | High | Low | Daily sync with MyScheme, manual review, user feedback |
| User data leak | Critical | Low | Encryption, security audits, minimal data collection |
| Low user adoption | High | Medium | Pilot in 5 villages, iterate based on feedback |
| High API costs | Medium | Medium | Optimize calls, cache aggressively, negotiate pricing |
| Voice recognition errors | Medium | High | Allow text input, confirmation prompts, error handling |
| Scheme database outdated | Medium | Medium | Weekly automated sync, manual quarterly review |
| Infrastructure failure | High | Low | Multi-AZ deployment, automated backups, disaster recovery |
| Regulatory compliance | High | Low | Legal review, Aadhaar compliance, privacy policy |
| Team bandwidth | Medium | Medium | Prioritize MVP features, outsource non-core tasks |

---

## 10. FUTURE ENHANCEMENTS (Post-MVP)

### Phase 2 (Month 7-12)
- Multi-language support (Tamil, Telugu, Bengali)
- Document upload and OCR
- Aadhaar eKYC integration
- DigiLocker integration
- Admin dashboard for NGOs/government
- Real-time application status tracking
- Video tutorials in Hindi

### Phase 3 (Year 2)
- AI-powered document verification
- Grievance redressal system
- Community forum
- Scheme comparison tool
- Personalized recommendations using ML
- Integration with all state portals
- Mobile app (Android/iOS)

### Scalability Roadmap
- Kubernetes for orchestration
- Microservices architecture
- Multi-region deployment
- Edge computing for voice processing
- GraphQL API
- Real-time analytics dashboard

---

## 11. TESTING STRATEGY

### Unit Testing
- Jest for backend (80%+ coverage)
- React Testing Library for frontend
- Mock external APIs

### Integration Testing
- API endpoint testing (Supertest)
- Database integration tests
- External API integration tests

### End-to-End Testing
- Playwright for web flows
- WhatsApp bot testing (manual + automated)
- IVR testing (manual)

### Performance Testing
- Load testing (Artillery.io)
- Stress testing (1000 concurrent users)
- Voice processing latency tests

### Security Testing
- OWASP Top 10 checks
- Penetration testing (quarterly)
- Dependency vulnerability scans

### User Acceptance Testing
- Pilot with 100 users in 2 villages
- Feedback collection
- Iterate based on real usage

---

## 12. SUCCESS CRITERIA

### Technical Success
- 99%+ uptime
- < 3 second response time (p95)
- 90%+ voice recognition accuracy
- < 2% error rate in scheme matching
- Zero critical security vulnerabilities

### User Success
- 4/5 satisfaction score
- 60%+ monthly active rate
- < 20% conversation abandonment
- 70%+ task completion rate

### Business Success
- 10K users in 6 months
- < ₹7 per user per month cost
- 5K scheme applications guided
- ₹5 crore benefits accessed
- Sustainable unit economics
