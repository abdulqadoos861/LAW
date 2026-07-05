# Pakistan Law Aggregator - User Guide

A real-time legislative tracking and alert intelligence portal designed for a Pakistani Law Agency. This platform crawls federal and provincial assemblies, parses new enactments/bills, and fires email updates to legal desks while providing an administrative dashboard.

---

## 🛠️ Technology Stack

### Backend
- **Core Engine**: [FastAPI](https://fastapi.tiangolo.com/) (Python 3.10+) for lightweight, high-performance async APIs.
- **ORM & Database**: [SQLAlchemy](https://www.sqlalchemy.org/) with [SQLite](https://www.sqlite.org/) for local, self-contained relational storage.
- **Scraper Pipeline**: [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) & [Requests](https://requests.readthedocs.io/) for HTML table parsing and content extraction.
- **Server Runner**: [Uvicorn](https://www.uvicorn.org/) ASGI web server.

### Frontend
- **Framework**: [Next.js](https://nextjs.org/) 14+ (React 18, App Router layout structure).
- **Type Safety**: [TypeScript](https://www.typescriptlang.org/) for compile-time interface safety.
- **Styling**: Vanilla CSS with CSS-in-JS (global JSX styles) featuring an elegant dark mode palette with gold/emerald accents and glassmorphism.

### Alert System
- **SMTP Gateway**: Python `smtplib` with MIME HTML body formatting. Configured to dispatch emails securely via `smtp.gmail.com` using TLS.
- **Fallback Alerting**: Resend.com API integration.

---

## ⚡ Core Features & Functionalities

### 1. Legislative Web Scrapers (`backend/scraper/sites/`)
Dedicated parsers scrape the following legislative sources:
- 🏛️ **National Assembly & Senate**: Federal bills, acts, and amendments.
- 📜 **Ministry of Law (Pakistan Code)**: Scrapes the "Latest Uploads" section for newly gazetted ordinances.
- 🟢 **Punjab Assembly**: Provincial enactments and draft bills.
- 🔵 **Sindh Assembly**: Provincial bills, acts, and statutory rules.

### 2. Precise Publication Date Heuristics (`crawler.py`)
- Replaces simple year checks with an exact date-string regex parser that normalizes formats (e.g., `30th June 2026`, `27 March,2017`, `20 September 2024`).
- Ensures manual/automatic triggers filter out older historical entries, capturing only laws passed on or after a specified date.

### 3. SMTP Alert Notifications (`notifier/notify.py`)
- Automated notification dispatches whenever a crawl session completes.
- Builds high-quality HTML summary digests containing titles, categories, and direct download links.
- Routes alert digests to configured administrator email mailboxes.

### 4. Admin Dashboard Control Panel (`frontend/src/app/admin/`)
Provides full control over aggregator components:
- **Dashboard Tab (Default)**: Displays today's legislative updates grouped by assembly node with quick links to download documents.
- **Crawler logs console**: Real-time terminal simulator showing scrape progress, resolved active links, and new records count.
- **Sources Tab**: Edit base URLs, toggle active status, or delete scraping targets.
- **Receivers Tab**: Add and remove email addresses that receive alert notifications.
- **Profile Tab**: Shows current login name and permits updating the admin password.

### 5. Secure Authentication Engine (`routes/auth.py`)
- Admin panels are protected using standard PBKDF2 HMAC-SHA256 password hashing.
- Issues 24-hour cryptographically secure session tokens (`Bearer` headers) on login.
- Rotating the administrator password automatically invalidates and removes all active tokens, forcing an immediate re-login.

### 6. Public Portal Pages (`frontend/src/app/`)
- **Public Feed (Home)**: Search and filter updates by category or assembly.
- **About Us**: Mission statements and legislative aggregation goals.
- **Services**: Legal advisory, compliance desk mapping, policy drafting, and litigation services list.
- **Contact Us**: Contact details, direct Gmail address (`romangray197@gmail.com`), and an interactive support query form.

---

## 🚀 How to Run the Project

### Prerequisites
Ensure you have the following installed:
- Python 3.10+
- Node.js 18+ & npm

---

### Backend Setup

1. **Navigate to the Backend Directory**:
   ```bash
   cd backend
   ```

2. **Create a Virtual Environment**:
   ```bash
   python -m venv venv
   # Activate on Windows:
   venv\Scripts\activate
   # Activate on Mac/Linux:
   source venv/bin/activate
   ```

3. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```
   *(If `requirements.txt` is not present, install the core packages: `pip install fastapi uvicorn sqlalchemy requests beautifulsoup4 pydantic cryptography`)*

4. **Configure Environment Variables (`.env`)**:
   Create a `.env` file in the root of the `backend/` folder:
   ```env
   # SMTP Gateway Configuration
   ADMIN_FROM_EMAIL=duaafayyaz5@gmail.com
   MAIL_PASSWORD=mjha atip xblw drrq  # Google App Password

   # Optional: Custom default admin login credentials
   ADMIN_LOGIN_USERNAME=admin
   ADMIN_LOGIN_PASSWORD=admin123
   ```

5. **Start Uvicorn Server**:
   ```bash
   python main.py
   ```
   The backend API will run at `http://127.0.0.1:8000`.

---

### Frontend Setup

1. **Navigate to the Frontend Directory**:
   ```bash
   cd ../frontend
   ```

2. **Install Packages**:
   ```bash
   npm install
   ```

3. **Start Next.js Development Server**:
   ```bash
   npm run dev
   ```
   The user interface will be served at `http://localhost:3000`.

---

## 🔍 Verifying the System

### Automated Integration Tests
You can run automated test scripts in the background to verify features:

- **Verify Date Filtration Rules**:
  ```bash
  python backend/scratch/test_exact_date.py
  ```
- **Verify SMTP Alerts Dispatch**:
  ```bash
  python backend/scratch/test_smtp.py
  ```
- **Verify Admin Login & Password Rotation**:
  ```bash
  python backend/scratch/test_password_change.py
  ```
