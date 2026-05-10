# 🎯 AI-Powered Job Application Tracker — Complete Interview Guide

---

## 🗣️ 0. HOW TO EXPLAIN THIS PROJECT (Read This First!)

---

### ⚡ One-Liner (for resume / intro)

> *"A 3-tier full-stack application — React + Spring Boot + Python Flask — that tracks job applications and uses NLP-based AI (TF-IDF + spaCy) to analyze and score resumes against job descriptions, with role-based access control via JWT authentication."*

---

### 🎤 Short Explanation — 30 Seconds
*(Say this when the interviewer asks: "Tell me about your project")*

> "I built an **AI-Powered Job Application Tracker** — a full-stack web app where students can track all their job applications in one place, upload their resume, and get **AI feedback** on how well their resume matches a job description.
>
> It also has an **Admin panel** where admins can post placement drives, manage users, and view platform analytics.
>
> I used **React** for the frontend, **Spring Boot** for the backend REST API, **MySQL** for the database, and a **Python Flask microservice** for the AI resume analysis."

---

### 📋 Brief Explanation — 2 Minutes
*(Say this when they ask: "Can you explain it in more detail?" or "Walk me through the architecture")*

> "The project has **three separate services** running together:
>
> **1. React Frontend** — Users can register and login, then add job applications and track their status — Applied, Interview, Offer, or Rejected. They can upload their resume as a PDF or DOCX file and use the AI Analyzer to get a match score against any job description. Admins get a completely separate dashboard to post job drives, manage users, view all resumes, and see analytics charts.
>
> **2. Spring Boot Backend** — This is the main REST API. It handles all business logic — authentication using **JWT tokens**, **role-based access control** for USER and ADMIN roles, file upload handling, and it acts as a **proxy** that forwards the resume file from disk to the Python AI service. Spring Security protects all routes, and Spring Data JPA handles all database operations with MySQL.
>
> **3. Python AI Microservice** — A Flask service that receives the resume file, extracts text from PDF or DOCX, detects technical skills using a keyword list and **spaCy NLP**, and calculates a **match score** between the resume and a job description using **TF-IDF and cosine similarity**. It also gives improvement suggestions — like missing resume sections, weak action verbs, no quantified achievements, and missing contact info.
>
> The **key challenge** I faced was the AI service receiving the resume file — the Spring Boot backend reads the file from disk and forwards it as multipart form data to Python. I also handled CORS between three different ports, JWT authentication with role-based routing on both frontend and backend, and used lazy loading in JPA to avoid N+1 query performance issues."

---


## 📌 1. Project Overview (30-Second Elevator Pitch)

> "I built a **full-stack, three-tier web application** called the **AI-Powered Job Application Tracker**. It helps students and job seekers manage their job applications, view company placement drives, upload and analyze resumes using AI, and get matched against job descriptions — all in one platform. It has separate dashboards for **Users** and **Admins**, secured with **JWT authentication**, and uses a **Python AI microservice** for NLP-based resume analysis."

---

## 🏗️ 2. System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT BROWSER                        │
│              React.js SPA (Port 3000)                        │
└──────────────────────┬──────────────────────────────────────┘
                       │ HTTP REST (Axios + JWT)
                       ▼
┌─────────────────────────────────────────────────────────────┐
│              Spring Boot Backend (Port 8080)                  │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────┐   │
│  │   Auth   │ │   Jobs   │ │  Resume  │ │    Admin     │   │
│  │Controller│ │Controller│ │Controller│ │  Controller  │   │
│  └──────────┘ └──────────┘ └──────────┘ └──────────────┘   │
│              Spring Security (JWT Filter)                     │
│              Spring Data JPA (Hibernate ORM)                 │
└──────────┬────────────────────────────┬────────────────────-┘
           │ SQL Queries                │ HTTP (resume file)
           ▼                            ▼
