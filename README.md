# CommitForge

**A Professional Project Submission & Review Management Platform**

---

## 📌 Project Title & Overview

### What is CommitForge?

CommitForge is a web-based platform designed to streamline the process of submitting, managing, and reviewing projects. It provides a structured workflow where contributors can submit their work, reviewers can provide detailed feedback, and administrators can oversee the entire review process.

### Problem It Solves

**Challenges Addressed:**
- 🎯 **Unorganized submission process** - Projects scattered across emails, cloud storage, or messaging apps
- 📋 **Lack of version control** - Difficult to track project history and changes over time
- 👥 **Communication gaps** - Contributors don't know the status of their submissions; reviewers can't coordinate feedback
- ⚙️ **No central management** - Admins struggle to assign reviewers and track project statuses
- 📊 **Missing audit trail** - No record of what changed and when

### Target Users

- **Contributors**: Developers, students, or team members who want to submit projects for review
- **Reviewers**: Subject matter experts, team leads, or quality assurance personnel who evaluate submissions
- **Administrators**: Project managers, supervisors, or system administrators who oversee the entire workflow

---

## 🎯 Product Requirements Document (PRD)

### Project Objective

To create a robust, user-friendly platform that:
1. Enables structured project submissions with file attachments
2. Facilitates collaborative review process with multiple reviewers
3. Maintains complete audit trail of project history
4. Provides role-based access control and security
5. Keeps users informed through real-time notifications

### Key Features & Functionalities

#### For Contributors:
- ✅ User registration and authentication
- 📤 Submit projects with title, description, category, GitHub link, demo link, and ZIP file
- 📝 Edit submitted projects (within 5-hour edit window)
- 🗑️ Soft delete projects (move to trash, restore later)
- 📜 View complete project history with all changes
- 🔔 Receive notifications on project assignments and status updates
- 📊 Dashboard showing all submitted and active projects

#### For Reviewers:
- ✅ User registration and authentication
- 📋 View assigned projects on personal dashboard
- 🔍 Download and review project files (ZIP uploads)
- 💬 Provide detailed feedback on projects
- ✔️ Approve or reject projects with decision documentation
- 📜 View complete review history with feedback records
- 📂 Access uploaded project files from centralized folder
- 🔔 Receive notifications when assigned to review projects

#### For Administrators:
- 👤 Create, manage, and delete user accounts (all roles)
- 🏢 Assign projects to one or multiple reviewers
- 📊 View all projects with contributor and reviewer information
- 🔄 Manually update project status
- 📋 Manage project statuses: Submitted, Assigned, In Review, Revision Requested, Approved, Rejected, Deleted
- 👥 View comprehensive dashboard with users, projects, and reviews
- 🔔 Receive notifications related to project management

#### For All Users:
- 🔐 Secure login and session management
- 🚪 Account deletion with cascading data cleanup
- 📬 Notification center with read/unread status
- 🔔 Notification management (mark as read, clear all)
- 👤 Role-based access control (RBAC)

### User Stories

**As a Contributor**, I want to:
- Submit my project quickly with all necessary details so it can be reviewed
- Edit my project within a short time window before it's assigned to reviewers
- Track what feedback reviewers provided on my project
- Restore accidentally deleted projects from a trash folder
- Know when my project has been assigned and who will review it

**As a Reviewer**, I want to:
- See all projects assigned to me for review
- Download project files to test them locally
- Provide detailed feedback and an approval/rejection decision
- View my complete review history
- Be notified when new projects are assigned to me

**As an Admin**, I want to:
- Manage all user accounts in the system
- Assign projects to appropriate reviewers
- Track project status throughout the workflow
- Ensure smooth project flow from submission to completion
- Have a complete audit trail of all changes

### Functional Requirements

#### Authentication & Security:
- User registration with name, email, password, and role selection
- Secure login validation
- Session management with role-based redirects
- Logout functionality
- Account deletion with data cleanup
- Password storage (currently plain text - see Security notes)

#### Project Management:
- Submit projects with: title, description, category, GitHub link, demo link, ZIP file
- Store projects with timestamps: created_at, updated_at, deleted_at
- Project status workflow: Submitted → Assigned → In Review → Approved/Rejected
- Complete project history tracking with action logs
- Soft delete functionality for projects
- Project restore capability

#### Review Workflow:
- Multi-reviewer assignment per project
- Reviewer-project relationship tracking
- Review submission with feedback and decision
- Project status update based on review decision
- Review history accessible to reviewers

#### Notification System:
- Create notifications for key events (assignments, status changes)
- Notify contributors when projects are assigned
- Notify reviewers when projects are assigned to them
- Mark notifications as read
- Clear individual or all notifications
- Display unread notification count

