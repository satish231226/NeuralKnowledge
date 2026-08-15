# Product Requirements Document (PRD)

## NeuralKnowledgeApp

**Document Version:** 1.0  
**Status:** Draft / Baseline  
**Product Type:** AI-Powered Document Knowledge & Question Answering Platform  
**Primary Platform:** Web Application  
**Primary Audience:** Product, Engineering, QA, Security, UI/UX, and Project Stakeholders  
**Source Basis:** Existing NeuralKnowledgeApp project source files and templates provided for analysis

---

## 1. Executive Summary

NeuralKnowledgeApp is a secure, web-based knowledge management application that enables authenticated users to upload PDF documents, retain those documents in the application database, extract readable text from PDFs, view and manage their uploaded documents, and ask AI-powered questions based on the content of a selected document.

The current implementation is built around a Spring Boot backend, Spring Security authentication, Thymeleaf-based server-rendered UI, database-backed user/document persistence, Apache Tika PDF text extraction, and Spring AI `ChatClient` for document question answering.

The product combines three core capabilities:

1. **Secure user and account management**
2. **Personal PDF/document management**
3. **AI-assisted question answering from uploaded documents**

This PRD documents the product as represented by the supplied implementation and establishes a professional product baseline for further development.

---

# 2. Product Vision

### Vision

Provide users with a secure and simple workspace where they can store knowledge documents and interact with those documents through natural-language AI questions.

### Product Promise

> Upload your knowledge. Secure it. Ask questions. Learn from your documents.

### Product Principles

- **Security first:** User-specific functionality must require appropriate authentication and authorization.
- **Document ownership:** Documents are associated with the user who uploads them.
- **Simple interaction:** Core actions should be accessible directly from the dashboard.
- **AI grounded in documents:** Document-specific answers should be based on the selected document content.
- **Clear feedback:** Upload, authentication, password, and AI operations should provide understandable success/error states.
- **Maintainable architecture:** Business logic should remain separated into controller, service, repository, entity, security, and supporting layers.

---

# 3. Problem Statement

Users often have important information distributed across PDF documents but lack a convenient way to interact with that information through natural language.

NeuralKnowledgeApp addresses this by providing a single web workspace where a user can:

- Create and authenticate an account.
- Upload PDF knowledge documents.
- Store and manage those documents.
- View/download/delete uploaded PDFs.
- Select a document and ask a natural-language question.
- Receive an AI-generated answer based on the selected document.
- Manage profile information and account security.

---

# 4. Goals and Objectives

## 4.1 Primary Goals

### G1 — Secure Authentication
Provide authenticated access to user-specific application features.

### G2 — Personal Document Management
Allow users to upload, store, view, download, and delete their PDF documents.

### G3 — Document-Based AI Q&A
Allow users to select an uploaded document and ask questions about its contents.

### G4 — Account Management
Allow users to view profile information, update profile images, change passwords, and recover forgotten passwords.

### G5 — Consistent User Experience
Provide a unified dark/glassmorphism-style interface across the application's primary screens.

---

# 5. Non-Goals / Current Scope Boundaries

The supplied implementation does **not establish** the following as implemented product requirements:

- Multi-user document collaboration
- Sharing documents with other users
- Document version control
- Vector database / embedding-based RAG
- Semantic search across multiple documents
- Chat history persistence
- AI conversation history
- Mobile-native applications
- Administrative analytics dashboard
- Subscription/billing functionality
- Team/workspace management
- Real-time collaboration
- OCR for image-only/scanned PDFs
- Explicit enterprise SSO beyond the configured Google OAuth2 flow

These should be treated as future scope unless separately approved.

---

# 6. Target Users

## 6.1 Authenticated Individual User

The primary user can:

- Register an account.
- Log in using application credentials.
- Log in using Google OAuth2 where configured.
- Upload PDFs.
- Manage personal documents.
- Ask questions from selected PDFs.
- Manage profile and password settings.

## 6.2 Unauthenticated Visitor

A visitor can:

- Access the landing page.
- Navigate to authentication.
- Register/login.
- Initiate password recovery.

Unauthenticated visitors should not access protected user functionality.

---

# 7. User Journey

## 7.1 New User Journey

```text
Landing Page
    ↓
Login / Register
    ↓
Registration
    ↓
Account Created
    ↓
Login
    ↓
Dashboard
```

