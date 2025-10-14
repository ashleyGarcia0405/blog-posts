This semester I helped lead the CORE Fellowship initiative where we match students at Columbia to startups. This involves outreach, making and keeping track of a ton of forms, evaluations, and final matching. And it was a headache - manual, time-consuming, and unsustainable.
Our current system, which relies on a patchwork of tools (Wufoo, Google Sheets, email), is creating administrative overload and diverting critical time away from securing high-quality startup partnerships.

I conducted about 48 interviews and had to keep track of their evaluations and preferences relative to each other. If I interviewed you and you're reading this and you got an internship you're unhappy with, uhh I'm sorry...

I propose developing a dedicated, automated CORE Fellowship platform to streamline operations, enhance efficiency, and elevate the quality of our initiative.

### Key Pain Points of the Current System

| Curr Manual Task               | Impact on CORE Leadership                                                                                                                                           |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Form Creation and Data Entry   | Tedious, prone to errors, and requires constant manual transfer between Wufoo and Google Sheets.                                                                    |
| Interview Scoring and Tracking | Scores and preferences are managed manually in spreadsheets, making the final matching process subjective and incredibly time-intensive.                            |
| Communication and Outreach     | Student and startup communication is ad-hoc via email, leading to lost threads and lack of centralized history.                                                     |
| Focus Diversion                | Administrative tasks consume an excessive amount of leadership time, preventing the team from focusing on critical startup recruitment and relationship management. |

### Vision for the New Automated Platform

The new platform will centralize all components of the fellowship life cycle, drastically reducing the administrative burden and freeing up leadership time to focus on strategic growth:

1. **Centralized Data Management:** Replace disparate forms with a unified platform for applications, preferences, and technical evaluations.
    
2. **Automated Scoring & Tracking:** Immediately capture interview scores and preferences, providing a real-time, consolidated dashboard of all candidates.
    
3. **Intelligent Matching Algorithm:** Implement a **semi-automated matching feature** that uses student scores, technical profiles, and stated preferences to generate optimal student-startup pairings. This tool would transform weeks of manual work into a single afternoon of review and refinement.
    
4. **Integrated Communications:** Built-in email or notification features to handle routine outreach, scheduling, and acceptance/rejection notifications.
### Development and Sprint Planning
I sent a message in the CORE Announcements channel. I have about 4 people who have agreed to work on this so far (including me).  I will keep track of this project in the CORE Notion with a Kanban board, but this space is more for me to personally keep track of the core features.

#### Sprint 1: Foundation & Setup
**Goal:** Establish project infrastructure and core data models
**Tasks:**
- Set up development environment and version control
- Define tech stack (Frontend: React/Next.js, Backend: Node.js/Python, Database: PostgreSQL/MongoDB)
- Create database schema for users, applications, startups, and interviews
- Set up authentication system (student/admin roles)
- Basic landing page and navigation structure
- Define API endpoints documentation
**Deliverable:** Working development environment with basic auth and database schemas

#### Sprint 2: Application Management
**Goal:** Replace Wufoo forms with integrated application system
**Tasks:**
- Build student application form with validation
- Create startup profile submission form
- Implement form data storage and retrieval
- Admin dashboard to view all applications
- Export functionality to CSV/Excel for backup
- Application status tracking (submitted, under review, accepted, rejected)
**Deliverable:** Fully functional application submission and viewing system

#### Sprint 3: Interview & Evaluation System
**Goal:** Automate interview scoring and preference tracking
**Tasks:**
- Create interview scheduling interface
- Build evaluation form for interviewers (technical skills, communication, preferences)
- Real-time score calculation and aggregation
- Candidate comparison dashboard
- Notes and comments system for each candidate
- Filter and search functionality for candidates
**Deliverable:** Complete interview management and scoring system

#### Sprint 4: Matching Algorithm 
**Goal:** Implement semi-automated matching between students and startups
**Tasks:**
- Design matching algorithm (weighted scoring: technical fit, preferences, startup needs)
- Build matching interface with drag-and-drop capability
- Generate initial automated matching suggestions
- Manual override and adjustment tools
- Conflict resolution (multiple students for same position)
- Matching history and version control
**Deliverable:** Working matching algorithm with manual refinement capabilities

#### Sprint 5: Communications & Notifications
**Goal:** Centralize all communications
**Tasks:**
- Email integration (SendGrid/AWS SES)
- Template system for common emails (acceptance, rejection, interview invites)
- Automated notification triggers (application received, interview scheduled, match made)
- Communication history log per student/startup
- Bulk email functionality for announcements
- Calendar integration for interview scheduling
**Deliverable:** Integrated communication system with email templates and automation

#### Sprint 6: Testing and Launch
**Goal:** Ensure platform stability and prepare for production
**Tasks:**
- End-to-end testing with real data from previous cycles
- User acceptance testing with CORE leadership
- Performance optimization and bug fixes
- User documentation and training materials
- Data migration plan from current Google Sheets
- Production deployment and monitoring setup
**Deliverable:** Production-ready platform with documentation

#### Post-Launch Priorities
- Analytics dashboard (application metrics, conversion rates, time saved)
- Automated reminder system for incomplete applications
- Feedback collection system from students and startups