#### Data Tracking:
- Complete project history with all changes
- Action logging: created, updated, assigned, reviewed, deleted
- Timestamp tracking in IST timezone
- Contributor and project history correlation

### Non-Functional Requirements

#### Performance:
- Database queries should complete within 500ms
- Page load time under 2 seconds
- Support 100+ concurrent users
- File uploads up to 100MB ZIP files

#### Security:
- Implement HTTPS in production
- Use parameterized SQL queries (protection against SQL injection) ✅
- Implement password hashing (currently using plain text - needs improvement)
- Session timeout after inactivity
- CSRF protection
- Secure file upload validation (ZIP only)
- File access authentication before download

#### Scalability:
- Database structure supports growth to 10,000+ projects
- Modular route-based architecture for easy feature additions
- Horizontal scaling ready (stateless design)
- File storage size management strategy

#### Reliability:
- Database transaction rollback on errors
- Error handling for file operations
- Cascade delete handling for referential integrity
- Backup and recovery strategy

#### Maintainability:
- Clean, modular code structure
- Separation of concerns (routes, models, utilities)
- Well-organized template structure
- Helper functions for common operations

---

## 🧠 System Architecture

### High-Level Architecture Overview

CommitForge follows a **Three-Tier Architecture Pattern**:

```
┌─────────────────────────────────────────────────────────────┐
│                   PRESENTATION LAYER                         │
│              (HTML Templates + JavaScript)                   │
│  Base Layout │ Auth Pages │ Dashboards │ Admin Pages         │
└──────────────────────────┬──────────────────────────────────┘
                           │ HTTP Requests/Responses
┌──────────────────────────▼──────────────────────────────────┐
│                   APPLICATION LAYER                          │
│                    (Flask Application)                       │
│  ┌─────────────┬─────────────┬─────────────┬──────────────┐ │
│  │Auth Routes  │Contributor  │Reviewer     │Admin Routes  │ │
│  │             │Routes       │Routes       │              │ │
│  └─────┬───────┴──────┬──────┴──────┬──────┴────────┬─────┘ │
│        │               │             │               │       │
│  ┌─────▼───────────────▼─────────────▼───────────────▼─────┐ │
│  │              UTILITY LAYER                              │ │
│  │  ┌──────────────┬─────────────┬──────────────────────┐ │ │
│  │  │ Database (db)│ Auth (auth) │ Notifications        │ │ │
│  │  │              │             │ Helpers              │ │ │
│  │  └──────────────┴─────────────┴──────────────────────┘ │ │
│  └──────────────────────────────────────────────────────┘ │
└──────────────────────────┬──────────────────────────────────┘
                           │ SQL/CRUD Operations
┌──────────────────────────▼──────────────────────────────────┐
│                   DATA LAYER                                │
│              (SQLite Database)                              │
│  Users │ Projects │ Reviews │ Notifications │ History      │
└─────────────────────────────────────────────────────────────┘
```

### Technology Stack

#### Backend:
- **Framework**: Flask (Python Web Framework)
- **Language**: Python 3.x
- **Database**: SQLite3 (Relational Database)
- **File Handling**: Werkzeug (Secure file uploads)
- **Server**: Gunicorn (Production WSGI Server)

#### Frontend:
- **HTML5**: Semantic markup for templates
- **CSS**: Custom styling (static/css/style.css)
- **JavaScript**: Client-side interactivity (static/js/script.js)
- **Session Management**: Flask built-in sessions

#### Database Schema:

**Users Table:**
```
id (PK) | name | email (UNIQUE) | password | role
```

**Projects Table:**
```
id (PK) | contributor_id (FK) | title | description | category | 
demo_link | github_link | zip_path | status | created_at | 
updated_at | deleted_at
```

**Reviews Table:**
```
id (PK) | project_id (FK) | reviewer_id (FK) | feedback | decision
```

**Project Reviewers Table:**
```
id (PK) | project_id (FK) | reviewer_id (FK) | assigned_at | 
UNIQUE(project_id, reviewer_id)
```

**Project History Table:**
```
id (PK) | project_id (FK) | contributor_id (FK) | title | 
description | category | demo_link | github_link | zip_path | 
status | action | recorded_at
```

**Notifications Table:**
```
id (PK) | user_id (FK) | message | type | related_project_id | 
read_status | created_at
```

### Data Flow & Interactions

#### Project Submission Flow:
```
Contributor Submits Project → Validation → File Upload → 
Database Entry → Project History Record → Dashboard Update
```

#### Project Assignment Flow:
```
Admin Assigns Reviewers → Database Update → Notifications Sent → 
Project Status Changed → History Recorded → Reviewers Notified
```

#### Review Submission Flow:
```
Reviewer Submits Review → Database Entry → Project Status Updated → 
History Record → Notifications Sent → Contributor Notified
```

