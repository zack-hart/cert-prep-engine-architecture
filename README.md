# Certification Preparation Engine

> *Note: The source code for this application is held in a private repository for security and API protection. This repository serves as the architectural overview and technical documentation.*

**A multi-tenant, cloud-hosted SaaS educational platform optimized for cost, scale, and AI-driven diagnostics.**

## Executive Summary
The Cert Prep Engine is a certification-agnostic simulation platform designed to replace static study materials with algorithmic testing. Built from scratch without heavy frontend frameworks to maximize performance, the platform mathematically mirrors official exam blueprints, isolates user data in a multi-tenant PostgreSQL database, and utilizes an MD5-hashed caching layer to deliver AI-generated tutoring at near-zero latency and minimal API cost.

## Technical Stack & Infrastructure
*   **Backend:** Python 3 / Flask / Gunicorn (WSGI)
*   **Frontend:** Vanilla JavaScript (ES6) / Native CSS3 (Zero-dependency architecture)
*   **Database:** PostgreSQL (Relational schema via Neon.tech Serverless Cloud) with local SQLite fallback for development environments
*   **Hosting / DevOps:** Render.com (Stateless compute) with automated CI/CD pipeline via GitHub
*   **AI Integration:** Groq API (High-speed LPU inference for real-time explanations and automated question generation)
*   **Security:** Werkzeug PBKDF2 SHA-256 Hashing, Secure Session Middleware

## Core Engineering Achievements

### 1. Enterprise Multi-Tenant Architecture & Auth
*   **Stateless Compute:** Engineered the application to run on ephemeral instances (Render), routing database calls securely to an external serverless PostgreSQL cluster.
*   **Role-Based Access Control (RBAC):** Implemented custom Werkzeug-backed authentication middleware. All database queries natively enforce `user_id` scoping to guarantee strict data isolation between concurrent students.
*   **Secure Admin Gateway:** Built a hidden administrative dashboard for user lifecycle management, eliminating public-facing signup vectors and minimizing attack surfaces.

### 2. AI Caching & Compute Cost Optimization
*   **The Challenge:** Repeated calls to the AI API for identical missed questions waste cloud compute, incur redundant API costs, and create unacceptable UI latency.
*   **The Solution:** Engineered a cryptographic caching layer. When a student requests an explanation, the engine generates an MD5 hash of the `question_text` + `correct_answer` payload and checks the global `ai_cache` PostgreSQL table.
*   **Business Impact:** Cache hits serve explanations locally in milliseconds, drastically reducing token consumption and driving the marginal cost of AI tutoring per student toward zero.

### 3. Algorithmic Blueprint Engine & Data Serialization
*   **Mathematical Weighting:** Replaced randomized question selection with a dynamic "Blueprint Algorithm" that ingests JSON banks and generates exams that perfectly mirror official certification domain weights.
*   **LLM Ingestion Pipeline:** Built a custom admin endpoint (`/api/admin/generate_questions`) leveraging the Groq API to ingest raw study materials and automatically parse them into standardized JSON question banks.
*   **JSON Session Rehydration:** Entire exam sessions, including exact incorrect choices and time spent, are serialized as JSON payloads in PostgreSQL, allowing users to "re-hydrate" past exams with perfect fidelity.

## Product & UX Features
*   **The Neural Vault:** An automated algorithmic penalty box that tracks missed questions by `cert_id` and `user_id`, forcing students to interact with their weakest concepts.
*   **Targeted Diagnostic Analytics:** Exam results are parsed by specific domain percentages to immediately highlight cognitive gaps.
*   **Behavioral Gamification:** Tracks daily streaks and injects cumulative XP to move students through thresholds (Student > Practitioner > Exam Ready > Certified).
*   **State Persistence:** Leverages browser local storage to cache active exam states, allowing users to close sessions and resume unfinished exams without data loss.
*   **Dynamic UI Theming:** Fully integrated Light/Dark mode toggling driven by CSS variables and local storage preferences.

## Future Roadmap (v4)
*   **Air-Gapped Local AI Integration:** Transitioning from the external Groq API to quantized local models (Llama 3 / Gemma) running via Ollama for high-security, internet-restricted environments.
*   **B2B Heatmap Analytics:** Developing an instructor-facing dashboard to aggregate domain-level performance data across an entire student cohort.

## Core Project Structure
Demonstrating clean separation of concerns between backend routing, data ingestion, and client-side rendering.

```text
cert-prep-engine/
├── app.py                   # Main Flask application, RBAC, and routing logic
├── ingest.py                # LLM ingestion pipeline for parsing raw question banks
├── static/                  # Vanilla JS and Native CSS3 assets (Zero-dependency)
├── templates/               # HTML5 Jinja2 templates (login, exam engine, admin)
├── questions_netplus.json   # Serialized certification data structures
├── exam_history.db          # SQLite database fallback for local development
└── requirements.txt         # WSGI, Postgres, and Python dependencies