## 7.2 Returning User Journey

```text
Landing/Login
    ↓
Authentication
    ↓
Dashboard
    ↓
Choose Feature
```

## 7.3 Document Q&A Journey

```text
Dashboard
    ↓
Upload PDF
    ↓
PDF Stored + Text Extracted
    ↓
My Documents / Ask from PDF
    ↓
Select Document
    ↓
Enter Question
    ↓
AI Processing
    ↓
Document-Based Answer
```

---

# 8. Functional Requirements

## FR-001 — Landing Page

### Description
The system shall provide a public landing page introducing NeuralKnowledgeApp.

### Current UI Elements

- Application name
- Product positioning
- Login/Get Started CTA
- Technology stack information
- Footer

### Source Alignment
The current landing page presents the product as a secure knowledge-management platform and highlights Spring Boot, Spring Security, HTML, Bootstrap, and CSS.

---

## FR-002 — User Registration

### Description
The system shall allow a new user to create an account.

### Input Fields

- Email / username
- Full name
- Password
- Phone number
- Optional profile image

### Expected Flow

```text
Registration Form
    ↓
POST /signup
    ↓
UserService
    ↓
User Persistence
    ↓
Registration Result
    ↓
Login Page
```

### Business Rules

- User identity is based on the username/email used by the application.
- Passwords are handled through the application's password encoding mechanism.
- Profile image may be stored with the user profile.

---

## FR-003 — Standard Login

### Description
The system shall authenticate users through Spring Security form login.

### Inputs

- Username/email
- Password

### Success

Redirect authenticated user to the dashboard.

### Failure

Display an authentication error state.

---

## FR-004 — Google OAuth2 Login

### Description
The application shall expose Google OAuth2 login where OAuth2 configuration is available.

### Expected Flow

```text
Login
  ↓
Continue with Google
  ↓
Google OAuth2
  ↓
User Information
  ↓
Application User
  ↓
Dashboard
```

The supplied security configuration assigns the authenticated OAuth2 user the USER role and redirects successful authentication to the dashboard.

---

## FR-005 — Forgot Password

### Description
The system shall provide a password recovery workflow based on a reset token delivered through email.

### Flow

```text
Forgot Password
    ↓
Registered Email
    ↓
/forget
    ↓
Reset Token
    ↓
Email
    ↓
Reset Password Page
```

### Expected Outcomes

- Existing user: reset email is sent.
- Unknown user: an appropriate message is displayed.
- Email delivery failure: an appropriate failure message is displayed.

---

## FR-006 — Password Reset

### Description
The system shall allow a user to set a new password through a valid reset token.

### Inputs

- New password
- Confirm password
- Reset token

### Validation

- Reset token must be valid.
- New password and confirmation must match.

---

## FR-007 — Dashboard

### Description
The dashboard shall act as the primary authenticated navigation hub.

### Core Actions

- Upload PDF
- View documents
- Open profile
- Ask questions from PDF
- Logout

---

## FR-008 — PDF Upload

### Description
Authenticated users shall be able to upload PDF documents.

### Input

- PDF file

### Flow

```text
Upload PDF
    ↓
Multipart File
    ↓
DocumentService
    ↓
File Size Validation
    ↓
Text Extraction
    ↓
Document Persistence
```

### Current Implementation Characteristics

- The UI accepts PDF files.
- The service reads the uploaded file.
- Apache Tika is used for text extraction.
- Original PDF bytes are retained.
- Extracted content is stored with the document.
- The document is associated with the authenticated user.
- The current service implementation includes a file-size check.

### User Feedback

The current controller exposes messages for:

- Successful upload
- Existing document
- File-size exceeded
- Upload failure

---

## FR-009 — My Documents

### Description
Authenticated users shall be able to view their uploaded documents.

### Information

- Document name
- Available document actions

### Actions

- View
- Download
- Delete

The document list is retrieved for the authenticated user's username.

---

## FR-010 — PDF Viewing

### Description
The system shall provide an in-application PDF viewing experience.

### Current UI

The document page uses an iframe pointing to the PDF-view endpoint.

### Expected Experience

```text
My Documents
    ↓
View
    ↓
PDF displayed in browser/application page
```

---

