# AI Saathi - Minimum Viable Product (MVP) Specification

## 1. MVP OVERVIEW

**Project Name:** AI Saathi  
**Tagline:** "Har Gaon Mein Sarkari Yojana Ka Saathi"

**What is the MVP?**  
AI Saathi MVP is a WhatsApp text chatbot that helps rural Indians discover government welfare schemes they're eligible for. Users answer 5 simple questions about themselves (age, occupation, income, location, category), and the bot instantly recommends the top 5 relevant schemes with benefit details and application links. No app download, no registration - just send a WhatsApp message and get personalized scheme recommendations in 2 minutes.

**Core Problem It Solves:**  
70% of eligible beneficiaries never access government schemes because they don't know these schemes exist, can't navigate complex government portals, or don't understand eligibility criteria. AI Saathi makes scheme discovery as simple as chatting with a friend on WhatsApp, using conversational Hindi that anyone can understand.

### MVP Scope: What's IN vs OUT

**✅ IN SCOPE (MVP):**
- WhatsApp text chatbot (no voice yet)
- 5-question profile builder
- 20 popular central government schemes
- Simple rule-based matching (if-else logic)
- Top 5 scheme recommendations
- Hindi + English support (text only)
- Conversation history storage
- Basic error handling

**❌ OUT OF SCOPE (Future Versions):**
- Voice input/output (Phase 2)
- IVR phone calls (Phase 2)
- Regional languages beyond Hindi (Phase 3)
- Admin dashboard (Phase 2)
- Application submission (Phase 3)
- Document upload/verification (Phase 3)
- Real-time application tracking (Phase 3)
- Mobile app (Phase 3)
- Offline mode (Phase 3)
- Payment processing (Never)

### Success Criteria for MVP

**User Metrics:**
- 50-100 test users complete full conversation
- 70%+ users receive at least 3 scheme matches
- 60%+ users rate experience as "helpful" (4/5 or 5/5)
- < 30% conversation abandonment rate
- Average conversation completion time: < 3 minutes

**Technical Metrics:**
- Bot responds within 3 seconds
- 95%+ message delivery success rate
- Zero critical bugs after Week 6
- Database stores 100% of conversations correctly

**Business Validation:**
- At least 20 users share bot with friends/family
- 10+ users ask follow-up questions about schemes
- 5+ users report attempting to apply for schemes

**Timeline:** 6-8 weeks to build

**Week 1-2:** Setup + Basic WhatsApp bot (send/receive messages)  
**Week 3-4:** Conversation flow + Profile builder  
**Week 5:** Scheme database + Matching logic  
**Week 6:** Testing + Bug fixes  
**Week 7-8:** User testing + Iteration

---

## 2. MVP FEATURES (MUST HAVE)

### Core Features

#### ✅ Feature 1: WhatsApp Text Chatbot
**What:** Bot receives and sends text messages via WhatsApp  
**Why:** WhatsApp has 500M+ users in India, no app download needed  
**How:** Twilio WhatsApp API + Webhook to Node.js server  
**Acceptance Criteria:**
- User sends "Hi" → Bot responds within 3 seconds
- Bot handles 10 concurrent conversations
- Messages delivered 95%+ of the time

#### ✅ Feature 2: Conversational Profile Builder
**What:** Bot asks 5 questions to understand user needs  
**Why:** Need basic info to match schemes accurately  
**How:** State machine with 5 steps, stores answers in database  
**Questions:**
1. Name (for personalization)
2. Age (many schemes have age limits)
3. Occupation (farmer, laborer, student, widow, unemployed, other)
4. Annual income (BPL, < 1L, 1-3L, 3-5L, > 5L)
5. State (for state-specific schemes)

**Acceptance Criteria:**
- All 5 questions asked in sequence
- Bot handles invalid inputs (e.g., age = "abc")
- User can restart conversation anytime
- Profile saved to database after completion

#### ✅ Feature 3: User Profile Storage
**What:** Store user data in database for future conversations  
**Why:** Avoid asking same questions repeatedly  
**How:** PostgreSQL table with phone number as unique key  
**Data Stored:**
- Phone number (unique ID)
- Name
- Age
- Occupation
- Income bracket
- State
- Category (SC/ST/OBC/General) - optional
- Created timestamp
- Last active timestamp

