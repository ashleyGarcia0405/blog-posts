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
---
### My Progress

Day 1
I started off with setting up some scaffolding and making some decisions about where I would host everything. I am working within a monorep

Day 2
Today I am implementing JWT authentication. The system will support 3 user types with role-based access control: students, startups, and admin. 
Architecture: Centralized JWT Issuance with Distributed Validation
- identity-service: Issues JWTs, manages user registration/login
- api-gateway: Validates JWTs, extracts userId, forwards as X-User-Id header to downstream services
- applications-service: Trusts gateway-provided userId header for authorization

User Lifecycle
1. Registration (anonymous -> user account)
	User visits → Register page → Submits registration form -> redirect to Login
	Here the identity service:
	-  validates email uniqueness
	- hashes password with bcrypt
	- creates user doc with:
		- userType (STUDENT vs STARTUP), role, accountEnables = true, emailVerified = false (this will be for future email verification)
	- returns 201 CREATED
2. Login (Authentication Session)
	Identity service:
	- finds user by email
	- verifies password with bcrypt
	- checks accountEnabled && !accountLocked (this will be rare)
	- generates JWT with claims:
		- sub: userId (this will be MongoDB ObjectIf), email, userType, role, exp: 24 hours from current time
	- updates user.LastLoginAt
	- Returns JWT token
	Frontend stores JWT in local storage -> User is authenticated 
3. Application Creation
	Students
		A student will go to the application form and fill it out which then triggers a 
		 -  POST /v1/students/applications (with JWT in Authorization header)
		The api-gateway validates JWT, extracts userId from JWT subject claim, adds X-User-ID header to the request and forwards it to the application-service.
		The application-service will extract userId from the X-User_id header and do some checks on whether the user already has an application for this term. The service will attempt to findByUserIdAndTerm(userId, currentTerm). If it exists, the service will return a 409 Conflict "You have already submitted an application for this term"
		- Service creates StudentApplication with userId from header, email from request body, status = "submitted", term (set by admin or null initially). This is saved to the database and a 201 Created with application document is returned.
	Startups
		Same goes for startups. POST /v1/startups/intake (with JWT) + same validation and linking process.

 Application Lifecycle
 1. SUBMITTED
	 - the user can view their own application, the admin can see the review queue
	 - the user CANNOT edit after submission
	 - the user can only have ONE application per term
 2. UNDER_REVIEW (Admin Action)
	- admin reviews application -> PATCH /v1/students/applications/{id}
	- the application is then updated with status = "under review", reviewedBy: adminUserId, updatedAt = Instant.now()
 3. ACCEPTED/REJECTED
	 Admin decision → PATCH /v1/students/applications/{id}
	 Request body: {
	   status: "accepted" | "rejected",
	   reviewedBy: "adminUserId",
	   reviewNotes: "Reason for decision"
	 }
	 Application updated with status: "accepted" or "rejected", reviewedBy: adminUserId, reviewNotes: stored, updatedAt: Instant.now()

Stuff I did:
- JWT authentication filter in api-gateway
- JWT validation and user header forwarding (X-User-Id, X-User-Role, X-User-Email)
- Proxy endpoints for authentication (/v1/auth/register, /v1/auth/login)
- RBAC config
- added userId field to StudentApplication and Startup models
- added userID query methods to repos
- StudentApplicationController with complete authentication logic
- Added @PreAuthorize("hasRole('ADMIN')") to ExportController

Things I have to do now:
-   update StartupController (similar to StudentApplicationController)
-  update SecurityConfig to enable method security
-  build and test applications-service
-  end-to-end integration testing