## FR-011 — PDF Download

### Description
Authenticated users shall be able to download stored PDF documents.

### Expected Behavior

The system returns the stored PDF bytes as an attachment using the document name.

---

## FR-012 — Document Deletion

### Description
Authenticated users shall be able to delete a document from their document-management view.

### UX

The current UI asks for confirmation before deletion.

---

## FR-013 — Profile

### Description
Authenticated users shall be able to view their account details.

### Current Information

- Username/email
- Full name
- Phone number
- Role
- Account status
- Profile photo

---

## FR-014 — Profile Image Update

### Description
Authenticated users shall be able to upload/update their profile image.

### Flow

```text
Choose Image
    ↓
POST /uploadProfileImage
    ↓
UserService
    ↓
Profile Updated
```

The profile page displays the profile image through the profile-image endpoint.

---

## FR-015 — Change Password

### Description
Authenticated users shall be able to change their password.

### Inputs

- Current password
- New password
- Confirm new password

### Validation

- New password should differ from the current password.
- New password and confirmation must match.
- Existing password must be validated through the service layer.

---

## FR-016 — Ask Question from PDF

### Description
Authenticated users shall be able to ask an AI question against a selected uploaded PDF.

### Inputs

- Document ID
- Natural-language question

### Flow

```text
Select PDF
    ↓
Enter Question
    ↓
POST /ask
    ↓
Retrieve Document
    ↓
Extracted Document Content
    ↓
AiService
    ↓
Spring AI ChatClient
    ↓
Answer
```

---

## FR-017 — Document-Grounded AI Response

### Description
For document-specific questions, the AI service shall instruct the model to answer strictly from the supplied document content.

### Current Behavior

If the document has no readable content, the service returns:

> The selected document has no readable content.

If the requested information is not present in the document, the prompt instructs the AI to return:

> The document does not contain this information.

### Current Architecture

The supplied implementation is a **RAG-lite/context-in-prompt approach**:

```text
Stored Document Text
        ↓
Prompt Context
        ↓
ChatClient
        ↓
LLM Answer
```

It does not establish a vector database or embedding retrieval layer.

---

## FR-018 — CSRF Protection

The current HTML forms for state-changing operations include Thymeleaf-generated CSRF tokens.

This applies to flows such as:

- Login
- Registration
- Forgot password
- Password update
- PDF upload
- Change password
- Profile image upload
- Logout
- AI question submission

---

## FR-019 — Access Denied

The application shall provide a user-facing page when a user does not have permission to access a protected resource.

---

## FR-020 — Application Error Handling

The application shall provide an application error page displaying the exception type and error message exposed by the centralized exception handling mechanism.

---

# 9. Data Requirements

## 9.1 User Data

The current implementation establishes a user entity containing information including:

- Username/email
- Name
- Password
- Phone
- Role
- Enabled/account status
- Profile photo

The supplied entity defaults a newly created user to the USER role and enabled state.

## 9.2 Document Data

The current document entity contains:

- Document ID
- Extracted text content
- Original PDF bytes
- Document name
- Owning user

## 9.3 Relationship

```text
MyUser 1 ───────── * DocumentChunk
```

A user can own multiple documents.

---

# 10. Security Requirements

## SEC-001 — Authentication

Protected user features must require authentication.

## SEC-002 — Authorization

User-specific features should require the USER role according to the application's security configuration.

## SEC-003 — Password Protection

Application passwords shall not be stored as plaintext. The current implementation uses BCrypt password encoding.

## SEC-004 — CSRF

State-changing web forms shall use CSRF protection.

## SEC-005 — OAuth2

Google OAuth2 authentication shall use the configured Spring Security OAuth2 flow.

## SEC-006 — Token-Based Password Recovery

Password reset uses a JWT-based reset-token mechanism with a configured expiry.

## SEC-007 — Document Ownership

Documents are associated with their owning user. User-specific document retrieval is supported by the repository layer.

### Security Review Note

The supplied implementation contains several ID-based document operations (`view`, `download`, `ask`, and `delete`) where controller/service calls use generic document lookup patterns. These flows should be reviewed before production release to ensure every document operation enforces ownership against the authenticated user.

This is a **requirement/review item**, not an implementation change in this PRD.

---

# 11. Non-Functional Requirements

## NFR-001 — Security