**Acceptance Criteria:**
- Profile created after first conversation
- Returning users recognized by phone number
- Users can update profile by typing "update profile"

#### ✅ Feature 4: Scheme Database (20 Schemes)
**What:** Curated list of 20 popular central government schemes  
**Why:** Cover 80% of common use cases with 20% of schemes  
**How:** JSON file or database table with scheme details  
**Schemes to Include:**
1. PM-KISAN (farmers)
2. PM Fasal Bima Yojana (crop insurance)
3. Ayushman Bharat (health insurance)
4. PM Awas Yojana (housing)
5. Sukanya Samriddhi Yojana (girl child)
6. Atal Pension Yojana (pension)
7. PM Mudra Yojana (business loans)
8. National Social Assistance Programme (old age pension)
9. Widow Pension Scheme
10. Disability Pension
11. PM Scholarship Scheme (students)
12. Mid-Day Meal Scheme
13. Pradhan Mantri Matru Vandana Yojana (pregnant women)
14. Kisan Credit Card
15. PM Svanidhi (street vendors)
16. Skill India (training)
17. Stand Up India (SC/ST entrepreneurs)
18. National Rural Livelihood Mission
19. PM Ujjwala Yojana (LPG)
20. Jal Jeevan Mission (water)

**Data per Scheme:**
- Scheme ID
- Name (Hindi + English)
- Department
- Benefit amount/type
- Eligibility criteria (age, occupation, income, state)
- Required documents
- Application URL
- Helpline number
- Short description (2-3 sentences in Hindi)

**Acceptance Criteria:**
- All 20 schemes in database
- Each scheme has complete information
- Data validated for accuracy

#### ✅ Feature 5: Simple Matching Algorithm
**What:** Rule-based logic to match user profile with schemes  
**Why:** No need for complex ML in MVP, rules work well  
**How:** If-else conditions based on eligibility criteria  
**Matching Logic:**
```
For each scheme:
  score = 0
  
  // Age match
  if user.age >= scheme.age_min AND user.age <= scheme.age_max:
    score += 20
  
  // Occupation match
  if user.occupation in scheme.target_occupations:
    score += 30
  
  // Income match
  if user.income <= scheme.income_max:
    score += 25
  
  // State match
  if user.state in scheme.applicable_states OR scheme.is_central:
    score += 15
  
  // Category match (bonus)
  if user.category in scheme.priority_categories:
    score += 10
  
  // Add to results if score > 40
  if score >= 40:
    matched_schemes.push({scheme, score})

// Sort by score descending
// Return top 5
```

**Acceptance Criteria:**
- Returns 3-10 schemes for typical user
- Most relevant schemes ranked higher
- Zero schemes only if truly ineligible
- Matching completes in < 500ms

#### ✅ Feature 6: Scheme Recommendations Display
**What:** Show top 5 matched schemes in readable format  
**Why:** Users need clear, actionable information  
**How:** Formatted WhatsApp message with emojis  
**Format:**
```
🎯 Aapke liye 5 yojana mili hain:

1️⃣ PM-KISAN
💰 Benefit: ₹6,000/saal (3 installments)
📋 Eligibility: Kisan with < 2 hectare land
🔗 Apply: pmkisan.gov.in
📞 Helpline: 155261

2️⃣ Ayushman Bharat
💰 Benefit: ₹5 lakh health insurance
📋 Eligibility: BPL families
🔗 Apply: pmjay.gov.in
📞 Helpline: 14555

[... 3 more schemes ...]

Kya aapko kisi scheme ke bare mein aur jaankari chahiye?
```

**Acceptance Criteria:**
- All 5 schemes displayed clearly
- Emojis make it visually appealing
- Links are clickable
- Message fits in WhatsApp (< 4096 chars)

#### ✅ Feature 7: Conversation History
**What:** Save all messages exchanged with user  
**Why:** Debug issues, improve bot, analyze usage  
**How:** Database table with conversation logs  
**Data Stored:**
- Conversation ID
- User phone number
- Message timestamp
- Message sender (user/bot)
- Message content
- Current conversation state

**Acceptance Criteria:**
- 100% of messages logged
- Logs queryable by phone number
- Logs retained for 90 days

