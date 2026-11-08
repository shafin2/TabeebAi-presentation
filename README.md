# Tabeeb AI (FYP) — Slide Content (English)

This README is written as **slide bullets** (copy/paste into PowerPoint).  
**Important**: This is a “system explanation”, so we avoid listing API endpoints.

Project parts:
- **Web App**: `TabeebAi-web` (patient + doctor portal)
- **Backend**: `TabeebAi-backendNode` (Node/Express + MongoDB)
- **Admin Panel**: `TabeebAi-admin`
- **AI Service**: `TabeebAi-ai-service` (FastAPI)

---

### Slide 1 — Title
- **Tabeeb AI — AI-powered Healthcare Platform**
- **From symptoms → to the right doctor → to digital care → to lifelong health history**

---

### Slide 2 — Problem
- Patients often visit **2–3 wrong doctors** before finding the right specialist
- No reliable **pre-consultation guidance** (triage) before paying
- Doctor discovery is hard: **quality + availability + trust + location**
- Medical history is scattered: **reports / prescriptions / conversation notes**

---

### Slide 3 — Our Solution (What the platform does)
- **AI-first triage** to understand symptoms and suggest the right specialty
- **Smart doctor recommendation** from a large dataset (10k+ external doctors + system doctors)
- **Booking + payment** inside one platform
- **Online consultation** with real doctors
- **Clinical notes + digital prescription** after consultation
- **Personal health record (EHR-style)**: reports + transcripts + prescriptions stored for future context

---

### Slide 4 — System Architecture (High-Level)
- **Frontend**
  - Patient experience + doctor portal (Web)
  - Admin portal (Web)
- **Backend (Azure)**
  - Auth + profiles + roles
  - Doctor discovery + booking + payments
  - Messaging + video token + clinical notes + prescriptions + reviews
  - Admin operations (sync, analytics, payouts)
- **AI Layer**
  - **V1** Node RAG (Gemini + embeddings)
  - **V2** Python FastAPI (LangChain + vector search + Redis sessions)
  - **V3** LangGraph evolution (multi-agent orchestration)

---

### Slide 5 — Tech Stack (What we used)
- **Frontend**: React + Vite + Tailwind (Web + Admin)
- **Backend**: Node.js + Express
- **Database**: MongoDB Atlas
- **AI stack**: RAG + embeddings (384-dim), **LangChain**, **LangGraph**, Redis
- **Payments**: Stripe
- **Video**: Agora RTC (real-time)
- **Chat**: Stream Chat
- **Deployment**
  - Backend: **Microsoft Azure VM**
  - Web + Admin: **Vercel**

---

## Feature 1 — Authentication & Authorization (Auth)

### Slide 6 — Auth Overview (Roles + Security)
- Multi-role system: **patient / doctor / admin**
- Role-based access control (RBAC) across the platform
- Admin is **restricted to web-only** access
- Password security: **bcrypt hashing**
- Session security: **JWT access + refresh tokens**

---

### Slide 7 — Patient Auth Flow
- Signup → email verification → login → access dashboard
- Forgot/reset password via email
- Resend verification email supported
- Tokens:
  - Access token: **15 minutes**
  - Refresh token: **7 days**

---

### Slide 8 — Doctor Auth Flow (More secure onboarding)
- Doctor signup is gated:
  - Email verification required
  - **Profile completion** (multi-step doctor profile)
  - **Admin approval** required before full access
- Doctor receives emails for approval/rejection updates

---

### Slide 9 — Google OAuth (Continue with Google)
- Continue with Google supported
- Server verifies Google identity token and links/creates accounts
- Doctor flow still respects: **profile completion + admin approval**

---

## Feature 2 — AI Chatbot (Evolution V1 → V2 → V3)

### Slide 10 — AI V1 (Node.js RAG)
- **Goal**: symptom triage + recommended specialty + top doctors
- **How it works**
  - Generate embeddings (384-dim)
  - In-memory cosine similarity search (symptoms → specialties → doctors)
  - Gemini generates final response using retrieved context
- **Limitations (what V1 taught us)**
  - Slower search at scale (10k doctors)
  - Rigid conversation flow (linear questions)
  - Weak session persistence (restart loses state)

---

### Slide 11 — AI V2 (FastAPI + LangChain + Vector Search + Redis)
- **Goal**: production-grade AI triage with fast retrieval + better UX
- **Key upgrades**
  - FastAPI microservice dedicated to AI (separate from Node backend)
  - LangChain chains for symptom analysis + doctor recommendation
  - MongoDB Atlas vector search (much faster than in-memory)
  - **Redis session management** (1-hour TTL, fallback available)
  - Smart question detection: asks only missing info (duration/severity)
  - Off-topic detection to keep chatbot medical-focused

---

### Slide 12 — AI V3 (LangGraph — Multi-Agent Workflows)
- **Why we introduced LangGraph**
  - Medical conversations are not linear (branching + loops)
  - Emergencies must route differently than normal cases