The application should protect authenticated resources and user data from unauthorized access.

## NFR-002 — Usability

Primary actions should be discoverable from the dashboard with minimal navigation.

## NFR-003 — Responsiveness

The current UI uses Bootstrap and responsive grid structures.

## NFR-004 — Maintainability

Business logic should remain separated into:

- Controllers
- Services
- Repositories
- Entities
- Security
- Exception handling

## NFR-005 — Reliability

The system should provide meaningful application-level feedback for failed operations.

## NFR-006 — Performance

Document processing and AI operations should be designed so that large documents do not unnecessarily degrade the user experience.

The supplied implementation currently places the extracted document content directly into the AI prompt, so prompt size and model context limits should be considered for production-scale documents.

## NFR-007 — Data Integrity

A successfully uploaded document should retain both its source PDF representation and extracted textual representation as currently designed.

---

# 12. UI/UX Requirements

## Design Language

The current application uses:

- Dark theme
- Glassmorphism cards
- Radial/dark gradients
- Bootstrap components
- Poppins typography
- Rounded cards/buttons
- Responsive layouts

## UX Consistency

Primary pages should maintain consistent:

- Navigation
- Branding
- Typography
- Color treatment
- Button styles
- Footer treatment
- Success/error messaging

## Core Screen Inventory

| Screen | Purpose |
|---|---|
| Landing | Product introduction |
| Login/Register | Authentication and account creation |
| Dashboard | Primary navigation |
| Upload PDF | Document ingestion |
| My Documents | Document management |
| PDF Viewer | Document viewing |
| Ask Question | AI document Q&A |
| My Profile | Account information |
| Change Password | Security settings |
| Reset Password | Password recovery |
| Access Denied | Authorization error |
| Application Error | Runtime exception display |

---

# 13. API / Route Inventory

The following route inventory is derived from the supplied controller and security configuration.

| Method | Route | Purpose |
|---|---|---|
| GET | `/` | Landing page |
| GET/POST | `/login` | Authentication |
| POST | `/signup` | Registration |
| GET | `/dashboard` | Dashboard |
| GET | `/upload_pdf` | Upload page |
| POST | `/upload` | Upload PDF |
| GET | `/my_document` | User documents |
| GET | `/getPdf` | Document page/view selection |
| GET | `/viewPdf` | PDF content |
| GET | `/download` | PDF download |
| GET | `/delete` | Delete document |
| GET | `/my_profile` | User profile |
| GET | `/getProfileImage` | Profile image |
| POST | `/uploadProfileImage` | Update profile image |
| GET | `/changePassword` | Change password page |
| POST | `/change_password` | Change password |
| GET | `/ask` | AI question page |
| POST | `/ask` | Ask AI question |
| POST | `/forget` | Initiate password reset |
| GET | `/reset_password` | Reset password page |
| POST | `/update_password` | Update reset password |

---

# 14. Backend Component Responsibilities

## NeuralController

Responsible for web request handling and coordinating:

- Authentication-related pages
- Dashboard
- Documents
- Profile
- Password operations
- AI Q&A
- Uploads

## UserService

Responsible for user-related business operations including:

- User lookup
- User creation
- Password handling
- Profile image update
- Password changes
- Password reset update

## DocumentService

Responsible for:

- PDF processing
- Text extraction
- Document persistence
- User-specific document retrieval
- Document deletion/retrieval

## AiService

Responsible for:

- General AI interaction
- Document-context AI interaction

It uses Spring AI `ChatClient`.

## MailSending

Responsible for password-reset email delivery.

## JwtUtil

Responsible for reset-token generation/validation.

## SecurityConfig

Responsible for:

- Authorization
- Form login
- OAuth2 login
- User authentication
- Password encoding
- Security filter configuration
- Role assignment

## Repositories

Repositories provide persistence access for:

- Users
- Documents

---

# 15. Business Rules

### BR-001
Only authenticated users should access user-specific application features.

### BR-002
Each uploaded document is associated with the authenticated user.

### BR-003
A user should see their own uploaded document collection.

### BR-004
AI document questions should use the selected document as the source context.

### BR-005
If the selected document has no readable content, the system should return a clear message.

### BR-006
If requested information is absent from the supplied document, the AI prompt instructs the system to communicate that the document does not contain the information.