#### ✅ Feature 8: Basic Error Handling
**What:** Handle common errors gracefully  
**Why:** Users shouldn't see technical errors  
**How:** Try-catch blocks + user-friendly messages  
**Error Scenarios:**
1. Invalid age input → "Kripya sahi umar dalein (18-100)"
2. Invalid income → "Kripya income number mein dalein"
3. No schemes found → "Maaf kijiye, abhi aapke liye koi yojana nahi mili. Kya aap apni details update karna chahenge?"
4. API failure → "Kuch technical problem hai. Kripya 5 minute baad try karein"
5. Unclear input → "Main samjha nahi. Kripya fir se batayein"

**Acceptance Criteria:**
- No error crashes the bot
- All errors show helpful messages
- Errors logged for debugging

---

## 3. USER FLOW (DETAILED)

### Flow 1: First-Time User (Happy Path)

```
USER: Hi
BOT: Namaste! 🙏 Main AI Saathi hoon. Main aapko sarkari yojana 
     ke bare mein bataunga.
     
     Pehle aapke bare mein kuch jaanna chahta hoon. Sirf 5 sawaal 
     hain, 2 minute lagenge.
     
     Aapka naam kya hai?

USER: Ramesh Kumar
BOT: Dhanyavaad Ramesh ji! 😊
     
     Aapki umar kya hai?

USER: 45
BOT: Theek hai.
     
     Aap kya kaam karte hain? Number select karein:
     1️⃣ Kisan (Farmer)
     2️⃣ Mazdoor (Laborer)
     3️⃣ Student
     4️⃣ Vidhwa (Widow)
     5️⃣ Vendor/Dukandaar
     6️⃣ Unemployed
     7️⃣ Other

USER: 1
BOT: Aapki saalana income kitni hai? Number select karein:
     1️⃣ BPL Card holder (< ₹50,000)
     2️⃣ ₹50,000 - ₹1,00,000
     3️⃣ ₹1,00,000 - ₹3,00,000
     4️⃣ ₹3,00,000 - ₹5,00,000
     5️⃣ > ₹5,00,000

USER: 3
BOT: Aap kahan rehte hain? State ka naam batayein:
     (Jaise: Bihar, UP, MP, etc.)

USER: Bihar
BOT: Perfect! ✅ Ab main aapke liye sahi yojana dhundh raha hoon...
     
     ⏳ Kripya 2 second rukein...

[2 seconds later]

BOT: 🎯 Ramesh ji, aapke liye 5 yojana mili hain:

     1️⃣ PM-KISAN
     💰 Benefit: ₹6,000 har saal (3 baar mein)
     📋 Eligibility: Kisan with agricultural land
     🔗 Apply: https://pmkisan.gov.in
     📞 Helpline: 155261
     
     2️⃣ Pradhan Mantri Fasal Bima Yojana
     💰 Benefit: 90% premium subsidy on crop insurance
     📋 Eligibility: All farmers
     🔗 Apply: https://pmfby.gov.in
     📞 Helpline: 18001801551
     
     3️⃣ Kisan Credit Card (KCC)
     💰 Benefit: Loan at 4% interest
     📋 Eligibility: Farmers with land records
     🔗 Apply: Nearest bank branch
     📞 Helpline: 1800-180-1111
     
     4️⃣ Ayushman Bharat (PM-JAY)
     💰 Benefit: ₹5 lakh health insurance
     📋 Eligibility: Income < ₹3 lakh/year
     🔗 Apply: https://pmjay.gov.in
     📞 Helpline: 14555
     
     5️⃣ PM Awas Yojana - Gramin
     💰 Benefit: ₹1.2 lakh for house construction
     📋 Eligibility: Rural families without pucca house
     🔗 Apply: https://pmayg.nic.in
     📞 Helpline: 1800-11-6446
     
     ━━━━━━━━━━━━━━━━━━━━━━
     
     Kya aapko kisi yojana ke bare mein aur jaankari chahiye?
     Ya koi sawaal hai?

USER: PM-KISAN ke liye kya documents chahiye?
BOT: PM-KISAN ke liye yeh documents chahiye:
     
     ✅ Aadhaar Card
     ✅ Bank Passbook (Aadhaar se linked)
     ✅ Zameen ke kagaz (Land records)
     ✅ Passport size photo
     
     Application kaise karein:
     1. pmkisan.gov.in par jaayein
     2. "Farmer Corner" par click karein
     3. "New Farmer Registration" select karein
     4. Aadhaar number dalein
     5. Details bharein aur submit karein
     
     Ya phir aap apne paas ke CSC center ya Gram Panchayat 
     office mein jaake bhi apply kar sakte hain.
     
     Kya aur kuch help chahiye?

USER: Nahi, thank you
BOT: Aapka swagat hai Ramesh ji! 😊
     
     Agar kabhi aur yojana ke bare mein jaanna ho, toh mujhe 
     message kar sakte hain.
     
     Aapka din shubh ho! 🙏
```