┌─────────────────┐          ┌───────────────────────────────┐
│  MySQL Database │          │  Python AI Service (Port 5000) │
│  (5 Tables)     │          │  Flask + spaCy + scikit-learn  │
└─────────────────┘          └───────────────────────────────┘
```

---

## 🗂️ 3. Complete Project Structure

```
Job simulator/
│
├── backend/                          # Spring Boot (Java 17)
│   ├── Dockerfile
│   ├── pom.xml                       # Maven dependencies
│   └── src/main/java/com/jobtracker/
│       ├── JobTrackerApplication.java    # Main entry point
│       ├── config/
│       │   ├── SecurityConfig.java       # Spring Security + CORS
│       │   ├── DataLoader.java           # Seed admin user on startup
│       │   ├── OpenApiConfig.java        # Swagger/OpenAPI setup
│       │   ├── PasswordEncoderConfig.java
│       │   └── SimpleCorsFilter.java
│       ├── controller/
│       │   ├── AuthController.java       # POST /api/auth/register, /login
│       │   ├── JobController.java        # CRUD /api/jobs/**
│       │   ├── JobPostingController.java # Admin posts jobs, users view
│       │   ├── ResumeController.java     # POST /api/resume/upload
│       │   ├── AIController.java         # Proxy to Python AI service
│       │   ├── AdminController.java      # /api/admin/** (ADMIN role only)
│       │   └── NotificationController.java
│       ├── model/                        # JPA Entities
│       │   ├── User.java
│       │   ├── JobApplication.java
│       │   ├── JobPosting.java
│       │   ├── Resume.java
│       │   └── Notification.java
│       ├── dto/                          # Request/Response objects
│       ├── repository/                   # Spring Data JPA Repositories
│       ├── security/
│       │   ├── JwtUtil.java              # Token generation & validation
│       │   └── JwtFilter.java            # Intercepts every request
│       └── service/                      # Business logic
│
├── frontend/                         # React.js (CRA)
│   ├── Dockerfile
│   ├── nginx.conf
│   ├── package.json
│   └── src/
│       ├── App.js                        # Router + Auth state
│       ├── api/api.js                    # Axios instance + interceptors
│       ├── components/
│       │   ├── Layout.js                 # User sidebar + header
│       │   └── AdminLayout.js            # Admin sidebar + header
│       └── pages/
│           ├── Login.js / Register.js
│           ├── Dashboard.js              # User stats overview
│           ├── Jobs.js                   # Manage job applications
│           ├── Drives.js                 # View placement drives
│           ├── Profile.js                # Edit profile info
│           ├── ResumeUpload.js           # Upload & manage resumes
│           ├── AIAnalyzer.js             # AI resume analysis UI
│           ├── Notifications.js
│           ├── AdminDashboard.js
│           ├── AdminUsers.js
│           ├── AdminJobs.js
│           ├── AdminDrives.js
│           ├── AdminResumes.js
│           └── AdminAnalytics.js
│
├── ai-service/                       # Python Flask Microservice
│   ├── app.py                            # All AI logic here
│   └── requirements.txt
│
├── start_all.ps1                     # One-click startup script
└── README.md
```

---

## 🗄️ 4. Database Schema Diagram

```
┌──────────────────────────────────────┐
│               users                  │
├──────────────────────────────────────┤
│ id          BIGINT (PK, AUTO)        │
│ name        VARCHAR(255) NOT NULL    │
│ email       VARCHAR(255) UNIQUE      │
│ password    VARCHAR(255) BCrypt      │
│ role        ENUM('USER','ADMIN')     │
│ phone       VARCHAR                  │
│ college     VARCHAR                  │
│ branch      VARCHAR                  │
│ cgpa        DOUBLE                   │
│ created_at  DATETIME                 │
└──────────────┬───────────────────────┘
               │ 1 : N
               ▼