### BR-007
Password reset must use a valid reset token.

### BR-008
Password confirmation must match the new password during password reset.

### BR-009
Password change requires validation of the current password.

---

# 16. Error & Feedback Requirements

The application currently exposes user feedback for common workflows.

## Authentication

- Invalid username/password
- Logout confirmation
- Registration success/failure

## Upload

- Upload success
- Duplicate/existing document
- File-size exceeded
- Upload failure

## Password

- Reset token invalid/expired
- Password mismatch
- Password update success/failure
- Password change errors

## Profile

- Profile update success
- User not found

## Application

- Access denied
- Generic application exception

---

# 17. Acceptance Criteria

## Authentication

- [ ] User can open the public landing page.
- [ ] User can register with supported account information.
- [ ] Registered user can log in.
- [ ] Invalid credentials result in an error state.
- [ ] Authenticated user reaches the dashboard.
- [ ] Logout ends the authenticated session.
- [ ] Google OAuth2 login works when configured.

## Documents

- [ ] Authenticated user can open the upload page.
- [ ] User can submit a PDF.
- [ ] Uploaded PDF is associated with the current user.
- [ ] Extracted text is persisted.
- [ ] User can see their documents.
- [ ] User can view a PDF.
- [ ] User can download a PDF.
- [ ] User can delete a document.
- [ ] Unauthorized users cannot access protected document-management functionality.

## AI Q&A

- [ ] User can open Ask from PDF.
- [ ] User can select an uploaded document.
- [ ] User can enter a question.
- [ ] System sends document content to the AI service.
- [ ] AI response is displayed.
- [ ] Empty/unreadable document content is handled.
- [ ] Questions outside document knowledge receive the defined fallback behavior.

## Profile

- [ ] User can view profile details.
- [ ] User can upload/update profile image.
- [ ] User can open password-change functionality.
- [ ] User can change their password after validation.

## Password Recovery

- [ ] User can request password recovery.
- [ ] Reset email can be generated/sent when configured.
- [ ] Valid reset token opens reset flow.
- [ ] Invalid/expired token is rejected.
- [ ] Matching passwords can update the account password.

---

# 18. QA Test Matrix

| Area | Test | Expected Result |
|---|---|---|
| Registration | Valid registration | Account created |
| Registration | Duplicate user | Duplicate feedback |
| Login | Valid credentials | Dashboard |
| Login | Invalid credentials | Login error |
| OAuth2 | Valid Google authentication | Dashboard |
| Upload | Valid PDF | Document saved |
| Upload | Oversized file | Size error |
| Documents | User opens documents | Own documents shown |
| View | Valid document | PDF rendered |
| Download | Valid document | PDF downloaded |
| Delete | Confirm deletion | Document removed |
| Ask | Valid document/question | AI answer shown |
| Ask | Empty content | Readable-content message |
| Profile | Open profile | User details shown |
| Profile | Upload image | Profile image updated |
| Password | Correct current password | Password changed |
| Password | Wrong current password | Change rejected |
| Reset | Valid token | Password reset |
| Reset | Invalid token | Reset rejected |
| Authorization | Anonymous protected-route access | Access blocked |
| Error | Application exception | Error page shown |

---

# 19. Current Technical Architecture

```text
                    ┌──────────────────────┐
                    │      Browser/UI      │
                    │ Thymeleaf + Bootstrap│
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │  NeuralController    │
                    └───────┬───────┬──────┘
                            │       │
              ┌─────────────┘       └─────────────┐
              ▼                                   ▼
      ┌─────────────────┐                 ┌───────────────┐
      │   UserService   │                 │ DocumentService│
      └────────┬────────┘                 └───────┬───────┘
               │                                  │
               ▼                                  ▼
        ┌────────────┐                     ┌──────────────┐
        │  UserRepo  │                     │ DocumentRepo │
        └─────┬──────┘                     └──────┬───────┘
              │                                   │
              └──────────────┬────────────────────┘
                             ▼
                       ┌────────────┐
                       │  Database  │
                       └────────────┘

                 Document Question Flow
                             │
                             ▼
                      ┌────────────┐
                      │  AiService │
                      └──────┬─────┘
                             ▼
                    ┌────────────────┐
                    │ Spring AI      │
                    │ ChatClient     │
                    └───────┬────────┘
                            ▼
                          LLM
```

