# AI Saathi - Requirements Document

## 1. PROJECT OVERVIEW

**Project Name:** AI Saathi

**One-line Description:** Voice-first AI assistant that helps rural Indians discover and apply for government schemes in Hindi.

**Problem Statement:**
India has 500+ government welfare schemes, but 70% of eligible beneficiaries never access them due to lack of awareness, language barriers, and complex application processes. Rural citizens, especially women, elderly, and marginally literate populations, struggle to navigate digital portals designed for urban, English-speaking users.

**Solution Summary:**
AI Saathi is a conversational AI platform accessible via WhatsApp, phone calls, and web that speaks Hindi, asks simple questions to understand user needs, matches them with relevant schemes, and provides step-by-step application guidance. It eliminates the need for literacy or technical knowledge, making government benefits accessible to everyone.

**Target Users:**
- Rural women (widows, pregnant women, homemakers)
- Small and marginal farmers
- Daily wage workers and laborers
- Senior citizens seeking pensions
- Students from economically weaker sections
- Persons with disabilities
- BPL card holders
- Illiterate or semi-literate populations

**Geographic Scope (Phase 1):**
- Primary: Uttar Pradesh, Bihar, Madhya Pradesh
- Language: Hindi only
- Expansion: 5 more states by Month 12

---

## 2. USER STORIES

### Rural Widow
**As a** rural widow,  
**I want to** ask in Hindi "Mujhe kya schemes milegi",  
**So that** I can discover pension schemes I'm eligible for.

### Small Farmer
**As a** small farmer with 2 acres of land,  
**I want to** know which agricultural schemes provide financial support,  
**So that** I can reduce my farming costs and increase income.

### Daily Wage Worker
**As a** daily wage worker earning ₹8,000/month,  
**I want to** find schemes for affordable housing and ration cards,  
**So that** I can improve my family's living conditions.

### Pregnant Woman
**As a** pregnant woman in a village,  
**I want to** learn about maternity benefits and nutrition schemes,  
**So that** I can ensure safe delivery and child health.

### Scholarship Seeker
**As a** 12th-grade student from a poor family,  
**I want to** discover scholarship opportunities for college,  
**So that** I can continue my education without financial burden.

### Disabled Person
**As a** person with 40% disability,  
**I want to** know what disability pensions and benefits I qualify for,  
**So that** I can gain financial independence.

### Senior Citizen
**As a** 68-year-old senior citizen without savings,  
**I want to** find old-age pension schemes,  
**So that** I can meet my daily expenses with dignity.

### BPL Card Holder
**As a** BPL card holder,  
**I want to** understand all schemes available specifically for BPL families,  
**So that** I can maximize the benefits I'm entitled to.

### Illiterate Farmer
**As an** illiterate farmer,  
**I want to** use voice commands instead of typing,  
**So that** I can access information without needing to read or write.

### Working Mother
**As a** working mother with two children,  
**I want to** find education and nutrition schemes for my kids,  
**So that** they can have better opportunities than I had.

### Landless Laborer
**As a** landless agricultural laborer,  
**I want to** discover skill development and employment schemes,  
**So that** I can find stable work and income.

### Tribal Community Member
**As a** member of a tribal community,  
**I want to** learn about schemes specific to ST category,  
**So that** I can access reserved benefits and opportunities.

### Migrant Worker
**As a** migrant worker who moved to the city,  
**I want to** know which schemes I can still access from my home state,  
**So that** I don't lose benefits due to migration.

### Widow with Children
**As a** widow with three school-going children,  
**I want to** find schemes covering education, food security, and widow pension,  
**So that** I can support my family after losing my husband.

### First-Time User
**As a** first-time user who heard about AI Saathi from a neighbor,  
**I want to** get started with just one WhatsApp message,  
**So that** I don't need to download apps or create accounts.

---

## 3. FUNCTIONAL REQUIREMENTS

### 3.1 Voice Interface
- [x] Accept voice input in Hindi
- [x] Convert speech to text using Bhashini API
- [x] Respond with voice output (text-to-speech)
- [x] Handle poor audio quality (2G networks)
- [x] Support conversation history (context)
- [x] Handle interruptions/corrections
- [x] Recognize common Hindi dialects and accents
- [x] Allow users to switch between voice and text mid-conversation

### 3.2 Scheme Matching Engine
- [x] Ask 5-10 qualifying questions
- [x] Store user profile (age, gender, income, etc.)
- [x] Match against 500+ schemes database
- [x] Rank by eligibility score
- [x] Show top 5-10 relevant schemes
- [x] Explain why user qualifies
- [x] Filter by state/district-specific schemes
- [x] Update scheme database weekly
- [x] Handle edge cases (borderline eligibility)

### 3.3 Application Guidance
- [x] List required documents
- [x] Provide step-by-step instructions
- [x] Share application links
- [x] Track application status
- [x] Send reminders
- [x] Escalate to human if stuck
- [x] Provide nearest CSC/Jan Seva Kendra locations
- [x] Explain document requirements in simple Hindi
- [x] Offer alternative application methods (online/offline)