### Flow 2: Returning User

```
USER: Hi
BOT: Namaste Ramesh ji! 🙏 Aapko fir se dekh kar khushi hui.
     
     Kya aap:
     1️⃣ Naye yojana dekhna chahte hain
     2️⃣ Apni profile update karna chahte hain
     3️⃣ Kisi yojana ke bare mein puchna chahte hain

USER: 1
BOT: Theek hai! Main aapke liye latest yojana dhundh raha hoon...
     
     [Shows updated scheme list based on existing profile]
```

### Flow 3: Error Handling - Invalid Age

```
USER: Hi
BOT: Namaste! Aapka naam kya hai?
USER: Sunita
BOT: Dhanyavaad Sunita ji! Aapki umar kya hai?
USER: twenty five
BOT: ❌ Maaf kijiye, main samjha nahi.
     
     Kripya umar number mein dalein (jaise: 25)
     
     Aapki umar kya hai?
USER: 25
BOT: Theek hai. ✅
     [Continues to next question]
```

### Flow 4: Error Handling - No Schemes Found

```
[After profile completion]

BOT: 😔 Maaf kijiye, abhi aapke profile ke liye koi yojana 
     nahi mili.
     
     Yeh ho sakta hai kyunki:
     - Aapki income limit se zyada hai
     - Aapki umar eligibility se bahar hai
     - Aapke state mein yeh yojana nahi hai
     
     Kya aap apni details update karna chahenge?
     1️⃣ Haan, update karo
     2️⃣ Nahi, baad mein

USER: 1
BOT: Theek hai! Kaunsi detail update karni hai?
     1️⃣ Occupation
     2️⃣ Income
     3️⃣ State
     4️⃣ Sab kuch fir se bharein
```

### Flow 5: User Asks Question Mid-Conversation

```
BOT: Aapki umar kya hai?
USER: Yeh bot kya hai?
BOT: Main AI Saathi hoon - ek chatbot jo aapko sarkari yojana 
     ke bare mein batata hai. 😊
     
     Main aapse kuch sawaal puchunga, aur phir aapke liye best 
     yojana suggest karunga.
     
     Chalo shuru karte hain!
     Aapki umar kya hai?
```

### Flow 6: User Wants to Restart

```
[Mid-conversation]
USER: restart
BOT: Koi baat nahi! Main fir se shuru karta hoon. 🔄
     
     Aapka naam kya hai?
```

### Flow 7: User Asks About Specific Scheme

```
USER: PM Kisan ke bare mein batao
BOT: 🌾 PM-KISAN (Pradhan Mantri Kisan Samman Nidhi)
     
     💰 Benefit: ₹6,000 har saal
     - ₹2,000 har 4 mahine mein (3 installments)
     - Seedha bank account mein
     
     📋 Eligibility:
     - Aap kisan hone chahiye
     - Aapke paas kheti ki zameen honi chahiye
     - Zameen 2 hectare se kam honi chahiye
     
     📄 Documents:
     - Aadhaar Card
     - Bank Passbook (Aadhaar linked)
     - Land records
     
     🔗 Apply: https://pmkisan.gov.in
     📞 Helpline: 155261
     
     Kya aapko aur kuch jaanna hai?
```

---

## 4. TECHNICAL ARCHITECTURE

### System Architecture (Simplified)