---

# 20. Security Architecture

```text
                  Incoming Request
                         │
                         ▼
                Spring Security
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
       Form Login     Google OAuth2   CSRF
          │              │
          └───────┬──────┘
                  ▼
          Authenticated User
                  │
                  ▼
              ROLE_USER
                  │
                  ▼
       Protected Application
```

Password storage uses BCrypt, while password recovery uses JWT-based reset tokens.

---

# 21. Document Processing Architecture

```text
             Multipart PDF
                   │
                   ▼
            DocumentService
                   │
             File validation
                   │
                   ▼
             Apache Tika
                   │
             Text extraction
                   │
          ┌────────┴─────────┐
          ▼                  ▼
    Original PDF         Text Content
      byte[]                 │
          │                  │
          └────────┬─────────┘
                   ▼
             DocumentChunk
                   │
                   ▼
                Database
```

---

# 22. AI Architecture

```text
User Question
      │
      ▼
Selected Document ID
      │
      ▼
Document Lookup
      │
      ▼
Extracted Text
      │
      ▼
Prompt Construction
      │
      ▼
Spring AI ChatClient
      │
      ▼
LLM
      │
      ▼
Document-grounded Answer
```

The current implementation passes the complete extracted document content into the prompt rather than retrieving relevant chunks through an embedding/vector-search pipeline.

---

# 23. Production Readiness Review Items

The supplied implementation should be reviewed in the following areas before being considered production-ready:

### Security

1. Verify ownership checks for every document operation.
2. Review public access to PDF viewing.
3. Review generic ID-based document lookup.
4. Move sensitive JWT secrets to secure configuration/secrets management.
5. Review OAuth2 production configuration.
6. Validate uploaded file type and content robustly.
7. Review upload size limits at both application and infrastructure layers.

### AI

1. Define behavior for very large PDFs.
2. Introduce chunking/retrieval if document sizes exceed model context constraints.
3. Consider vector embeddings for scalable document retrieval.
4. Define AI failure/timeout behavior.
5. Consider response grounding/citation requirements.

### Data

1. Review storage strategy for binary PDFs in the database.
2. Define retention/deletion policies.
3. Consider document metadata requirements.
4. Define backup and recovery requirements.

### UX

1. Standardize branding/footer identity across templates.
2. Replace legacy route/link remnants.
3. Standardize endpoint naming.
4. Add loading states for PDF upload and AI requests.
5. Improve empty/error states.

### Engineering

1. Add automated unit tests.
2. Add controller/integration tests.
3. Add security tests.
4. Add document ownership tests.
5. Add AI service tests using mocked model responses.
6. Add production logging and observability.

---

# 24. Future Enhancement Roadmap

## Phase 1 — Stabilization

- Security/ownership review
- Route consistency
- Error handling improvements
- UI consistency
- Automated testing

## Phase 2 — AI Enhancement

- Document chunking
- Embeddings
- Vector database
- Semantic retrieval
- Better document-grounded answers
- Source/page references

## Phase 3 — Knowledge Workspace

- Search
- Document categories/tags
- Document metadata
- Chat history
- Conversation persistence
- Multiple-document Q&A

## Phase 4 — Collaboration

- Document sharing
- Team workspaces
- Permission levels
- Collaborative knowledge bases

## Phase 5 — Production Platform

- Observability
- Rate limiting
- Secure secrets management
- Scalable file storage
- Background document processing
- AI usage controls
- Administrative controls

---

# 25. Success Metrics

The current supplied implementation does not define production KPIs. For future product measurement, the following metrics are recommended:

### Adoption

- Registration completion rate
- Login success rate
- Active users
- Returning users

### Document Engagement

- PDFs uploaded per active user
- Documents viewed
- Documents downloaded
- Documents deleted
- Upload success rate

### AI Engagement

- Questions asked per active user
- AI answer success rate
- Average AI response time
- AI failure/timeout rate
- Percentage of questions answered from document context

### Reliability

- Application error rate
- Authentication failure rate
- Upload failure rate
- AI service failure rate

### Security

- Unauthorized access attempts
- Failed authentication attempts
- Password reset requests
- Invalid/expired reset-token attempts

---

# 26. Dependencies

