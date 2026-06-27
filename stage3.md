# stage 3 Documentation

## mockups:

[URL](https://www.figma.com/design/X91sR5oVaaXp9ocPKGFFxn/SelfLab-Ui?node-id=0-1&t=2ESLjfp6OA7d938L-1)

## User Management:

As a user, I want to register an account on the platform so that I can access and benefit from its features.

As a user, I want to log in to the platform so that I can access my registered account.

As an admin, I want to access all curriculums and user-owned resources so that I can handle administrative issues when necessary.

## Curriculum designer:

As a Curriculum Designer, I want to create a new curriculum so that I can provide a structured learning path for trainees.

As a Curriculum Designer, I want to create sprints within a curriculum so that I can simulate a real-world work environment.

As a Curriculum Designer, I want to add projects to a sprint so that I can organize the learning journey into manageable milestones.

As a Curriculum Designer, I want to create new tasks within projects so that I can build educational content.

As a Curriculum Designer, I want to define edge cases for a task so that I can ensure the solution is correct and appropriate.

As a Curriculum Designer, I want to edit my draft curriculum so that I can improve its title and description before publishing it.

As a Curriculum Designer, I want to update or delete sprints, projects, tasks, and test cases while my curriculum is still a draft so that I can correct and organize its content.

As a Curriculum Designer, I want to publish a completed curriculum so that other users can enroll in it and start learning.

## Curriculum:

As a trainee, I want to enroll in a published curriculum so that I can start its learning path.

As a trainee, I want to view the entire curriculum so that I can understand the upcoming lessons and plan my learning journey.

As a trainee, I want to track my progress in a curriculum so that I can monitor my learning progress.

## Correction system:

As a trainee, I want to submit my solution for a task so that I can progress through the curriculum.

As a trainee, I want to know exactly where my solution failed so that I can understand my mistakes and improve my skills.

## Curriculums Browser:

As a trainee, I want to search for a curriculum by its name so that I can find specific curricula quickly.

As a trainee, I want to view a curriculum summary card so that I can compare different curricula quickly.

As a trainee, I want to view a curriculum's details before adding it so that I can determine whether it is suitable for me.

As a Curriculum Designer, I want a page that displays my curricula so that I can manage and track them easily.

## System Architecture: High-level diagram:
System arch.png

## ERD:

erDiagram
USER ||--o{ CURRICULUM : creates
USER ||--o{ ENROLLMENT : enrolls
CURRICULUM ||--o{ ENROLLMENT : has

CURRICULUM ||--o{ SPRINT : contains
SPRINT ||--o{ PROJECT : contains
PROJECT ||--o{ TASK : contains

TASK ||--o{ TEST_CASE : has

ENROLLMENT ||--o{ TASK_PROGRESS : tracks
TASK ||--o{ TASK_PROGRESS : has

USER {
    UUID id PK
    string first_name
    string last_name
    string email
    string password_hash
    string role
    datetime created_at
    datetime updated_at
}

CURRICULUM {
    UUID id PK
    int creator_id FK
    string title
    text description
    string status
    datetime created_at
    datetime updated_at
}

ENROLLMENT {
    UUID id PK
    int user_id FK
    int curriculum_id FK
    datetime started_at
    string status
    datetime created_at
    datetime updated_at
}

SPRINT {
    UUID id PK
    int curriculum_id FK
    int order_index
    int duration_days
    datetime created_at
    datetime updated_at
}

PROJECT {
    UUID id PK
    int sprint_id FK
    string title
    text description
    int order_index
    datetime created_at
    datetime updated_at
}

TASK {
    UUID id PK
    int project_id FK
    string title
    text description
    int order_index
    int points
    boolean strict_compile_enabled
    boolean memory_check_enabled
    datetime created_at
    datetime updated_at
}

TEST_CASE {
    UUID id PK
    int task_id FK
    text input_data
    text expected_output
    boolean is_hidden
    int weight
    int order_index
    datetime created_at
    datetime updated_at
}

TASK_PROGRESS {
    UUID id PK
    int enrollment_id FK
    int task_id FK
    float best_score
    string status
    datetime completed_at
    datetime last_checked_at
    datetime created_at
    datetime updated_at
}

Classes:
classDiagram
direction TB
class User {
    +UUID id
    +string firstName
    +string lastName
    +string email
    +string passwordHash
    +string role
    +datetime createdAt
    +datetime updatedAt
    +createCurriculum()
    +enrollInCurriculum()
    +updateProfile()
}

class Curriculum {
    +UUID id
    +UUID creatorId
    +string title
    +text description
    +string status
    +datetime createdAt
    +datetime updatedAt
    +publish()
    +lockContent()
    +canBeModified()
}

class Enrollment {
    +UUID id
    +UUID userId
    +UUID curriculumId
    +datetime startedAt
    +string status
    +datetime createdAt
    +datetime updatedAt
    +getCurrentSprint()
    +canAccessSprint()
    +getProgress()
}

class Sprint {
    +UUID id
    +UUID curriculumId
    +int orderIndex
    +int durationDays
    +datetime createdAt
    +datetime updatedAt
    +calculateStartDate()
    +calculateEndDate()
    +getStatus()
}

class Project {
    +UUID id
    +UUID sprintId
    +string title
    +text description
    +int orderIndex
    +datetime createdAt
    +datetime updatedAt
    +addTask()
    +updateDetails()
}

class Task {
    +UUID id
    +UUID projectId
    +string title
    +text description
    +int orderIndex
    +int points
    +int timeoutSeconds
    +int memoryLimitMb
    +boolean strictCompileEnabled
    +boolean memoryCheckEnabled
    +string outputMatchMode
    +datetime createdAt
    +datetime updatedAt
    +addTestCase()
    +updateSettings()
    +isCompleted(score)
}

class TestCase {
    +UUID id
    +UUID taskId
    +text inputData
    +text expectedOutput
    +boolean isHidden
    +int weight
    +int orderIndex
    +datetime createdAt
    +datetime updatedAt
    +updateTestCase()
}

class TaskProgress {
    +UUID id
    +UUID enrollmentId
    +UUID taskId
    +float bestScore
    +string status
    +datetime completedAt
    +datetime lastCheckedAt
    +datetime createdAt
    +datetime updatedAt
    +updateBestScore(score)
    +markCompleted()
    +updateLastChecked()
}

User "1" --> "0..*" Curriculum : creates
User "1" --> "0..*" Enrollment : has

Curriculum "1" --> "0..*" Enrollment : contains
Curriculum "1" *-- "0..*" Sprint : contains
Sprint "1" *-- "0..*" Project : contains
Project "1" *-- "0..*" Task : contains
Task "1" *-- "0..*" TestCase : contains

Enrollment "1" --> "0..*" TaskProgress : tracks
Task "1" --> "0..*" TaskProgress : has

## Sequence Diagrams:

#### Use Case 1 — User Login

A user authenticates with their credentials. On success the backend issues a JWT.

sequenceDiagram
actor U as User
participant FE as Frontend
participant BE as Backend
participant DB as Database
U->>FE: Enter email & password
FE->>BE: POST /auth/login (email, password)
BE->>DB: Find user by email
DB-->>BE: User record (password_hash, role)
BE->>BE: Verify password
alt Valid credentials
    BE->>BE: Generate JWT
    BE-->>FE: 200 OK { user, token }
    FE->>FE: Store token
    FE-->>U: Redirect to dashboard
else Invalid credentials
    BE-->>FE: 401 Unauthorized
    FE-->>U: Show error message
end

#### Use Case 2 — Create / Edit a Draft Curriculum & Publish

A designer saves the curriculum as a draft, reopens it from "My Curriculums" to
edit, and publishes it when ready. Content is structured as Sprints → Projects →
Tasks.

sequenceDiagram
actor U as Curriculum Designer
participant FE as Frontend
participant BE as Backend
participant DB as Database
Note over U,DB: Create draft
U->>FE: Fill form (title, description,<br/>sprints -> projects -> tasks)
U->>FE: Click "Save as draft"
FE->>BE: POST /curriculums { title, description, status: draft }
BE->>BE: Validate role & input
BE->>DB: Insert curriculum (status: draft)
DB-->>BE: Created (id)
BE-->>FE: 201 Created
FE-->>U: Draft saved

Note over U,DB: Edit existing draft
U->>FE: Open "My Curriculums"
FE->>BE: GET /me/curriculums
BE->>DB: Fetch curriculums where creator = me
DB-->>BE: List (with status)
BE-->>FE: 200 OK (list)
FE-->>U: Show my curriculums
U->>FE: Select a draft to edit
FE->>BE: GET /curriculums/:curriculumId
BE->>DB: Fetch curriculum
DB-->>BE: Curriculum data
BE-->>FE: 200 OK (curriculum)
FE-->>U: Load draft into form
U->>FE: Modify & click "Save changes"
FE->>BE: PATCH /curriculums/:curriculumId { title, description }
BE->>BE: Validate ownership & input
BE->>DB: Update curriculum
DB-->>BE: Updated
BE-->>FE: 200 OK
FE-->>U: Changes saved

Note over U,DB: Publish
U->>FE: Click "Publish"
FE->>BE: PATCH /curriculums/:curriculumId { status: published }
BE->>BE: Validate ownership
BE->>DB: Update status: published
DB-->>BE: Updated
BE-->>FE: 200 OK
FE-->>U: Curriculum is now published

#### Use Case 3 — Check Code (Correction)

A trainee submits C code for a task. The backend runs it in a Docker container
against all test cases, returns the current result, and updates only the best
score. The submitted code and detailed results are not stored.

sequenceDiagram
actor U as Trainee
participant FE as Frontend
participant BE as Backend
participant D as Docker Container
participant DB as Database
U->>FE: Write C code & click Check
FE->>BE: POST /tasks/:taskId/check { code }
BE->>BE: Verify enrollment & sprint (active/completed)
alt Not enrolled or future sprint
    BE-->>FE: 403 Forbidden
    FE-->>U: Show access error
else Allowed
    BE->>D: Compile & run code vs all test cases<br/>(timeout + memory limit)
    D-->>BE: Output + exit status
    BE->>BE: Determine evaluation_status<br/>& compute current_score
    BE->>DB: Get TASK_PROGRESS (best_score)
    DB-->>BE: best_score
    alt current_score > best_score
        BE->>DB: Update best_score<br/>(set completed if score >= 80)
    else Not higher
        Note over BE: Keep existing best_score
    end
    BE-->>FE: Current result (evaluation_status,<br/>current_score, results, task_progress)
end
Note over BE,DB: Submitted code & detailed results are NOT stored
FE-->>U: Display current correction result

# API Documentation

## 1. Overview

SelfLab is an educational platform for creating and studying technical curriculums.

A curriculum contains:

```
Curriculum → Sprints → Projects → Tasks → Test Cases
```

A user can create curriculums, publish them, enroll in published curriculums, and solve C programming tasks.

Base URL:

```
/api/v1
```

All protected endpoints require:

```
Authorization: Bearer <token>
```

---

## 2. Roles

```
user
admin
```

- A curriculum owner is the user who created it.
- A trainee is a user enrolled in a curriculum.
- An admin can access all resources.
- Any authenticated user can create curriculums and enroll in published curriculums.

---

## 3. Common Response Format

Success:

```json
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {}
}
```

Error:

```json
{
  "success": false,
  "message": "Validation failed",
  "errors": []
}
```

Common status codes:

```
200 OK
201 Created
204 No Content
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
422 Validation Error
500 Internal Server Error
```

---

## 4. Core Product Rules

### Curriculum states

```
draft
published
```

A curriculum is created as `draft`.

Publishing is permanent:

```
draft → published
```

A published curriculum cannot be edited, unpublished, or deleted by any user, including admins.

This includes:

```
Curriculum details
Sprints
Projects
Tasks
Test cases
Task order
Sprint duration
Checker settings
```

Any attempt to modify or delete published content returns:

```
409 Conflict
```

### Publishing requirements

A curriculum can be published only when it has:

```
At least one sprint
At least one project
At least one task
At least one test case per task
Total test-case weight greater than zero per task
```

### Sprint access

An enrolled user can access:

```
Completed sprints
Current active sprint
```

An enrolled user cannot access projects, tasks, or checker endpoints inside future sprints.

Future sprint access returns:

```
403 Forbidden
```

### Task completion

A task is completed when:

```
score >= 80
```

A task cannot be completed when evaluation status is:

```
compile_error
runtime_error
timeout
internal_error
```

### Task progress

`TASK_PROGRESS` is managed internally.

It is created automatically on the user's first code check for a task.

```
No TaskProgress row = not_started
Score below 80 = in_progress
Score 80 or higher = completed
```

`best_score` never decreases:

```
best_score = max(previous_best_score, current_score)
```

Submitted code, old results, and submission history are not stored in the MVP.

---

## 5. Authentication and Profile

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| POST | `/auth/register` | Public | Create a user account |
| POST | `/auth/login` | Public | Log in and receive JWT |
| GET | `/me` | Authenticated | Get current user |
| PATCH | `/me` | Authenticated | Update current user profile |

### POST `/auth/register`

```json
{
  "name": "Yazeed",
  "email": "yazeed@example.com",
  "password": "StrongPassword123"
}
```

New users receive:

```
role = user
```

### POST `/auth/login`

```json
{
  "email": "yazeed@example.com",
  "password": "StrongPassword123"
}
```

---

## 6. Curriculum Endpoints

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| GET | `/curriculums` | Public | List published curriculums |
| POST | `/curriculums` | Authenticated | Create a draft curriculum |
| GET | `/curriculums/:curriculumId` | Public / Owner / Admin | Get curriculum details |
| PATCH | `/curriculums/:curriculumId` | Owner / Admin | Update draft curriculum |
| DELETE | `/curriculums/:curriculumId` | Owner / Admin | Delete draft curriculum |
| POST | `/curriculums/:curriculumId/publish` | Owner / Admin | Publish and permanently lock curriculum |
| GET | `/me/curriculums` | Authenticated | List curriculums created by current user |

### POST `/curriculums`

```json
{
  "title": "C Programming Fundamentals",
  "description": "Learn C through practical projects."
}
```

The system always creates the curriculum with:

```
status = draft
```

### GET `/curriculums`

Optional query parameters:

```
search
page
limit
```

Only published curriculums are returned.

### GET `/me/curriculums`

Optional query parameters:

```
search
status
page
limit
```

The response includes:

```
id
title
description
status
enrollments_count
is_locked
created_at
updated_at
```

`is_locked` is calculated as:

```
status === published
```

---

## 7. Enrollment Endpoints

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| POST | `/curriculums/:curriculumId/enroll` | Authenticated | Enroll in a published curriculum |
| GET | `/me/enrollments` | Authenticated | List current user's enrollments |
| GET | `/me/enrollments/:enrollmentId/roadmap` | Enrollment owner / Admin | Get sprint schedule |
| GET | `/me/enrollments/:enrollmentId/progress` | Enrollment owner / Admin | Get curriculum progress |

### POST `/curriculums/:curriculumId/enroll`

Rules:

```
Curriculum must be published.
A user cannot enroll twice in the same curriculum.
```

Duplicate enrollment returns:

```
409 Conflict
```

### Roadmap calculation

Sprint dates are calculated separately for each enrollment:

```
Enrollment.started_at
Sprint.order_index
Sprint.duration_days
```

Sprint status values:

```
upcoming
active
completed
```

---

## 8. Sprint Endpoints

All write operations are allowed only while the parent curriculum is `draft`.

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| GET | `/curriculums/:curriculumId/sprints` | Owner / Admin | List curriculum sprints |
| POST | `/curriculums/:curriculumId/sprints` | Owner / Admin | Create sprint |
| PATCH | `/sprints/:sprintId` | Owner / Admin | Update sprint |
| DELETE | `/sprints/:sprintId` | Owner / Admin | Delete sprint and its content |

### POST `/curriculums/:curriculumId/sprints`

```json
{
  "order_index": 1,
  "duration_days": 5
}
```

A sprint has no fixed date in the database.

---

## 9. Project Endpoints

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| GET | `/sprints/:sprintId/projects` | Owner / Admin / Eligible enrolled user | List sprint projects |
| POST | `/sprints/:sprintId/projects` | Owner / Admin | Create project |
| GET | `/projects/:projectId` | Owner / Admin / Eligible enrolled user | Get project details |
| PATCH | `/projects/:projectId` | Owner / Admin | Update project |
| DELETE | `/projects/:projectId` | Owner / Admin | Delete project and tasks |

### POST `/sprints/:sprintId/projects`

```json
{
  "title": "Variables and Conditions",
  "description": "Practice basic C syntax.",
  "order_index": 1
}
```

---

## 10. Task Endpoints

The MVP supports C tasks only. 

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| GET | `/projects/:projectId/tasks` | Owner / Admin / Eligible enrolled user | List tasks |
| POST | `/projects/:projectId/tasks` | Owner / Admin | Create task |
| GET | `/tasks/:taskId` | Owner / Admin / Eligible enrolled user | Get task details |
| PATCH | `/tasks/:taskId` | Owner / Admin | Update task |
| DELETE | `/tasks/:taskId` | Owner / Admin | Delete task and test cases |

### POST `/projects/:projectId/tasks`

```json
{
  "title": "Print Hello World",
  "description": "Write a C program that prints Hello World.",
  "order_index": 1,
  "points": 10,
  "timeout_seconds": 2,
  "memory_limit_mb": 128,
  "strict_compile_enabled": true,
  "memory_check_enabled": false,
  "output_match_mode": "ignore_trailing_spaces"
}
```

For enrolled users, task lists may include their current progress.

---

## 11. Test Case Endpoints

Test cases are visible only to the curriculum owner and admin.

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| GET | `/tasks/:taskId/test-cases` | Owner / Admin | List all test cases |
| POST | `/tasks/:taskId/test-cases` | Owner / Admin | Create test case |
| PATCH | `/test-cases/:testCaseId` | Owner / Admin | Update test case |
| DELETE | `/test-cases/:testCaseId` | Owner / Admin | Delete test case |

### POST `/tasks/:taskId/test-cases`

```json
{
  "input_data": "2 3",
  "expected_output": "5",
  "is_hidden": false,
  "weight": 25,
  "order_index": 1
}
```

Rules:

```
weight must be greater than zero.
Hidden test cases are never returned to enrolled users.
```

Score formula:

```
score = passed_weight / total_weight * 100
```

---

## 12. Code Checker Endpoint

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| POST | `/tasks/:taskId/check` | Enrolled user with available sprint | Compile and check C code |

### POST `/tasks/:taskId/check`

```json
{
  "code": "#include <stdio.h>\n\nint main(void)\n{\n    printf(\"Hello World\\n\");\n    return (0);\n}"
}
```

The system:

```
1. Verifies enrollment.
2. Verifies sprint access.
3. Compiles code in an isolated Docker container.
4. Runs all visible and hidden test cases.
5. Calculates current score.
6. Creates or updates TaskProgress.
7. Returns the current result.
```

Possible `evaluation_status` values:

```
success
compile_error
runtime_error
timeout
internal_error
```

Possible `completion_status` values:

```
in_progress
completed
```

Example result:

```json
{
  "success": true,
  "message": "Code checked successfully",
  "data": {
    "task_id": "task-id",
    "evaluation_status": "success",
    "current_score": 80,
    "all_tests_passed": false,
    "completion_status": "completed",
    "compile_output": null,
    "runtime_error": null,
    "execution_time_ms": 124,
    "task_progress": {
      "status": "completed",
      "best_score": 80,
      "completed_at": "2026-06-21T12:10:00Z",
      "last_checked_at": "2026-06-21T12:10:00Z"
    },
    "results": [
      {
        "is_hidden": false,
        "passed": true,
        "input_data": "2 3",
        "expected_output": "5",
        "actual_output": "5",
        "execution_time_ms": 30
      },
      {
        "is_hidden": true,
        "passed": false,
        "message": "A hidden test failed.",
        "execution_time_ms": 40
      }
    ]
  }
}
```

Rules:

```
Visible test cases may return input, expected output, and actual output.
Hidden test cases return pass/fail only.
Submitted code is not stored.
Detailed results are not stored.
internal_error does not update TaskProgress.
```

---

## 13. Health Check

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| GET | `/health` | Public | Check API status |

Example response:

```json
{
  "success": true,
  "message": "SelfLab API is running",
  "data": {
    "status": "ok"
  }
}
```

---

## 14. Excluded from MVP

```
Submission history
Stored source code
Previous correction results
Student solutions
Solution comments and ratings
Plagiarism detection
Notifications
Curriculum versioning
Multi-language checker support
Soft deletion
```

# SCM/QA

### 5. SCM and QA Strategies

#### 5.1. Source Control Management (SCM) Strategy

To keep our development process organized and conflict-free collaboration environment, the team will adhere to the following SCM practices using **GitHub**:

- **Branching Strategy:**
    - `main`: The production-ready branch. This branch is strictly locked.
    - `dev`: The central integration branch for ongoing development.
    - `feature/*`: Temporary branches created from `dev` for specific tasks (e.g., `feature/user-auth`).
- **Workflow & Branch Protection:**
    - Direct pushes to `main` are completely disabled.
    - All changes must be integrated via Pull Requests (PRs).
    - **Rule:** A minimum of **two approvals** from team members is required before any PR can be merged into the `main` branch to ensure code quality and shared understanding.
- **Commit Message Convention:**
    - The team will follow the Conventional Commits standard to maintain a readable history. Prefixes such as `feat:` (for new features), `fix:` (for bug fixes), and `docs:` (for documentation updates) will be mandatory.

#### 5.2. Quality Assurance (QA) Strategy

To guarantee the reliability of our MVP, we will implement a multi-layered testing and deployment strategy:

- **Testing Tools & Scope:**
    - **Jest:** Will be used for Unit Testing in both our Node.js (Back-end) and React (Front-end) environments to ensure individual components and functions behave as expected.
    - **Postman (via Newman):** Will be utilized to create and run automated API tests, ensuring our internal endpoints respond with the correct data and HTTP status codes.
- **Continuous Integration / Continuous Deployment (CI/CD):**
    - We will use **GitHub Actions** to automate our testing pipeline. The CI pipeline will be triggered automatically on **every direct push to the `dev` branch**, as well as on any Pull Request opened against `main`. The pipeline will automatically execute our Jest and Postman/Newman tests. Merging or pushing will be flagged, and merging into `main` will be blocked if any test fails.
- **Environment Management (Docker & Containers):**
    - To ensure environment consistency and eliminate configuration conflicts among team members, the project will be containerized using **Docker**.
    - We will utilize **Docker Compose** to manage and run our multi-container architecture simultaneously, separating the system components into isolated environments: one container for the front-end, one for the back-end, and one for the database.

#### 5.3. Technical Justifications

- **GitHub Strict Protection:** Requiring two PR approvals minimizes human error and ensures knowledge sharing across the team. Preventing direct pushes to `main` guarantees that only stable, reviewed code reaches production.
- **Continuous Testing on `dev` Push:** Triggering the CI pipeline on every push to the `dev` branch ensures that the core integration branch remains stable at all times. It provides immediate feedback to developers, catching bugs the moment code is shared, rather than delaying testing until a Pull Request is opened against `main`.
- **Conventional Commits:** Standardized commit messages make it easier to track changes, debug issues, and automatically generate changelogs in the future.
- **Docker & CI/CD Pipeline:** Using Docker ensures environment consistency across all developers' machines and production. Integrating GitHub Actions with Jest and Postman automates our QA process, catching bugs early in the development lifecycle rather than after deployment.

# Technical Justifications: Rationales for chosen technologies and designs:

# Technical Justifications

## Node.js, Express, and TypeScript

Node.js was selected for the backend because it is well suited for API-driven applications and supports asynchronous I/O efficiently. This is useful for SelfLab because the system handles authentication, curriculum management, enrollment, database operations, and communication with the code-checking service.

Express was chosen because it provides a lightweight and structured way to build REST APIs using routes, middleware, controllers, and error-handling layers. This keeps the backend easier to organize and maintain as the project grows.

TypeScript was used to reduce runtime errors and improve code reliability through static typing. It is particularly useful when handling structured data such as users, curriculums, tasks, test cases, and checker results.

## PostgreSQL and Prisma

PostgreSQL was selected because SelfLab contains strongly related data: users create curriculums, users enroll in curriculums, curriculums contain sprints, and tasks contain test cases. A relational database is appropriate because foreign keys, unique constraints, and transactions help preserve data integrity.

Prisma was chosen as the ORM because it provides type-safe database access, schema management, and migrations. It also reduces repetitive SQL code and helps keep the database schema aligned with the TypeScript backend.

## Docker-Based Code Checker

Docker was selected to execute trainee C code inside an isolated environment. User-submitted code must not run directly on the main backend server because it may contain infinite loops, consume excessive memory, or attempt unsafe operations.

Each check runs inside a restricted container with limits for execution time and memory. The container should run without network access, use a non-root user, and have restricted filesystem permissions. Docker improves reproducibility because every code check uses the same compiler and runtime environment.

## REST API Design

A REST API was chosen because the system is organized around clear resources such as curriculums, sprints, projects, tasks, enrollments, and test cases. REST endpoints provide predictable URL structures and use standard HTTP methods such as `GET`, `POST`, `PATCH`, and `DELETE`.

This design also separates the frontend from the backend, allowing each part to be developed and tested independently.

## JWT Authentication

JWT authentication was selected because the frontend and backend are separate components. The client sends the token in the authorization header with each protected request, allowing the backend to identify the current user without storing server-side sessions.

Authorization is enforced through middleware. Access is based on ownership, enrollment, and admin privileges. For example, only the curriculum owner or an admin can edit a draft curriculum, while only enrolled users can access available tasks.

## Two-Role Authorization Model

The MVP uses only two roles: `user` and `admin`. This reduces role-management complexity while still supporting the required business rules.

A normal user can create curriculums and enroll in published curriculums. Instead of creating separate roles such as trainee and curriculum designer, the system determines permissions from the user’s relationship to the resource:

- The creator of a curriculum is its owner.
- A user enrolled in a curriculum is treated as a trainee for that curriculum.
- An admin has global management privileges.

This approach keeps the authorization model simple while supporting the required features.

## Immutable Published Curriculums

A curriculum becomes permanently locked after publication. This prevents changes to task content, test cases, checker settings, sprint duration, or task order after users begin studying the curriculum.

The decision avoids inconsistent learning experiences where two trainees could be evaluated against different versions of the same curriculum. Since automatic versioning is outside the MVP scope, curriculum immutability provides a simpler and safer alternative.

## Computed Sprint Schedule

Sprint dates are not stored directly for every trainee. Instead, they are calculated from the enrollment start date, sprint order, and sprint duration.

This design avoids duplicating schedule records for every enrolled user. It also allows each trainee to start the same curriculum at a different time while following the same sprint structure.

## Task Progress and Best Score Only

The MVP stores only task progress and the trainee’s best score. Source code submissions, previous checker outputs, and detailed test results are not stored.

This decision reduces database growth, lowers storage requirements, and keeps the MVP focused on the essential learning flow. The `TaskProgress` record is created automatically on the first code check, and the best score never decreases.

## MVP Scope Control

The first version intentionally excludes features such as submission history, plagiarism detection, notifications, multi-language support, curriculum versioning, and student solution sharing.

These features increase system complexity and are not required to validate the core idea of SelfLab. Limiting the MVP scope allows the team to focus on the essential workflow: creating curriculums, enrolling users, progressing through sprints, and checking C code safely.