```
┌─────────────────────────────────────────┐
│         USER (WhatsApp)                 │
│   Sends text message from phone         │
└─────────────┬───────────────────────────┘
              │
              ↓
┌─────────────────────────────────────────┐
│    TWILIO WHATSAPP API                  │
│    • Receives user messages             │
│    • Sends bot responses                │
│    • Webhook to your server             │
└─────────────┬───────────────────────────┘
              │
              ↓ HTTP POST
┌─────────────────────────────────────────┐
│    YOUR SERVER (Node.js + Express)      │
│                                         │
│  ┌─────────────────────────────────┐   │
│  │  Webhook Handler                │   │
│  │  • Receives message             │   │
│  │  • Extracts phone + text        │   │
│  └──────────┬──────────────────────┘   │
│             ↓                           │
│  ┌─────────────────────────────────┐   │
│  │  Conversation Manager           │   │
│  │  • State machine (5 states)     │   │
│  │  • Validates input              │   │
│  │  • Builds user profile          │   │
│  └──────────┬──────────────────────┘   │
│             ↓                           │
│  ┌─────────────────────────────────┐   │
│  │  Scheme Matching Engine         │   │
│  │  • Loads schemes from DB        │   │
│  │  • Runs matching algorithm      │   │
│  │  • Ranks by score               │   │
│  └──────────┬──────────────────────┘   │
│             ↓                           │
│  ┌─────────────────────────────────┐   │
│  │  Response Formatter             │   │
│  │  • Formats scheme list          │   │
│  │  • Adds emojis                  │   │
│  │  • Generates reply text         │   │
│  └──────────┬──────────────────────┘   │
│             ↓                           │
│  ┌─────────────────────────────────┐   │
│  │  Database Layer                 │   │
│  │  • Save user profile            │   │
│  │  • Log conversation             │   │
│  │  • Query schemes                │   │
│  └─────────────────────────────────┘   │
└─────────────┬───────────────────────────┘
              │
              ↓
┌─────────────────────────────────────────┐
│    DATABASE (PostgreSQL)                │
│                                         │
│  Tables:                                │
│  • users (phone, name, age, etc.)      │
│  • schemes (20 schemes data)           │
│  • conversations (message logs)        │
└─────────────────────────────────────────┘
```

### Tech Stack

**Backend:**
- Runtime: Node.js 18+
- Framework: Express.js 4.x
- Language: JavaScript (or TypeScript)

**Database:**
- PostgreSQL 15 (or MongoDB if preferred)
- ORM: Prisma (or Sequelize)

**WhatsApp Integration:**
- Twilio WhatsApp API (Sandbox for testing, Business API for production)

**Hosting:**
- Free tier options: Render.com, Railway.app, Fly.io
- Or: AWS EC2 t2.micro (free tier)

**Development Tools:**
- Git + GitHub
- Postman (API testing)
- ngrok (local webhook testing)
- VS Code

### Database Schema

#### Table: users
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  phone VARCHAR(15) UNIQUE NOT NULL,
  name VARCHAR(100),
  age INTEGER,
  occupation VARCHAR(50),
  income_bracket VARCHAR(20),
  state VARCHAR(50),
  category VARCHAR(10),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

#### Table: schemes
```sql
CREATE TABLE schemes (
  id SERIAL PRIMARY KEY,
  scheme_id VARCHAR(50) UNIQUE NOT NULL,
  name_hindi VARCHAR(200) NOT NULL,
  name_english VARCHAR(200) NOT NULL,
  department VARCHAR(200),
  benefit_amount VARCHAR(100),
  benefit_type VARCHAR(50),
  description_hindi TEXT,
  description_english TEXT,
  age_min INTEGER,
  age_max INTEGER,
  target_occupations TEXT[], -- Array of occupations
  income_max INTEGER,
  applicable_states TEXT[], -- Array of states
  is_central BOOLEAN DEFAULT true,
  required_documents TEXT[],
  application_url VARCHAR(500),
  helpline VARCHAR(20),
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### Table: conversations
```sql
CREATE TABLE conversations (
  id SERIAL PRIMARY KEY,
  conversation_id UUID DEFAULT gen_random_uuid(),
  user_phone VARCHAR(15) NOT NULL,
  message_from VARCHAR(10) NOT NULL, -- 'user' or 'bot'
  message_text TEXT NOT NULL,
  conversation_state VARCHAR(50),
  created_at TIMESTAMP DEFAULT NOW()
);
```

### API Endpoints

```
POST /webhook/whatsapp
- Receives messages from Twilio
- Body: { From, Body, MessageSid }
- Returns: 200 OK

GET /health
- Health check endpoint
- Returns: { status: "ok", timestamp }

GET /api/users/:phone
- Get user profile
- Returns: User object

POST /api/users
- Create/update user profile
- Body: User data
- Returns: User object

GET /api/schemes
- Get all schemes
- Query params: occupation, income, state
- Returns: Array of schemes