The supplied implementation indicates dependencies/concepts including:

- Spring Boot
- Spring Security
- Spring Security OAuth2 Client
- Spring AI
- Apache Tika
- Thymeleaf
- Bootstrap
- Poppins / Google Fonts
- BCrypt
- JWT utility
- Database persistence
- Email service

Exact production versions/configuration should be maintained in the project's build/configuration files and are not redefined by this PRD.

---

# 27. Assumptions

This PRD is based on the supplied project files.

Where the source files do not explicitly establish a requirement, this document does not treat that capability as currently implemented.

In particular:

- The project is treated as a single-user-workspace model per authenticated account.
- Documents are treated as user-owned.
- AI Q&A is treated as document-context-based.
- Google OAuth2 is treated as configuration-dependent.
- Email password recovery is treated as configuration-dependent.
- Production infrastructure, deployment topology, exact database vendor, monitoring stack, and cloud provider are not defined by the supplied source set.

---

# 28. Known Source-Level Inconsistencies

The following inconsistencies were observed across the supplied files and should be treated as engineering cleanup items rather than silently changing product requirements:

1. Security configuration references `/ask_question`, while the active controller/UI flow uses `/ask`.
2. `/viewPdf` has an inconsistent authorization configuration in the supplied security file.
3. Several document operations use generic document ID retrieval and should be reviewed for ownership enforcement.
4. `accessDenied.html` contains legacy links such as `/userHome`, `/adminHome`, and `/signUp`.
5. `reset_password.html` contains legacy/static navigation references.
6. Footer developer identity is inconsistent across supplied templates.
7. Some template names/routes differ from older naming conventions.
8. The landing-page wording refers to JWT Authentication, while the main browser authentication flow is implemented through Spring Security form login and Google OAuth2; JWT is also used for password-reset token handling.

These observations are documented for traceability and are not automatically interpreted as defects without confirming the intended product behavior.

---

# 29. Definition of Done

A feature is considered complete when:

- Functional requirements are implemented.
- Authentication/authorization behavior is verified.
- User ownership rules are verified.
- Validation is implemented.
- Success and failure states are handled.
- UI is responsive and consistent.
- Automated tests cover important business logic.
- Security-sensitive paths have been reviewed.
- Error logging is adequate.
- No known critical security defect remains.
- Acceptance criteria pass in the target environment.

---

# 30. Product Summary

NeuralKnowledgeApp is positioned as a secure personal document knowledge platform with AI-powered document question answering.

Its current core value chain is:

```text
SECURE ACCOUNT
      ↓
PERSONAL DOCUMENTS
      ↓
PDF STORAGE + TEXT EXTRACTION
      ↓
DOCUMENT SELECTION
      ↓
AI QUESTION
      ↓
DOCUMENT-GROUNDED ANSWER
```

The current implementation already establishes the fundamental product workflow across authentication, document management, profile/security management, PDF processing, and AI-assisted Q&A.

The highest-priority engineering consideration before broader production adoption is to validate **document-level authorization/ownership enforcement**, followed by scalability of the current full-document prompt approach for larger PDFs.

---

## Appendix A — Current Screen Map

```text
/
└── index.html
      │
      └── /login
            │
            ├── Login
            ├── Register → /signup
            ├── Google OAuth2
            └── Forgot Password → /forget
                         │
                         └── Email Link
                                  │
                                  └── /reset_password
                                           │
                                           └── /update_password

Authenticated
      │
      └── /dashboard
             │
             ├── /upload_pdf
             │       └── POST /upload
             │
             ├── /my_document
             │       ├── /getPdf
             │       ├── /viewPdf
             │       ├── /download
             │       └── /delete
             │
             ├── /my_profile
             │       ├── /getProfileImage
             │       ├── /uploadProfileImage
             │       └── /changePassword
             │               └── POST /change_password
             │
             └── /ask
                     └── POST /ask
```

---

## Appendix B — Source-of-Truth Note

This PRD was created from the supplied NeuralKnowledgeApp project files and templates that were available in the conversation.

It intentionally distinguishes:

- **Implemented/current behavior** — derived from the supplied source.
- **Review items** — observations requiring engineering validation.
- **Future roadmap** — proposed expansion beyond the current implementation.

No external product research or web sources were used to define the current product behavior.