### 3.4 Multi-Channel Delivery
- [x] WhatsApp bot integration
- [x] Phone call (IVR) system
- [x] SMS fallback
- [x] PWA web app
- [x] Offline mode with sync
- [x] USSD support for feature phones
- [x] Missed call service for callback

### 3.5 Data & Integrations
- [x] MyScheme API integration
- [x] Bhashini ASR/TTS
- [x] Aadhaar eKYC (future)
- [x] DigiLocker (future)
- [x] State government portals
- [x] UMANG app integration
- [x] DBT portal for payment tracking

---

## 4. NON-FUNCTIONAL REQUIREMENTS

### 4.1 Performance
- Response time: < 3 seconds on 3G
- Voice clarity: 90%+ accuracy
- Uptime: 99%+ availability
- Handles 1000 concurrent users
- Audio file processing: < 2 seconds
- Database query time: < 500ms

### 4.2 Scalability
- Support 10K users in Month 1
- Scale to 100K by Month 6
- Multi-region deployment ready
- Auto-scaling based on load
- Horizontal scaling capability
- CDN for static assets

### 4.3 Security
- User data encrypted (AES-256)
- Aadhaar compliance (no storage of full Aadhaar)
- GDPR-like privacy standards
- Secure API calls (TLS 1.3)
- No data selling or third-party sharing
- Regular security audits
- OTP-based authentication
- Rate limiting to prevent abuse

### 4.4 Usability
- Works for illiterate users
- Max 3 taps to start conversation
- No app download needed (WhatsApp/web)
- Works on feature phones (IVR/SMS)
- Simple, conversational language
- No technical jargon
- Visual aids for literate users
- Audio-first design

### 4.5 Cost
- < ₹5 per user per month
- Free for end users
- Sustainable unit economics
- Optimize API calls to reduce costs
- Use caching to minimize repeated queries
- Government API usage (free where possible)

---

## 5. OUT OF SCOPE (Phase 1)

- Languages other than Hindi
- Application submission (only guidance provided)
- Payment processing
- Document upload and verification
- Video calls with support agents
- Admin dashboard for government officials
- Real-time application status tracking
- Integration with all state portals
- Aadhaar-based authentication
- Biometric verification
- Grievance redressal system
- Community forum features

---

## 6. SUCCESS METRICS

### User Metrics
- 10,000 registered users in 6 months
- 60%+ monthly active rate
- 4/5 average satisfaction score
- 40%+ repeat usage rate
- < 20% conversation abandonment rate
- Average 3+ schemes discovered per user
- 70%+ voice interaction success rate

### Impact Metrics
- 5,000 scheme applications filed (with guidance)
- ₹5 crore in benefits accessed by users
- 30% increase in scheme awareness in target areas
- < 5% wrong information rate
- 80%+ user recommendation rate (NPS)
- 50% reduction in time to discover relevant schemes

### Technical Metrics
- 99%+ uptime
- < 3 second average response time
- 90%+ voice recognition accuracy
- < 2% error rate in scheme matching
- 95%+ API success rate

---

## 7. CONSTRAINTS

### Budget
- Total: ₹15 lakhs for 6 months
- Breakdown:
  - Development: ₹6 lakhs
  - API costs: ₹3.6 lakhs (₹60K/month)
  - Infrastructure: ₹2 lakhs
  - Testing & QA: ₹1.5 lakhs
  - Contingency: ₹1.9 lakhs

### Team
- 3 people total:
  - 1 Full-stack developer
  - 1 AI/ML engineer
  - 1 Product manager + QA

### Timeline
- 2 months to MVP
- Month 1: Core voice + scheme matching
- Month 2: Multi-channel + testing
- Month 3-6: Scale + iterate

### Technology
- No proprietary models (use APIs)
- Open-source frameworks preferred
- Government APIs where available
- Cloud infrastructure (AWS/DigitalOcean)
- Must work on low-bandwidth networks

### Regulatory
- Aadhaar Act compliance
- Data Protection Bill compliance
- No storage of sensitive documents
- User consent for data collection
- Right to data deletion

---

## 8. ASSUMPTIONS

- Bhashini API will remain free for government-aligned projects
- MyScheme API provides accurate, updated data
- Target users have access to WhatsApp or phone
- 2G/3G network available in target areas
- Users willing to share basic profile information
- Government scheme eligibility criteria remain stable

---

## 9. DEPENDENCIES

- Bhashini API availability and uptime
- MyScheme API data accuracy
- Claude API rate limits and pricing
- Twilio/Exotel service reliability
- AWS/cloud infrastructure stability
- Government portal accessibility

---

## 10. ACCEPTANCE CRITERIA

### MVP Launch Criteria
- Voice input/output working in Hindi
- 100+ schemes in database
- Profile creation via conversation
- Top 5 scheme recommendations
- WhatsApp channel functional
- < 5 second response time
- 85%+ voice accuracy
- Security audit passed

### Phase 1 Completion Criteria
- All functional requirements met
- 10K users onboarded
- 60% monthly active rate achieved
- 4/5 satisfaction score
- All three channels live (WhatsApp, IVR, Web)
- 500+ schemes covered
- < 5% error rate in recommendations