GET /api/schemes/:id
- Get specific scheme details
- Returns: Scheme object

POST /api/match
- Match schemes for user
- Body: { phone }
- Returns: { schemes: [], scores: [] }
```

---

## 5. CONVERSATION STATE MACHINE

The bot uses a state machine to track conversation progress:

```
States:
1. INITIAL → Waiting for name
2. NAME_RECEIVED → Waiting for age
3. AGE_RECEIVED → Waiting for occupation
4. OCCUPATION_RECEIVED → Waiting for income
5. INCOME_RECEIVED → Waiting for state
6. PROFILE_COMPLETE → Show schemes
7. IDLE → Waiting for follow-up questions

State Transitions:
INITIAL --[name]--> NAME_RECEIVED
NAME_RECEIVED --[age]--> AGE_RECEIVED
AGE_RECEIVED --[occupation]--> OCCUPATION_RECEIVED
OCCUPATION_RECEIVED --[income]--> INCOME_RECEIVED
INCOME_RECEIVED --[state]--> PROFILE_COMPLETE
PROFILE_COMPLETE --[show schemes]--> IDLE
IDLE --[question]--> IDLE
IDLE --[restart]--> INITIAL
```

**Implementation:**
```javascript
const states = {
  INITIAL: 'initial',
  NAME_RECEIVED: 'name_received',
  AGE_RECEIVED: 'age_received',
  OCCUPATION_RECEIVED: 'occupation_received',
  INCOME_RECEIVED: 'income_received',
  PROFILE_COMPLETE: 'profile_complete',
  IDLE: 'idle'
};

function handleMessage(phone, message, currentState) {
  switch(currentState) {
    case states.INITIAL:
      // Save name, ask for age
      return { nextState: states.NAME_RECEIVED, response: "Aapki umar kya hai?" };
    
    case states.NAME_RECEIVED:
      // Validate age, ask for occupation
      if (!isValidAge(message)) {
        return { nextState: states.NAME_RECEIVED, response: "Invalid age" };
      }
      return { nextState: states.AGE_RECEIVED, response: "Occupation?" };
    
    // ... other states
  }
}
```

---

## 6. SCHEME MATCHING ALGORITHM (DETAILED)

### Matching Logic Pseudocode

```javascript
function matchSchemes(userProfile) {
  const allSchemes = loadSchemesFromDatabase();
  const matchedSchemes = [];
  
  for (const scheme of allSchemes) {
    let score = 0;
    let reasons = [];
    
    // 1. Age Check (20 points)
    if (userProfile.age >= scheme.age_min && 
        userProfile.age <= scheme.age_max) {
      score += 20;
      reasons.push("Age eligible");
    } else {
      continue; // Skip if age not eligible
    }
    
    // 2. Occupation Match (30 points)
    if (scheme.target_occupations.includes(userProfile.occupation) ||
        scheme.target_occupations.includes('all')) {
      score += 30;
      reasons.push("Occupation match");
    }
    
    // 3. Income Check (25 points)
    const userIncome = getIncomeValue(userProfile.income_bracket);
    if (userIncome <= scheme.income_max || scheme.income_max === null) {
      score += 25;
      reasons.push("Income eligible");
    }
    
    // 4. State/Geography (15 points)
    if (scheme.is_central || 
        scheme.applicable_states.includes(userProfile.state)) {
      score += 15;
      reasons.push("Available in your state");
    }
    
    // 5. Category Bonus (10 points)
    if (userProfile.category && 
        scheme.priority_categories.includes(userProfile.category)) {
      score += 10;
      reasons.push("Priority category");
    }
    
    // Only include if minimum score met
    if (score >= 40) {
      matchedSchemes.push({
        scheme: scheme,
        score: score,
        reasons: reasons
      });
    }
  }
  
  // Sort by score (highest first)
  matchedSchemes.sort((a, b) => b.score - a.score);
  
  // Return top 5
  return matchedSchemes.slice(0, 5);
}