- **What V3 adds**
  - Multi-agent workflow: symptom collector + triage + specialist matcher + recommender
  - Conditional routing (emergency vs normal)
  - Better explainability (which agent decided what)
  - Parallel reasoning (speed + accuracy)

---

## Feature 3 — Doctor Discovery (Search + Recommendation)

### Slide 13 — Doctor Discovery (How patients find doctors)
- Filters: specialty, city, hospital/clinic, rating, fee, gender, availability
- Reviews & ratings visible to improve trust
- AI-recommended doctors shown directly from chatbot results
- Works with:
  - System doctors (registered on Tabeeb AI)
  - External doctors (large imported dataset)

---

## Feature 4 — Data Scraping + Sync (Admin Operations)

### Slide 14 — External Dataset Sync (Scraping pipeline)
- We maintain a large healthcare dataset (10k+ doctors) synced into MongoDB
- Sync supports:
  - Specialties
  - Hospitals (geo + city-based)
  - Symptoms + services
  - Doctors (by city + specialty)
- Reliability:
  - Rate limiting
  - Sync logs + stats
  - Cancel + retry jobs

---

### Slide 15 — Parameterized Sync (Admin controls)
- Admin can run sync jobs with parameters:
  - Cities (comma-separated)
  - Specific specialties (or “top N”)
  - Max pages (for testing / partial sync)
  - Full pipeline with skip flags for sub-steps
- This allows fast demos and controlled dataset refresh

---

### Slide 16 — Embeddings Generation (Admin-triggered)
- After new data is synced, we generate embeddings for AI search:
  - Symptoms
  - Specialties
  - System doctors
  - External doctors (primary source)
- Admin panel can trigger this as a job (same monitoring as sync jobs)

---

## Feature 5 — Appointment Scheduling + Payments

### Slide 17 — Appointment Scheduling
- Real-time slot checking + double-booking prevention
- Works for:
  - System doctors (their configured availability)
  - External doctors (default schedule for booking support)
- Both patient & doctor dashboards show appointment history and upcoming visits

---

### Slide 18 — Payments (Stripe)
- Integrated payment flow for appointment booking
- Webhook-based confirmation (reliable, server-verified)
- Transaction tracking:
  - Total amount
  - Platform commission
  - Doctor earnings
  - Payout status

---

## Feature 6 — Consultation + Notes + Prescription

### Slide 19 — Online Consultation (Video)
- Real-time video consultation (Agora RTC)
- Controls: join/leave, mute, camera toggle, call timer
- Appointment-linked access (only patient + assigned doctor)

---

### Slide 20 — Clinical Notes (Transcript → Notes)
- During/after consultation, clinical notes are stored with the appointment
- Current implementation supports:
  - Transcript capture in the doctor UI (browser speech recognition)
  - Saved transcript attached to the appointment
- This becomes input for structured clinical notes and patient record

---

### Slide 21 — Digital Prescription
- Doctor creates a prescription only after consultation is completed
- Prescription includes:
  - Diagnosis
  - Medications (dosage/frequency/duration/instructions)
  - Follow-up instructions
- Patient can view prescriptions anytime in their account

---

## Feature 7 — Reviews + Chat + Patient Records

### Slide 22 — Reviews / Feedback
- Patient can submit review after a completed visit
- Rating + optional category scoring (communication, professionalism, effectiveness)
- Builds trust and improves ranking/selection

---

### Slide 23 — Patient ↔ Doctor Messaging
- In-app chat for appointment context using Stream Chat
- Chat is created per appointment and access is restricted to:
  - the patient
  - the assigned doctor

---

### Slide 24 — Patient Health Record (EHR-style)
- Patient profile stores structured health info:
  - allergies, medications, chronic conditions, vitals, emergency contact, etc.
- Patient can upload medical documents (PDF/images/docs)
- Clinical notes + transcripts + prescriptions are linked to the patient timeline
- This record is designed to be used as context for future AI triage and diagnosis

---

## Feature 8 — Doctor Earnings + Admin Analytics

### Slide 25 — Doctor Earnings
- Doctor dashboard shows:
  - total earnings
  - pending vs paid breakdown
  - recent transactions and related appointments
- Earnings are computed from platform commission rules

---

### Slide 26 — Admin Analytics + Payouts
- Revenue analytics at platform level
- Doctor earnings leaderboard
- Pending payouts list + payout processing workflow

---

### Slide 27 — Miscellaneous (Extra Modules)
- **Health Hub**
  - Curated health articles/blogs with tag-based browsing (Health / Wellness / Medicine, etc.)
  - Integrated in frontend using a blog service
- **Health Calculators**
  - BMI calculator page
  - BMR & calorie calculator page (includes daily calories and goals)


---

## Author

Built by **[Shafin Zaman](https://www.shafinzaman.dev)** (Shafin Uz Zaman), Senior AI Engineer. Case study: [TabeebAI](https://www.shafinzaman.dev/work/tabeeb-ai). More of my work: [shafinzaman.dev/work](https://www.shafinzaman.dev/work).