---

## ⚙️ Workflow Explanation

### Complete User Journey

#### **Contributor Journey:**

1. **Registration Phase:**
   - User visits `/register`
   - Enters: name, email, password
   - Selects role: "Contributor"
   - Account created in `users` table

2. **Project Submission:**
   - Navigate to `/contributor/submit`
   - Fill project details:
     - Title, Description, Category
     - GitHub link (required)
     - Demo link (optional)
     - Upload ZIP file (required)
   - Submit → File saved to `/uploads` → Project stored in database
   - Project history record created with action: "created"
   - Redirected to contributor dashboard

3. **Project Monitoring:**
   - View dashboard at `/contributor/dashboard`
   - See all submitted projects with status
   - View notifications of assignments
   - Click on project to see details or history

4. **Project Editing (5-hour window):**
   - Can edit project details if editable window not expired
   - Changes saved → Project history updated
   - After 5 hours, editing disabled

5. **Project History:**
   - Visit `/contributor/history/<project_id>`
   - See all changes in chronological order
   - Clear history if needed

6. **Project Deletion:**
   - Soft delete (not hard delete) moves project to trash
   - Can restore from `/contributor/deleted`
   - Status changed to "Deleted"

#### **Reviewer Journey:**

1. **Registration:**
   - Register as "Reviewer" role
   - Credentials stored in database

2. **View Assignments:**
   - Login → Redirected to `/reviewer/dashboard`
   - See all projects assigned to reviewer
   - Status shows: Submitted, Assigned, In Review, etc.

3. **Download & Review:**
   - Click on project to review
   - Download ZIP file
   - View project metadata (GitHub link, demo link, description)
   - File information displayed: size, modification date

4. **Provide Feedback:**
   - Access `/review/<project_id>`
   - Write detailed feedback
   - Select decision: "Approved" or "Rejected"
   - Submit → Review stored in `reviews` table
   - Project status updated to selected decision
   - History record created with action: "reviewed"

5. **View History:**
   - Visit `/reviewer/history`
   - See all reviews provided with feedback
   - Click to view project details
   - Clear review history if needed

6. **Project Visibility:**
   - Can only see projects assigned to them
   - Can also see reviewed projects even after assignment removed

#### **Admin Journey:**

1. **Admin Dashboard Access:**
   - Login as Admin role
   - Redirected to `/admin/dashboard`
   - Complete overview of:
     - All users (Contributor, Reviewer, Admin)
     - All projects with contributor info
     - Available reviewers

2. **User Management:**
   - Visit `/admin/users/add`
   - Create new users with role assignment
   - Can create Contributors, Reviewers, or Admins

3. **Project Assignment:**
   - On dashboard, view all projects
   - For each project, select one or multiple reviewers
   - Update assignment → Reviewers added to `project_reviewers` table
   - Status automatically changes from "Submitted" to "Assigned"
   - Notifications sent to:
     - Selected reviewers about assignment
     - Contributor about reviewer assignments

4. **Status Management:**
   - Manually update project status
   - Options: Submitted, Assigned, In Review, Revision Requested, Approved, Rejected, Deleted
   - Updates recorded in project history

5. **Project Monitoring:**
   - View assigned reviewers for each project
   - Track project progress through workflow
   - See all changes in project history

### Backend Processing Logic

#### **Request-Response Flow:**

```
1. User Action (Form Submit, Click)
   ↓
2. Route Handler Receives Request
   - Validates request method (GET/POST)
   - Checks session for authentication
   - Validates user role
   ↓
3. Database Query
   - Execute SQL (parameterized queries)
   - Fetch/Insert/Update/Delete data
   - Maintain transaction integrity
   ↓
4. Business Logic
   - Process file uploads (validate, save)
   - Update related tables (cascade updates)
   - Create notifications
   - Record in project history
   ↓
5. Response
   - Render HTML template (Jinja2)
   - Redirect to appropriate page
   - Return file download
   ↓
6. Client Receives Response
   - Render page in browser
   - Display feedback to user
```

#### **Key Processing Steps:**

**Project Submission Processing:**
```python
@contributor_bp.route("/contributor/submit", methods=["POST"])
def submit():
    # 1. Validate inputs (project_type, github_link, zip_file)
    # 2. Check file type (must be .zip)
    # 3. Save ZIP file to /uploads directory
    # 4. Create projects table entry
    # 5. Create project_history record (action: "created")
    # 6. Return success response
```