function getIncomeValue(bracket) {
  const incomeMap = {
    'bpl': 50000,
    '50k-1l': 100000,
    '1l-3l': 300000,
    '3l-5l': 500000,
    '5l+': 1000000
  };
  return incomeMap[bracket] || 0;
}
```

### Example Matching Scenario

**User Profile:**
- Name: Ramesh Kumar
- Age: 45
- Occupation: Farmer
- Income: ₹1-3 lakhs
- State: Bihar

**Scheme: PM-KISAN**
- Age: 18-100 ✅ (20 points)
- Occupation: Farmer ✅ (30 points)
- Income: No limit ✅ (25 points)
- State: All India ✅ (15 points)
- Total: 90 points → Rank #1

**Scheme: Sukanya Samriddhi**
- Age: Must have girl child < 10 ❌ (0 points)
- Skip this scheme

**Scheme: Ayushman Bharat**
- Age: 18-100 ✅ (20 points)
- Occupation: All ✅ (30 points)
- Income: < ₹5 lakhs ✅ (25 points)
- State: All India ✅ (15 points)
- Total: 90 points → Rank #2

---

## 7. DATA REQUIREMENTS

### 20 Schemes to Include in MVP

| # | Scheme Name | Target Users | Benefit |
|---|-------------|--------------|---------|
| 1 | PM-KISAN | Farmers | ₹6,000/year |
| 2 | PM Fasal Bima | Farmers | Crop insurance |
| 3 | Ayushman Bharat | Low income | ₹5L health insurance |
| 4 | PM Awas Yojana | Homeless | ₹1.2L for house |
| 5 | Sukanya Samriddhi | Girl child parents | High interest savings |
| 6 | Atal Pension | Workers | Pension after 60 |
| 7 | PM Mudra Yojana | Entrepreneurs | Business loan |
| 8 | Old Age Pension | Senior citizens | ₹200-500/month |
| 9 | Widow Pension | Widows | ₹300-500/month |
| 10 | Disability Pension | Disabled persons | ₹300-500/month |
| 11 | PM Scholarship | Students | ₹10,000-20,000/year |
| 12 | Mid-Day Meal | School children | Free meals |
| 13 | Matru Vandana | Pregnant women | ₹5,000 |
| 14 | Kisan Credit Card | Farmers | Low interest loan |
| 15 | PM Svanidhi | Street vendors | ₹10,000 loan |
| 16 | Skill India | Youth | Free training |
| 17 | Stand Up India | SC/ST/Women | ₹10L-1Cr loan |
| 18 | NRLM | Rural poor | Livelihood support |
| 19 | PM Ujjwala | BPL women | Free LPG connection |
| 20 | Jal Jeevan Mission | Rural households | Tap water connection |

### Data Collection Sources
- Official government websites
- MyScheme.gov.in portal
- Ministry websites
- State government portals
- Manual verification by team

---

## 8. TESTING PLAN

### Week 6: Internal Testing

**Unit Tests:**
- Test matching algorithm with 10 sample profiles
- Test state machine transitions
- Test input validation functions
- Test database CRUD operations

**Integration Tests:**
- Test Twilio webhook end-to-end
- Test database connections
- Test API endpoints with Postman

**Manual Testing:**
- 10 team members test full conversation flow
- Test all error scenarios
- Test with different user profiles
- Test edge cases (very young, very old, very high income)

### Week 7-8: User Testing

**Beta Testers:**
- 50-100 real users (friends, family, village contacts)
- Mix of demographics (age, occupation, location)
- Both literate and semi-literate users

**Testing Checklist:**
- [ ] Can complete conversation without help
- [ ] Receives relevant scheme recommendations
- [ ] Understands bot responses (language clarity)
- [ ] Finds information useful
- [ ] Would recommend to others

**Feedback Collection:**
- Post-conversation survey (5 questions)
- WhatsApp message asking for rating (1-5 stars)
- Optional: Phone call with 10 users for detailed feedback

**Metrics to Track:**
- Conversation completion rate
- Average time to complete
- Number of errors encountered
- Satisfaction rating
- Schemes discovered per user
- Follow-up questions asked

---

## 9. DEPLOYMENT PLAN

### Development Environment
- Local machine with ngrok for webhook testing
- PostgreSQL running locally or Docker
- Git for version control

### Staging Environment
- Deploy to Render.com or Railway.app (free tier)
- Use Twilio WhatsApp Sandbox
- Test database (separate from production)
- URL: https://ai-saathi-staging.onrender.com

### Production Environment (Week 8)
- Deploy to Render.com (paid tier) or AWS EC2
- Twilio WhatsApp Business API (approved number)
- Production database with backups
- URL: https://ai-saathi.onrender.com
- Domain: aisaathi.in (optional)

### Deployment Checklist
- [ ] Environment variables configured
- [ ] Database migrations run
- [ ] Scheme data seeded
- [ ] Twilio webhook URL updated
- [ ] Health check endpoint working
- [ ] Monitoring/logging enabled
- [ ] Backup strategy in place

---

## 10. COST BREAKDOWN (MVP)

### Development Phase (6-8 weeks)

**Free Tier Services:**
- Twilio WhatsApp Sandbox: ₹0 (1000 free messages)
- Render.com hosting: ₹0 (free tier, 750 hours/month)
- PostgreSQL: ₹0 (Render free tier, 1GB)
- GitHub: ₹0 (free for public repos)
- ngrok: ₹0 (free tier for testing)

**Paid Services (Optional):**
- Domain name: ₹500-1000/year
- Twilio WhatsApp Business API: ₹1.5/message (after free tier)
- Render.com paid tier: ₹500/month (if free tier insufficient)

**Total MVP Cost: ₹0-2,000**

### Post-MVP (100 users/month)

**Estimated Costs:**
- Twilio: 100 users × 20 messages × ₹1.5 = ₹3,000
- Hosting: ₹500/month (Render paid tier)
- Database: ₹0 (included in hosting)
- Total: ₹3,500/month = ₹35/user/month

**Cost Optimization:**
- Use free tier as long as possible
- Optimize message count (combine multiple responses)
- Cache common queries
- Use cheaper alternatives (Gupshup for WhatsApp)

---

## 11. RISKS & MITIGATION

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Twilio sandbox limitations | High | High | Apply for Business API early |
| Users don't understand bot | High | Medium | Test with real users, simplify language |
| Scheme data becomes outdated | Medium | Medium | Monthly manual review |
| Low user adoption | High | Medium | Start with known contacts, get referrals |
| Technical bugs in production | Medium | Medium | Thorough testing, error logging |
| Database crashes | High | Low | Use managed database, daily backups |
| Exceeded free tier limits | Low | Low | Monitor usage, upgrade if needed |

---

## 12. SUCCESS CRITERIA (RECAP)

### Must Achieve (MVP Success)
✅ 50+ users complete full conversation  
✅ 70%+ users get 3+ scheme matches  
✅ 60%+ satisfaction rating (4/5 or 5/5)  
✅ < 30% conversation abandonment  
✅ < 3 second response time  
✅ Zero critical bugs in Week 8  

### Nice to Have (Bonus)
🎯 100+ users  
🎯 10+ users share with friends  
🎯 5+ users report applying for schemes  
🎯 Media coverage or social media mentions  

---

## 13. NEXT STEPS (WEEK 1)

### Day 1-2: Setup
- [ ] Create GitHub repository
- [ ] Set up Node.js project (npm init)
- [ ] Install dependencies (express, twilio, pg, dotenv)
- [ ] Create Twilio account
- [ ] Join WhatsApp Sandbox
- [ ] Set up ngrok for local testing

### Day 3-4: Basic Bot
- [ ] Create Express server
- [ ] Implement /webhook/whatsapp endpoint
- [ ] Test receiving messages from WhatsApp
- [ ] Test sending messages back
- [ ] Log all messages to console

### Day 5-7: Database
- [ ] Set up PostgreSQL locally
- [ ] Create database schema (3 tables)
- [ ] Implement database connection
- [ ] Test CRUD operations
- [ ] Seed 5 sample schemes

**Week 1 Goal:** Bot that echoes messages back + database working

---

## 14. RESOURCES & REFERENCES

### Documentation
- Twilio WhatsApp API: https://www.twilio.com/docs/whatsapp
- Express.js: https://expressjs.com/
- PostgreSQL: https://www.postgresql.org/docs/
- MyScheme Portal: https://www.myscheme.gov.in/

### Tutorials
- Building WhatsApp Bot: https://www.twilio.com/blog/build-whatsapp-chatbot-node-js
- State Machines in Node.js: https://github.com/jakesgordon/javascript-state-machine

### Scheme Information
- PM-KISAN: https://pmkisan.gov.in/
- Ayushman Bharat: https://pmjay.gov.in/
- MyScheme: https://www.myscheme.gov.in/

---

**Ready to build? Let's start with Week 1! 🚀**