┌──────────────────────────────────────┐
│           job_applications           │
├──────────────────────────────────────┤
│ id                BIGINT (PK)        │
│ user_id           BIGINT (FK→users)  │
│ company           VARCHAR NOT NULL   │
│ role              VARCHAR NOT NULL   │
│ status            ENUM(APPLIED,      │
│                   INTERVIEW,         │
│                   REJECTED, OFFER)   │
│ application_date  DATE               │
│ interview_date    DATE               │
│ notes             TEXT               │
│ company_website   VARCHAR            │
│ company_logo      VARCHAR            │
│ application_source VARCHAR           │
└──────────────────────────────────────┘

               │ 1 : N (users → resumes)
               ▼
┌──────────────────────────────────────┐
│              resumes                 │
├──────────────────────────────────────┤
│ id                BIGINT (PK)        │
│ user_id           BIGINT (FK→users)  │
│ file_path         VARCHAR NOT NULL   │
│ original_filename VARCHAR            │
│ upload_date       DATETIME           │
│ analysis_result   TEXT (JSON stored) │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│            job_postings              │
├──────────────────────────────────────┤
│ id               BIGINT (PK)         │
│ company          VARCHAR NOT NULL    │
│ role             VARCHAR NOT NULL    │
│ location         VARCHAR NOT NULL    │
│ salary           VARCHAR             │
│ type             VARCHAR             │
│ drive_date       DATE                │
│ deadline         DATE                │
│ openings         INT                 │
│ description      TEXT                │
│ requirements     TEXT                │
│ skills           TEXT                │
│ external_link    VARCHAR             │
│ applicants_count INT DEFAULT 0       │
│ posted_date      DATETIME            │
│ active           BOOLEAN DEFAULT 1   │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│           notifications              │
├──────────────────────────────────────┤
│ id         BIGINT (PK)               │
│ user_id    BIGINT (FK→users, NULL=all│
│ title      VARCHAR NOT NULL          │
│ message    TEXT                      │
│ type       VARCHAR                   │
│ read       BOOLEAN DEFAULT 0         │
│ created_at DATETIME                  │
└──────────────────────────────────────┘
```

---

## 🔐 5. Authentication & Security Flow

### How JWT Works in This Project

```
1. User → POST /api/auth/login  { email, password }
2. Backend → BCrypt verifies password
3. Backend → JwtUtil.generateToken(email, role)
          → Signs HS256 token with secret key
          → Token contains: email, role, issuedAt, expiration
4. Frontend → stores token in localStorage
5. Every subsequent request:
   Frontend axios interceptor → adds "Authorization: Bearer <token>"
6. JwtFilter (OncePerRequestFilter):
   → Extracts token from header
   → JwtUtil.validateToken() → verifies signature & expiry
   → Sets SecurityContext with user details
7. @AuthenticationPrincipal UserDetails → controller gets current user
8. If token missing/expired → 401 → frontend redirects to /login
```

### Security Rules (SecurityConfig.java)
| Route | Access |
|-------|--------|
| `/api/auth/**` | Public (no auth needed) |
| `/swagger-ui/**` | Public |
| `/api/admin/**` | ADMIN role only |
| `/api/jobs/**` | Authenticated users |
| `/api/resume/**` | Authenticated users |
| `/api/ai/**` | Authenticated users |

---

## 🤖 6. AI Service — How It Works

### Tech Stack
- **Flask** — REST API framework
- **spaCy** (`en_core_web_sm`) — Named Entity Recognition (NER)
- **scikit-learn** (`TfidfVectorizer`, `cosine_similarity`) — Text matching
- **PyPDF2** — PDF text extraction
- **python-docx** — DOCX text extraction

### Endpoints
| Endpoint | Method | What it does |
|----------|--------|--------------|
| `/analyze` | POST | Analyze resume only — extracts skills, email, phone, improvements |
| `/match` | POST | Resume + Job Description → cosine similarity match score |
| `/health` | GET | Health check |

### Resume Analysis Pipeline
```
Upload File (PDF/DOCX)
       ↓
Extract Raw Text (PyPDF2 / python-docx)
       ↓
Extract Skills → Match against 60+ tech skill keywords
                 + spaCy NER (ORG, PRODUCT entities)
       ↓
Extract Email → Regex pattern
Extract Phone → Regex pattern
Extract Experience Years → Regex ("X years" / "X yrs")
       ↓
Generate Improvements:
   - Missing sections (Summary, Skills, Education, Experience)
   - Missing quantification (numbers/percentages)
   - Weak action verbs
   - Resume length check
   - Missing contact info
   - LinkedIn/GitHub links
       ↓
Return JSON result
```

### Resume Matching (TF-IDF + Cosine Similarity)
```python
vectorizer = TfidfVectorizer(stop_words='english', ngram_range=(1,2))
tfidf_matrix = vectorizer.fit_transform([resume_text, job_description])
similarity = cosine_similarity(tfidf_matrix[0:1], tfidf_matrix[1:2])[0][0]
match_score = similarity * 100  # 0-100%
```
> **Why TF-IDF?** It gives higher weight to rare/important words and ignores common words like "the", "and". Cosine similarity measures the angle between two document vectors — 100% means identical, 0% means completely different.

---

## 🔗 7. Service Connectivity Map

```
Frontend (React :3000)
    │
    ├─── axios → Spring Boot (:8080)
    │         ├── /api/auth/*        → AuthController
    │         ├── /api/jobs/*        → JobController
    │         ├── /api/resume/*      → ResumeController
    │         ├── /api/admin/*       → AdminController
    │         ├── /api/admin/job-postings/* → JobPostingController
    │         └── /api/ai/*          → AIController
    │                   │
    │                   └─── HTTP → Python AI Service (:5000)
    │                              ├── /analyze
    │                              └── /match
    │
    └─── Spring Boot ↔ MySQL (via Spring Data JPA / Hibernate)
```

### Frontend API Layer (`api/api.js`)
```javascript
// Auto-attaches JWT to every request
api.interceptors.request.use((config) => {
    const token = localStorage.getItem('token');
    if (token) config.headers.Authorization = `Bearer ${token}`;
    return config;
});

// Auto-redirects to login on 401
api.interceptors.response.use(
    (res) => res,
    (error) => {
        if (error.response?.status === 401) {
            localStorage.clear();
            window.location.href = '/login';
        }
    }
);
```

---

## 👥 8. User Roles & Features

### Regular User
| Feature | Description |
|---------|-------------|
| Register/Login | JWT-based auth |
| Dashboard | Stats: Total applied, interviews, offers, rejections |
| Jobs | Add/Edit/Delete job applications, filter by status |
| Drives | View placement drives posted by Admin |
| Resume Upload | Upload PDF/DOCX resumes, stored on server filesystem |
| AI Analyzer | Analyze resume or match against job description |
| Profile | View/edit personal info (phone, college, branch, CGPA) |
| Notifications | View admin-sent notifications |

### Admin
| Feature | Description |
|---------|-------------|
| Admin Dashboard | Platform stats: total users, jobs, resumes |
| User Management | View all users, delete users |
| Job Postings | Create/edit/delete job drives & postings |
| Resume Management | View all uploaded resumes platform-wide |
| Analytics | Charts: applied/interview/offer/rejected counts |
| Notifications | Send notifications to all users |

---

## 🛠️ 9. Technology Stack (Full List)

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| Frontend | React.js | 18.x | SPA framework |
| Frontend | React Router DOM | v6 | Client-side routing |
| Frontend | Axios | Latest | HTTP client |
| Frontend | react-hot-toast | Latest | Toast notifications |
| Frontend | TailwindCSS | 3.x | Styling |
| Frontend | Recharts | Latest | Analytics charts |
| Backend | Spring Boot | 3.2.2 | REST API framework |
| Backend | Spring Security | 6.x | Auth & authorization |
| Backend | Spring Data JPA | 3.x | ORM / DB access |
| Backend | Hibernate | 6.x | JPA implementation |
| Backend | JJWT | 0.11.5 | JWT generation/validation |
| Backend | MySQL Connector | Latest | DB driver |
| Backend | Springdoc OpenAPI | 2.3.0 | Swagger UI |
| Backend | Commons IO | 2.15.1 | File operations |
| Database | MySQL | 8.x | Relational DB |
| AI Service | Python | 3.10+ | AI microservice runtime |
| AI Service | Flask | 3.0.0 | REST framework |
| AI Service | spaCy | 3.7.2 | NLP / NER |
| AI Service | scikit-learn | 1.4.0 | TF-IDF + cosine similarity |
| AI Service | PyPDF2 | 3.0.1 | PDF parsing |
| AI Service | python-docx | 1.1.0 | DOCX parsing |
| AI Service | Gunicorn | 21.2.0 | Production WSGI server |
| DevOps | Docker | Latest | Containerization |
| DevOps | Java 17 | LTS | Backend runtime |

---

## 🚀 10. How to Run the Project

### Prerequisites
- Java 17 JDK
- Maven 3.8+
- Node.js 18+
- Python 3.10+
- MySQL 8.x running on port 3306

### Step 1 — Database Setup
```sql
CREATE DATABASE job_tracker;
```
> Spring Boot auto-creates tables via `spring.jpa.hibernate.ddl-auto=update`

### Step 2 — Backend
```bash
cd backend
mvn spring-boot:run
# Runs on http://localhost:8080
# Swagger UI: http://localhost:8080/swagger-ui/index.html
```

### Step 3 — AI Service
```bash
cd ai-service
pip install -r requirements.txt
python -m spacy download en_core_web_sm
python app.py
# Runs on http://localhost:5000
```

### Step 4 — Frontend
```bash
cd frontend
npm install
npm start
# Runs on http://localhost:3000
```

### OR — One Command (Windows)
```powershell
.\start_all.ps1
```

### Default Admin Credentials (seeded by DataLoader.java)
```
Email:    admin@jobtracker.com
Password: admin123
```

---

## ⚠️ 11. Challenges Faced & How I Solved Them

### Challenge 1: CORS Issues Between Services
**Problem:** React (port 3000) calling Spring Boot (8080) was blocked by browser CORS policy.  
**Solution:** Configured `CorsConfigurationSource` bean in `SecurityConfig.java` + added `SimpleCorsFilter`. Also had to configure `flask_cors` in the Python service separately.

---

### Challenge 2: Spring Security Blocking All Requests
**Problem:** After adding Spring Security, every endpoint returned 403, including the login endpoint itself.  
**Solution:** Used `SecurityFilterChain` bean (Spring Security 6 style — no extending `WebSecurityConfigurerAdapter`) and explicitly permitted `/api/auth/**` and Swagger paths. Also had to handle `OPTIONS` preflight requests.

---

### Challenge 3: JWT Role-Based Authorization
**Problem:** Admin routes were accessible by regular users.  
**Solution:** Stored `role` as a custom claim in the JWT. `JwtFilter` reads the role and sets `GrantedAuthority` as `ROLE_ADMIN`. Spring Security's `.hasRole("ADMIN")` checks for `ROLE_ADMIN` prefix automatically.

---

### Challenge 4: File Upload Storage
**Problem:** Storing uploaded resume files in the database (as BLOB) made queries slow and the DB too large.  
**Solution:** Saved files to the server filesystem at `uploads/resumes/{UUID}_{originalName}`. Only the file path is stored in the `resumes` table. This is the industry-standard approach for file handling.

---

### Challenge 5: PDF Text Extraction Failures
**Problem:** Some PDFs are image-based (scanned documents) — PyPDF2 returned empty text.  
**Solution:** Added validation — if extracted text is empty, return a clear error: *"The PDF may be image-based or corrupted."* The system cannot parse image PDFs without OCR (like Tesseract), which was out of scope.

---

### Challenge 6: N+1 Query Problem (Hibernate)
**Problem:** Fetching users with their job applications caused N+1 SELECT queries (1 for users + N for each user's applications).  
**Solution:** Used `FetchType.LAZY` on all `@OneToMany` relationships so child records are only loaded when explicitly accessed.

---

### Challenge 7: React State Management Across Routes
**Problem:** JWT token and user data needed to be available on every page without prop drilling.  
**Solution:** Stored `user` and `token` in `localStorage` and lifted state to the top-level `App.js`. The Axios interceptor auto-reads the token on every request — no need for Redux or Context for this scale.

---

### Challenge 8: Role-Based Frontend Routing
**Problem:** Users could manually navigate to `/admin/dashboard` by typing the URL.  
**Solution:** Wrapped every route with authentication checks in `App.js`. If user is not ADMIN, `<Navigate to="/dashboard" />` redirects them. Server-side, the `SecurityConfig` enforces `.hasRole("ADMIN")` independently.

---

### Challenge 9: TF-IDF Giving Unexpected Low Scores
**Problem:** A resume with many matching skills would still get a 20% match score.  
**Solution:** Added `ngram_range=(1,2)` to the `TfidfVectorizer` — this allows bigrams like "spring boot" or "machine learning" to be treated as single tokens, improving accuracy. Also added direct skill keyword matching as a separate step.

---

### Challenge 10: DataLoader Running on Every Restart
**Problem:** `CommandLineRunner` (DataLoader) seeded the admin user every time the app started, causing duplicate key errors.  
**Solution:** Added a check: `if (!userRepository.existsByEmail("admin@jobtracker.com"))` before inserting.

---

## ❓ 12. Common Interview Questions & Answers

### Q1: What is the purpose of this project?
> It's a platform for students to track job applications, view campus drives, and use AI to improve their resumes. Admins manage job postings, users, and platform analytics. It mirrors real-world enterprise architecture with three separate services.

### Q2: Why did you use three separate services instead of one monolith?
> Separation of concerns. The Java backend handles business logic and data. The Python AI service uses NLP libraries (spaCy, scikit-learn) that are best-in-class in Python — impossible in Java without similar overhead. The React frontend is independently deployable. Each service can be scaled separately.

### Q3: How does JWT authentication work?
> After login, the server creates a signed token containing the user's email and role using HMAC-SHA256. The client stores it and sends it in every `Authorization: Bearer` header. The server validates the signature on each request — no session storage needed (stateless).

### Q4: What is TF-IDF?
> Term Frequency-Inverse Document Frequency. TF measures how often a word appears in a document. IDF penalizes words that appear in many documents (like "the"). Together, they score how important a word is to a specific document. We use it to turn resume and job description text into vectors, then measure their similarity with cosine similarity.

### Q5: What is cosine similarity?
> It measures the angle between two vectors in multi-dimensional space. 0° angle = identical (score 1.0), 90° angle = completely different (score 0.0). We multiply by 100 to get a percentage match score.

### Q6: How is role-based access control implemented?
> Two layers:
> 1. **Backend (server-side):** `SecurityConfig` uses `.hasRole("ADMIN")` on admin routes. `JwtFilter` extracts the role from the JWT and sets it as a `GrantedAuthority`.
> 2. **Frontend (client-side):** `App.js` checks `user.role` before rendering routes and redirects unauthorized users.

### Q7: Why BCrypt for passwords?
> BCrypt is a one-way hash with a cost factor (work factor). Even if the database is breached, hashed passwords cannot be reverse-engineered. Unlike MD5/SHA, BCrypt is intentionally slow (adaptive) — it resists brute-force attacks.

### Q8: What is Spring Data JPA?
> An abstraction over JPA/Hibernate. Instead of writing SQL, you define entity classes (`@Entity`) and repository interfaces (`JpaRepository`). Spring auto-generates the SQL at runtime. Methods like `findByEmail()` or `countByStatus()` are auto-implemented from method names.

### Q9: Why store resume files on the filesystem, not the database?
> BLOBs in databases slow down every query (table size increases, backups are huge, indexing is inefficient). The industry standard is to store files on a filesystem or object storage (like AWS S3), and only store the file path in the database.

### Q10: How is the project containerized?
> Each service has a `Dockerfile`. The backend uses a multi-stage build: first stage compiles Java with Maven, second stage copies only the JAR into a slim JRE image. This reduces the final image size significantly.

### Q11: What is the `@PrePersist` annotation in `JobPosting.java`?
> It's a JPA lifecycle callback — automatically executed before a new entity is saved to the database. I use it to set `postedDate = LocalDateTime.now()` so the timestamp is always server-generated, not client-provided.

### Q12: What improvements would you make?
> 1. Add **Redis** caching for dashboard stats (expensive aggregation queries)
> 2. Use **AWS S3** instead of local filesystem for resume storage
> 3. Add **WebSockets** for real-time notifications
> 4. Integrate **OAuth2** (Google/LinkedIn) for social login
> 5. Add **email notifications** using JavaMailSender
> 6. Deploy with **Docker Compose** or **Kubernetes**
> 7. Add **unit tests** with JUnit + Mockito + pytest

---

## 📋 13. API Endpoints Reference

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/api/auth/register` | Public | Register new user |
| POST | `/api/auth/login` | Public | Login, get JWT |
| GET | `/api/jobs` | User | Get my job applications |
| POST | `/api/jobs` | User | Add job application |
| PUT | `/api/jobs/{id}` | User | Update application |
| DELETE | `/api/jobs/{id}` | User | Delete application |
| PATCH | `/api/jobs/{id}/status` | User | Update status only |
| GET | `/api/jobs/dashboard` | User | Get stats |
| POST | `/api/resume/upload` | User | Upload PDF/DOCX |
| GET | `/api/resume` | User | Get my resumes |
| DELETE | `/api/resume/{id}` | User | Delete resume |
| POST | `/api/ai/analyze` | User | AI resume analysis |
| POST | `/api/ai/match` | User | Resume vs JD match |
| GET | `/api/admin/dashboard` | Admin | Platform stats |
| GET | `/api/admin/users` | Admin | All users |
| DELETE | `/api/admin/users/{id}` | Admin | Delete user |
| GET | `/api/admin/analytics` | Admin | Status analytics |
| GET | `/api/admin/job-postings` | All Auth | View job postings |
| POST | `/api/admin/job-postings` | Admin | Create job posting |
| GET | `/api/notifications` | User | Get notifications |

---

## 🎓 14. Key Concepts You Must Know

| Concept | How Used In Project |
|---------|---------------------|
| REST API | All inter-service communication |
| JWT | Stateless auth, role embedded in token |
| Spring Security Filter Chain | Every request passes through `JwtFilter` |
| JPA/Hibernate | ORM mapping Java classes ↔ MySQL tables |
| Microservices | 3 independent services communicating over HTTP |
| CORS | Configured to allow cross-origin requests |
| BCrypt | Password hashing (one-way, salted) |
| TF-IDF | Resume-to-JD text similarity |
| Cosine Similarity | Similarity score calculation |
| spaCy NER | Named entity extraction from resume text |
| React Router | Client-side SPA navigation |
| Axios Interceptors | Auto-attach JWT, handle 401 globally |
| Lazy Loading (JPA) | Avoid N+1 query problem |
| Multi-stage Docker | Smaller production image |
| `@PrePersist` | JPA lifecycle hook for auto-timestamps |
| ENUM in DB | Status fields stored as strings in MySQL |

---

*Built with ❤️ — AI-Powered Job Application Tracker*