**Project Assignment Processing:**
```python
@admin_bp.route("/admin/projects/<int:project_id>/assignment", methods=["POST"])
def update_project_assignment(project_id):
    # 1. Get selected reviewer IDs
    # 2. Validate reviewers exist and have Reviewer role
    # 3. Clear existing project_reviewers entries
    # 4. Insert new project_reviewers rows
    # 5. Update project status (Submitted → Assigned)
    # 6. Create project_history record (action: "assigned_to_...")
    # 7. Send notifications to reviewers
    # 8. Send notifications to contributor
```

**Review Submission Processing:**
```python
@reviewer_bp.route("/review/<int:id>", methods=["POST"])
def review(id):
    # 1. Validate project assignment
    # 2. Get feedback and decision from form
    # 3. Create reviews table entry
    # 4. Update project status (status = decision)
    # 5. Create project_history record (action: "reviewed")
    # 6. Return to dashboard
```

#### **Notification System Logic:**

```
Event Triggered (Assignment, Status Change)
     ↓
create_notification() called with:
  - user_id (who to notify)
  - message (what to tell them)
  - notification_type (assignment, status_update)
  - related_project_id (which project)
     ↓
Database Entry Created in notifications table
     ↓
User Receives Notification in Dashboard
     ↓
User Can Mark as Read or Clear All
```

### Error Handling & Data Integrity

- **SQL Errors**: Caught with try-except, rollback on failure
- **File Upload Errors**: Validate file type, size, and save location
- **Authentication**: Check session before processing
- **Authorization**: Verify user role for each action
- **Cascade Deletes**: Delete related notifications, reviews on account deletion
- **Data Consistency**: Transactions ensure atomicity

---

## 📂 Folder Structure Explanation

### Root Level Files:

| File | Purpose |
|------|---------|
| `app.py` | Main Flask application entry point, blueprint registration, upload routing |
| `config.py` | Configuration constants (database path, upload folder, secret key) |
| `requirements.txt` | Python dependencies (Flask, Gunicorn) |
| `database.db` | SQLite database file (auto-created on first run) |

### `/models` Directory:

Contains simple data model classes:
- `user_model.py` - User class with id, name, email, role
- `project_model.py` - Project class with id, title, status
- `review_model.py` - Review class with id, feedback, decision
- `__init__.py` - Package initialization

**Purpose**: Define data structures (currently minimal, mostly using database directly)

### `/routes` Directory:

Contains Flask blueprint route handlers for different user roles:

| File | Purpose |
|------|---------|
| `auth_routes.py` | Login/register/logout, home redirect based on role, account deletion, notifications |
| `contributor_routes.py` | Project submission, editing, deletion, history, restore, dashboard |
| `reviewer_routes.py` | Project dashboard, review submission, history, file access, project visibility |
| `admin_routes.py` | User management, project assignment, status updates, admin dashboard |
| `__init__.py` | Package initialization |

### `/utils` Directory:

Helper functions and utility modules:

| File | Purpose |
|------|---------|
| `db.py` | Database connection, initialization, schema creation |
| `auth.py` | Authentication utilities (if present) |
| `helpers.py` | File upload validation, datetime formatting (IST timezone) |
| `notifications.py` | Notification CRUD, notification sending functions |
| `__init__.py` | Package initialization |

### `/templates` Directory:

HTML templates for rendering pages:

**Root templates:**
- `base.html` - Base layout template (header, navigation, footer)
- `index.html` - Home page
- `login.html` - Login form
- `register.html` - Registration form
- `notifications.html` - Notifications page

**`/templates/contributor/` - Contributor pages:**
- `dashboard.html` - Active projects view
- `submit_project.html` - Project submission form
- `edit_project.html` - Edit project details
- `project_detail.html` - Project information display
- `deleted_projects.html` - Soft-deleted projects (trash)
- `project_history.html` - Change history for a project

**`/templates/reviewer/` - Reviewer pages:**
- `dashboard.html` - Assigned projects view
- `review_project.html` - Review form with file download
- `review_history.html` - Submitted reviews
- `project_history.html` - Project change history
- `uploads.html` - Centralized file browser

**`/templates/admin/` - Admin pages:**
- `dashboard.html` - All projects, users, assignments, status management
- `add_user.html` - User creation form
- `manage_projects.html` - Project management interface

### `/static` Directory:

Static assets served by the web server:

**`/static/css/`:**
- `style.css` - Main stylesheet for all pages

**`/static/js/`:**
- `script.js` - Client-side JavaScript functionality

### `/uploads` Directory:

Server-side file storage:
- Stores uploaded project ZIP files
- Files accessible via `/uploads/<filename>` route
- Secure file serving with validation

### Key File Relationships:

```
requests → routes/*.py → utils/*.py → database.db
                      ↓
            templates/*.html
                      ↓
        static/css/style.css + js/script.js
```

---

## 🚀 Installation & Setup

### Prerequisites

Before you begin, ensure you have the following installed:

- **Python 3.7+** ([Download](https://www.python.org/downloads/))
- **pip** (Python package manager, included with Python)
- **Git** (optional, for version control)
- **Virtual Environment** (Python's venv module)

### Step-by-Step Installation

#### 1. Clone or Download the Repository

```bash
# If using git:
git clone <repository-url>
cd CommitForge

# Or extract the project folder
cd CommitForge
```

#### 2. Create a Virtual Environment

```bash
# Windows
python -m venv venv

# macOS/Linux
python3 -m venv venv
```

#### 3. Activate Virtual Environment

```bash
# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate
```

#### 4. Install Dependencies

```bash
pip install -r requirements.txt
```

**What gets installed:**
- `flask` - Web framework
- `gunicorn` - Production server (optional for development)

#### 5. Initialize the Database

The database is automatically initialized when you first run the application. It creates all necessary tables:
- users
- projects
- reviews
- project_reviewers
- project_history
- notifications

#### 6. Create Upload Directory (if not exists)

```bash
# Windows
mkdir uploads

# macOS/Linux
mkdir uploads
```

### Running the Application

#### Development Mode:

```bash
python app.py
```

**Output:**
```
 * Running on http://127.0.0.1:5000
 * Debug mode: on
```

Visit: `http://localhost:5000`

#### Production Mode (using Gunicorn):

```bash
gunicorn app:app --bind 0.0.0.0:8000
```

### Default Port & Access

- **Development URL**: `http://localhost:5000`
- **Production URL**: `http://localhost:8000` or your domain

### Creating Admin Account

Since there's no admin signup route, the first admin must be created via:

**Option 1: Database Direct Access**
```bash
sqlite3 database.db
INSERT INTO users (name, email, password, role) 
VALUES ('Admin', 'admin@example.com', 'password123', 'Admin');
```

**Option 2: Create via SQL Script**
```bash
# Create a file: init_admin.py
from utils.db import get_db

conn = get_db()
conn.execute(
    "INSERT INTO users (name, email, password, role) VALUES (?, ?, ?, ?)",
    ('Admin', 'admin@example.com', 'password123', 'Admin')
)
conn.commit()
```

Then run: `python init_admin.py`

### Troubleshooting Installation

| Issue | Solution |
|-------|----------|
| `ModuleNotFoundError: No module named 'flask'` | Run `pip install -r requirements.txt` |
| `Address already in use` | Change port: `python -m flask run --port 5001` |
| `database.db not found` | Run the app once - it auto-creates on startup |
| `Permission denied` on uploads folder | Run: `mkdir uploads` with proper permissions |

---

## 🧪 Usage Guide

### Getting Started

#### 1. First Time Setup

**Create an Admin Account:**
```bash
# Run this SQL command in database.db
INSERT INTO users (name, email, password, role) 
VALUES ('Admin User', 'admin@commitforge.com', 'admin123', 'Admin');
```

#### 2. Login to Application

- Navigate to: `http://localhost:5000/login`
- Enter credentials (email & password)
- Redirected based on role (Contributor → Contributor Dashboard, etc.)

---

### Contributor Workflow

#### Submitting a Project

1. **Login** and go to Contributor Dashboard
2. Click **"Submit New Project"**
3. Fill in the form:
   ```
   Project Type: Web Application / Mobile App / Backend / etc.
   Title: My Amazing Project
   Description: A detailed description of what the project does
   Category: Select a category
   GitHub Link: https://github.com/user/repo (required)
   Demo Link: https://demo.example.com (optional)
   ZIP File: Select your project.zip (required)
   ```
4. Click **"Submit"**
   - File uploaded to `/uploads`
   - Project status: "Submitted"
   - History record created

#### Editing Your Project

1. On **Contributor Dashboard**, click project
2. Click **"Edit"** (only available within 5-hour window)
3. Modify any field
4. Click **"Save"**
5. History tracked automatically

#### Monitoring Your Project

1. **Dashboard**: See all your projects with current status
2. **Notifications**: Receive updates when:
   - Project assigned to reviewers
   - Status changes (Approved/Rejected)
   - Get feedback from reviewers
3. **Project History**: Click project → View "History" to see:
   - All changes made (edits)
   - When it was submitted
   - When reviewer provided feedback
   - Status changes

#### Managing Deleted Projects

1. Click **"View Deleted"** on dashboard
2. View soft-deleted projects
3. Click **"Restore"** to recover project
4. Project returns to active state

---

### Reviewer Workflow

#### Reviewing Assigned Projects

1. **Login** as Reviewer
2. **Dashboard** shows all assigned projects
3. Click a project to review
4. **Download file** using download link
5. **Extract & test** the project locally
6. **Return to review page**

#### Providing Feedback

1. On the review page, fill:
   ```
   Feedback: Detailed comments on the project
   - What works well
   - Areas for improvement
   - Bugs or issues found
   - Overall assessment
   ```
2. Select **Decision**:
   - ✅ Approved - Project meets requirements
   - ❌ Rejected - Project needs revisions
   - 🔄 Revision Requested - Fix issues and resubmit
3. Click **"Submit Review"**
   - Review stored
   - Project status updated
   - Contributor notified

#### Tracking Your Reviews

1. Click **"Review History"**
2. See all reviews you've submitted
3. See feedback given and decisions made
4. Filter by project or date (with enhancement)

#### Managing Files

1. Navigate to **"Uploads"** page
2. Browse all uploaded project files
3. Download any file to review locally

---

### Admin Workflow

#### Dashboard Overview

**Admin Dashboard** shows:
- 👥 All users (Contributor, Reviewer, Admin)
- 📋 All projects with details
- 🔍 Contributor name for each project
- 📊 Current status of each project
- 👤 Assigned reviewers

#### Creating Users

1. Click **"Add User"**
2. Fill form:
   ```
   Name: Reviewer Name
   Email: reviewer@example.com
   Password: secure_password
   Role: Reviewer (or Admin, Contributor)
   ```
3. Click **"Create"**
4. Account available immediately

#### Assigning Reviewers to Projects

1. On dashboard, find a project
2. In "Assign Reviewers" section:
   - Select one or multiple reviewers
   - **Can assign multiple reviewers per project**
   - Reviewers work in parallel
3. Click **"Assign"**
4. **Automatic actions**:
   - Project status → "Assigned"
   - Reviewers added to project_reviewers table
   - Notifications sent to reviewers
   - Contributor notified of reviewer names

#### Updating Project Status

1. Find project on dashboard
2. In "Status" dropdown, select:
   - Submitted
   - Assigned
   - In Review
   - Revision Requested
   - Approved
   - Rejected
   - Deleted
3. Click **"Update"**
4. Status changes immediately
5. History recorded

#### Monitoring Project Progress

1. Click project title to see details
2. View assigned reviewers
3. View complete project history:
   - When submitted
   - When assigned (who)
   - Reviewer feedback status
   - All status changes
4. See reviewer comments if already reviewed

#### User Statistics (Future Enhancement)

Could add:
- Total projects by contributor
- Review count by reviewer
- Average review time
- Approval/rejection ratio

---

## 📡 API Documentation

### REST API Endpoints

CommitForge uses **Flask-based routing** (Request-Response model, not traditional REST):

### Authentication Routes (`/auth_routes.py`)

| Endpoint | Method | Purpose | Auth Required |
|----------|--------|---------|---------------|
| `/` | GET | Home page (redirects based on role) | ✓ |
| `/login` | GET, POST | Login page & form handler | ✗ |
| `/register` | GET, POST | Registration page & form handler | ✗ |
| `/logout` | GET | Clear session & logout | ✓ |
| `/delete_account` | POST | Delete account (cascading deletes) | ✓ |
| `/notifications` | GET | View all notifications | ✓ |
| `/notifications/<id>/read` | POST | Mark notification as read | ✓ |
| `/notifications/clear` | POST | Clear all notifications | ✓ |

### Contributor Routes (`/contributor_routes.py`)

| Endpoint | Method | Purpose | Role |
|----------|--------|---------|------|
| `/contributor/dashboard` | GET | View submitted projects | Contributor |
| `/contributor/submit` | GET, POST | Submit new project | Contributor |
| `/contributor/edit/<id>` | POST | Edit project (5-hour window) | Contributor |
| `/contributor/delete/<id>` | POST | Soft delete project | Contributor |
| `/contributor/restore/<id>` | POST | Restore deleted project | Contributor |
| `/contributor/deleted` | GET | View trash/deleted projects | Contributor |
| `/contributor/history/<id>` | GET | View project history | Contributor |
| `/contributor/history/<id>/clear` | POST | Clear project history | Contributor |

### Reviewer Routes (`/reviewer_routes.py`)

| Endpoint | Method | Purpose | Role |
|----------|--------|---------|------|
| `/reviewer/dashboard` | GET | View assigned projects | Reviewer |
| `/review/<id>` | GET, POST | Review project & submit feedback | Reviewer |
| `/reviewer/history` | GET | View submitted reviews | Reviewer |
| `/reviewer/history/clear` | POST | Clear review history | Reviewer |
| `/reviewer/project-history/<id>` | GET | View project change history | Reviewer |
| `/reviewer/project-history/<id>/clear` | POST | Clear project history | Reviewer |
| `/reviewer/uploads` | GET | Browse uploaded files | Reviewer |
| `/reviewer/delete/<id>` | POST | Delete project from review | Reviewer |
| `/uploads/<filename>` | GET | Download project file | ✓ |

### Admin Routes (`/admin_routes.py`)

| Endpoint | Method | Purpose | Role |
|----------|--------|---------|------|
| `/admin/dashboard` | GET | View all projects, users, assignments | Admin |
| `/admin/projects/<id>/assignment` | POST | Assign reviewers to project | Admin |
| `/admin/projects/<id>/status` | POST | Update project status | Admin |
| `/admin/users/add` | GET, POST | Create new user | Admin |

---

### Request-Response Examples

#### Example 1: User Login

**Request:**
```
POST /login HTTP/1.1
Content-Type: application/x-www-form-urlencoded

email=contributor@example.com&password=password123
```

**Response:**
```
HTTP/1.1 302 Found
Location: /contributor/dashboard
Set-Cookie: session=<session_data>
```

#### Example 2: Submit Project

**Request:**
```
POST /contributor/submit HTTP/1.1
Content-Type: multipart/form-data

title=MyApp&description=Great project&category=Web&github_link=https://github.com/user/repo&zip_file=<file_data>
```

**Response:**
```
HTTP/1.1 302 Found
Location: /contributor/dashboard
```

#### Example 3: Assign Reviewers

**Request:**
```
POST /admin/projects/5/assignment HTTP/1.1
Content-Type: application/x-www-form-urlencoded

reviewer_ids=3&reviewer_ids=7
```

**Response:**
```
HTTP/1.1 302 Found
Location: /admin/dashboard
```

(Sends notifications to reviewers & contributor)

#### Example 4: Submit Review

**Request:**
```
POST /review/5 HTTP/1.1
Content-Type: application/x-www-form-urlencoded

feedback=Great work! Some minor improvements needed...&decision=Approved
```

**Response:**
```
HTTP/1.1 302 Found
Location: /reviewer/dashboard
```

(Updates project status, records in history)

#### Example 5: File Download

**Request:**
```
GET /uploads/project_2024.zip HTTP/1.1
```

**Response:**
```
HTTP/1.1 200 OK
Content-Type: application/zip
Content-Disposition: attachment; filename="project_2024.zip"

<binary_file_data>
```

---

## 🛠️ Future Improvements

### High-Priority Features

#### 1. Password Security Enhancement
```python
CURRENT: Passwords stored in plain text ⚠️
IMPROVEMENT: Use werkzeug.security.generate_password_hash()
- Hash passwords on registration
- Verify hashes on login
- Add password strength requirements
- Implement password reset functionality
```

#### 2. Advanced Search & Filtering
- Search projects by title, category, status
- Filter by date range, contributor, reviewer
- Sort by status, submission date, last updated
- Project tags for better categorization

#### 3. Bulk Operations
- Bulk assign reviewers to multiple projects
- Bulk status updates
- Batch user creation from CSV
- Export projects to CSV/PDF

#### 4. Enhanced Notifications
- Email notifications (send via SMTP)
- In-app notification preferences
- Notification categories configurable per user
- Notification digest (daily/weekly summary)

#### 5. Project Versioning
- Allow multiple submissions of same project
- Version history tracking
- Compare versions side-by-side
- Automatic version numbering

#### 6. Analytics & Reporting
- Project submission statistics
- Average review time
- Reviewer performance metrics
- Approval/rejection ratio
- Time-series charts of submissions

#### 7. Comments & Discussion
- Inline comments on project files
- Discussion thread per project
- @mentions for user notifications
- Comment resolution workflow

#### 8. Automated Workflows
- Auto-assignment based on reviewer availability
- Scheduled reminders for pending reviews
- Auto-escalation if review delayed
- Webhook support for external integrations

### Medium-Priority Features

#### 9. Mobile Responsive Design
- Mobile-friendly layout
- Touch-optimized buttons
- Mobile app (React Native/Flutter)

#### 10. Real-time Updates
- WebSocket for live notifications
- Live project status updates
- Real-time reviewer activity tracking

#### 11. Role Customization
- Custom role creation
- Permission templates
- Fine-grained access control (RBAC enhancements)

#### 12. Integration Features
- GitHub integration (auto-fetch repo info)
- GitLab integration
- Slack notifications
- Email notifications
- Jira integration

### Low-Priority Features

#### 13. Accessibility Improvements
- WCAG 2.1 compliance
- Keyboard navigation
- Screen reader support
- High contrast mode

#### 14. Localization
- Multi-language support
- Timezone management (currently IST only)
- Currency/format localization

#### 15. Testing Suite
- Unit tests for route handlers
- Integration tests for workflows
- Frontend E2E tests
- CI/CD pipeline setup

### Scalability Improvements

#### Database Optimization
- Add database indexes on frequently queried columns
- Query optimization & analysis
- Implement connection pooling
- Consider PostgreSQL for production

#### Caching Strategy
- Redis for session storage
- Cache project listings
- Cache user permissions
- Cache notification counts

#### File Storage
- Implement file versioning system
- Archive old files to cloud storage (AWS S3)
- Implement file compression
- Clean up old uploads periodically

#### Architecture Scaling
- Separate database server
- Load balancing with multiple app instances
- Async job processing (Celery)
- Message queue for notifications (RabbitMQ)

---

## 🤝 Contribution Guide

### How to Contribute

CommitForge is open to contributions. Follow these guidelines:

### Getting Started

1. **Fork the repository** (if open source)
2. **Clone your fork**:
   ```bash
   git clone <your-fork-url>
   cd CommitForge
   ```

3. **Create a feature branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```

4. **Set up development environment**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # or venv\Scripts\activate on Windows
   pip install -r requirements.txt
   ```

### Code Contribution Process

1. **Create your feature** with tests
2. **Follow code style**:
   - Use PEP 8 for Python code
   - Use consistent naming conventions
   - Add docstrings for functions
   - Add comments for complex logic

3. **Test your changes**:
   ```bash
   # Run all tests
   python -m pytest
   
   # Test specific module
   python -m pytest tests/test_routes.py
   ```

4. **Commit with clear messages**:
   ```bash
   git commit -m "feat: add password hashing for security"
   git commit -m "fix: resolve issue with file upload validation"
   ```

5. **Push to your fork**:
   ```bash
   git push origin feature/your-feature-name
   ```

6. **Create Pull Request**:
   - Describe what you changed
   - Reference related issues
   - Provide screenshots for UI changes

### Bug Reporting

Found a bug? Create an issue with:
- **Title**: Clear description (e.g., "Login fails with special characters in password")
- **Environment**: OS, Python version, database state
- **Steps to Reproduce**: Exact steps to trigger the bug
- **Expected Behavior**: What should happen
- **Actual Behavior**: What actually happens
- **Screenshots**: If UI-related

### Feature Requests

Have an idea? Share it:
- **Title**: Clear, concise feature name
- **Description**: What problem does it solve?
- **Why**: Why is this important?
- **Example Usage**: How would users use it?

### Code Review Standards

- Code changes should pass linting
- All SQL queries must be parameterized (prevent SQL injection)
- Add tests for new features
- Update documentation for API/UI changes
- No hardcoded secrets or credentials

### Documentation Contribution

Help improve docs:
- Fix spelling/grammar
- Add clarifications
- Add usage examples
- Create video tutorials
- Improve diagrams

---

## 📜 License

This project is licensed under the **MIT License** - see below.

### MIT License Summary

```
MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
```

**In Plain English:**
- ✅ Use this software for any purpose (personal, commercial)
- ✅ Modify the code as needed
- ✅ Distribute modified versions
- ❌ Hold the authors liable for issues
- ❌ Claim original authorship

**Full License**: Include the MIT License file with any distribution

---

## 🚀 Quick Start Checklist

- [ ] Install Python 3.7+
- [ ] Clone repository
- [ ] Create virtual environment
- [ ] Install requirements: `pip install -r requirements.txt`
- [ ] Run app: `python app.py`
- [ ] Visit: `http://localhost:5000`
- [ ] Create admin user via database
- [ ] Login and test workflows

---

## 📞 Support & Contact

- **Issues**: Report issues via GitHub Issues
- **Discussions**: Use GitHub Discussions for questions
- **Documentation**: Refer to sections above
- **Code Examples**: Check route files for implementation details

---

## 🎯 Key Assumptions Made

During analysis, the following assumptions were made:

1. **Password Security**: Currently using plain text; should implement hashing
2. **Database**: SQLite suitable for small-medium deployments; PostgreSQL recommended for production
3. **File Storage**: Local file system; cloud storage (S3) recommended for distributed systems
4. **Timezone**: All timestamps use IST; can be made configurable
5. **Notifications**: Only in-app; email integration not yet implemented
6. **Security**: No HTTPS enforcement, rate limiting, or CSRF protection (needed for production)
7. **User Roles**: Fixed roles (Contributor, Reviewer, Admin); could be made more flexible

---

**Last Updated**: March 24, 2026

**Version**: 1.0.0

---

## 📚 Additional Resources

- [Flask Documentation](https://flask.palletsprojects.com/)
- [SQLite Documentation](https://www.sqlite.org/docs.html)
- [Python Security Best Practices](https://owasp.org/www-project-top-ten/)
- [Web Application Security](https://owasp.org/www-project-web-security-testing-guide/)

---

**CommitForge** - Making Project Review Simple & Organized 🚀
