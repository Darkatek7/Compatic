# Compatic — Project Plan

## 1. Executive Summary

Compatic is a Linux-hosted web platform for AI-assisted software development. It provides a browser-based workspace similar in spirit to Codex Web and OpenCode Web, but focused on **project orchestration**, **chat-driven coding**, **task management**, and **repository lifecycle management**.

At its core, Compatic should let a user:

- create or import a software project into the local filesystem,
- automatically initialize Git and Task Master,
- connect the project to one or more coding agents such as **OpenCode**, **Claude Code**, and **Codex CLI**,
- open persistent chat sessions per project,
- let those agents work inside the project directory,
- visualize Task Master tasks in a kanban-style project board,
- manage the full lifecycle of project work from idea to implementation.

Compatic is not the model itself. It is the **control plane and workspace UI** for local AI coding agents operating on project directories.

---

## 2. Product Vision

### Vision Statement
Build a self-hosted developer workspace where users can manage code projects, AI coding sessions, repositories, and Task Master workflows from one web interface.

### Product Thesis
Current AI coding tools are fragmented:

- coding happens in terminal or editor-specific UIs,
- task planning lives elsewhere,
- repository creation and cloning are separate steps,
- session history is scattered,
- multi-agent usage is inconsistent.

Compatic should unify those flows into one Linux-hosted application with clear project boundaries and reproducible filesystem-based workspaces.

### Core Promise
A user opens Compatic, creates or imports a project, and immediately gets:

- a project folder on disk,
- a Git repository,
- Task Master initialized,
- one or more AI chat sessions tied to that project,
- visible tasks on a kanban board,
- a repeatable environment for building software with AI.

---

## 3. Goals

### Primary Goals
1. **Project-first UX**  
   Every action starts from a project workspace.

2. **Filesystem-native architecture**  
   Projects exist as real directories on the Linux host, not as virtual-only records.

3. **Multi-agent support**  
   Allow users to work with OpenCode, Claude Code, and Codex CLI from one UI.

4. **Integrated planning + execution**  
   Combine Task Master planning and kanban visibility with live AI chat sessions.

5. **Git-first workflow**  
   Every project should be cloneable, versioned, and automation-friendly.

6. **Self-hosted simplicity**  
   Easy deployment on a Linux server with sane defaults.

### Secondary Goals
- Session persistence
- Auditability of actions
- Extensibility for new backends
- Team support later
- Safe execution boundaries per project

---

## 4. Non-Goals (Initial Version)

These should explicitly stay out of the first version unless required later:

- Full IDE replacement
- Real-time multi-user editing
- Built-in Git hosting platform
- Built-in CI/CD runner
- Kubernetes-scale multi-tenant orchestration
- Fine-tuned model hosting
- Full MCP platform from day one
- Advanced permission sandboxing beyond practical Linux host isolation

---

## 5. Target Users

### Primary User
**Solo developer / technical power user** running a Linux box or server and wanting a browser-accessible AI coding workspace.

### Secondary User
**Small technical team** that wants a lightweight shared internal coding cockpit.

### User Characteristics
- comfortable with Git,
- comfortable with Linux,
- wants self-hosting,
- uses AI coding agents heavily,
- values project structure and task visibility.

---

## 6. Key Use Cases

### Use Case A — Create New Project
A user clicks **New Project**, enters a project name, chooses a base path, and selects:

- initialize Git repo,
- initialize Task Master,
- choose default agent backend,
- optionally add remote repository later.

Result:
- directory created,
- `.git` initialized,
- Task Master initialized,
- project metadata stored in Compatic,
- first chat session optionally created.

### Use Case B — Clone Existing Repository
A user pastes a Git URL and optionally credentials method.

Result:
- repository cloned into managed base directory,
- project registered in Compatic,
- Task Master initialization offered or auto-run if absent,
- sessions and task board become available.

### Use Case C — Chat and Code Within Project
A user opens a project, starts a session with Claude Code / OpenCode / Codex CLI, sends prompts, and the backend works inside that project directory.

Result:
- chat history stored,
- tool actions logged,
- filesystem changes visible,
- Git state refreshes.

### Use Case D — Work Through Task Master Board
A user opens the project board, sees Task Master tasks grouped by state, clicks a task, reads details, and launches a chat session scoped to that task.

Result:
- clearer execution flow,
- tasks linked to sessions and commits,
- kanban acts as the project control center.

### Use Case E — Resume Prior Work
A user returns later, opens a project, sees:

- latest sessions,
- Git branch and status,
- open tasks,
- recent activity.

---

## 7. Product Requirements

## 7.1 Project Management

Compatic must support:

- create project from scratch,
- clone project from Git,
- register an existing local directory,
- rename project display name,
- archive project in UI without deleting files,
- optionally delete project metadata only,
- optionally delete project metadata and directory,
- configure per-project defaults.

### Project Creation Inputs
- project name,
- slug,
- local path,
- create empty directory vs clone repository,
- Git init toggle,
- Task Master init toggle,
- default backend,
- default branch name,
- optional environment template.

### Validation Rules
- path must remain under allowed workspace roots unless explicitly allowed,
- slug must be unique,
- no path traversal,
- no overwrite of non-empty directory without explicit import flow.

---

## 7.2 Git Integration

### Minimum Git Features
- initialize repository,
- clone repository,
- detect Git status,
- current branch display,
- changed files list,
- commit from UI,
- branch creation,
- pull / fetch / push,
- remote URL management.

### Nice-to-Have Later
- PR links,
- commit diff view,
- stash support,
- branch compare,
- Git provider OAuth.

### Automation Rules
When a project is created:
- if project is blank and Git toggle is on, run `git init`,
- if cloned, detect repository state automatically,
- set default branch if applicable,
- optionally create first commit if template files are generated.

---

## 7.3 Task Master Integration

### Desired Behavior
Task Master should be treated as a first-class project subsystem.

When enabled for a project:
- initialize Task Master automatically,
- detect existing Task Master config if already present,
- parse tasks into internal API responses,
- render tasks on a kanban board,
- open task detail views,
- optionally run Task Master commands from the UI,
- link tasks to sessions and commits.

### Core Board Columns
Initial proposal:
- Backlog
- Ready
- In Progress
- Review
- Done
- Blocked

### Task Data to Expose
- id,
- title,
- status,
- priority,
- dependencies,
- details,
- subtasks,
- test strategy,
- timestamps,
- linked sessions,
- linked commits.

### Task Master UI Actions
- refresh tasks from disk,
- generate tasks,
- update task status,
- open related task file or JSON,
- create session from task,
- filter by priority / status / dependency state.

---

## 7.4 Session and Chat System

### Requirements
Each project should support multiple sessions.

A session should include:
- backend type (OpenCode / Claude Code / Codex CLI),
- model/provider metadata where available,
- title,
- created by,
- timestamps,
- message history,
- execution events,
- task association,
- branch association,
- working directory.

### Messaging Capabilities
- send user prompt,
- stream assistant responses,
- display tool usage events,
- display file edits and command runs,
- resume old sessions,
- fork session from previous context,
- attach session to a task.

### Important UX Principle
The chat is not generic. It is always **project-scoped** and ideally **directory-scoped**.

---

## 7.5 Agent Backend Abstraction

Compatic should implement a backend adapter layer.

### Supported Initial Backends
1. **OpenCode**
2. **Claude Code**
3. **Codex CLI**

### Adapter Responsibilities
Each adapter should provide a standard interface:

- check availability,
- start session,
- send prompt,
- stream events,
- stop session,
- resume session if possible,
- return tool/action logs,
- expose backend-specific metadata,
- report health/version.

### Standard Compatic Interface (Conceptual)
```ts
interface AgentAdapter {
  id: string
  kind: "opencode" | "claude-code" | "codex-cli"
  isAvailable(): Promise<AvailabilityResult>
  createSession(input: CreateSessionInput): Promise<SessionHandle>
  sendMessage(sessionId: string, message: UserMessage): Promise<StreamHandle>
  stopSession(sessionId: string): Promise<void>
  listCapabilities(): Promise<CapabilityDescriptor[]>
  getHealth(): Promise<HealthStatus>
}
```

### Backend Strategy
Do not tightly couple Compatic to one backend's session format. Normalize into Compatic's own internal session/event schema.

---

## 7.6 Filesystem Model

### Workspace Design
All managed projects should live under one or more configured root directories, for example:

```text
/opt/compatic/workspaces/
  project-a/
  project-b/
```

### Rules
- project metadata references real paths,
- all file operations are explicit,
- path resolution is canonicalized,
- symbolic links should be handled carefully,
- destructive actions require confirmation.

### Recommended Internal Separation
- project source files remain in project directory,
- Compatic app data stored separately,
- no hidden mutations outside the project scope unless configured.

---

## 7.7 UI / UX Requirements

### Main Navigation
- Projects
- Sessions
- Tasks / Board
- Activity
- Settings
- Integrations

### Project Overview Page
Should show:
- project name and path,
- backend availability,
- Git summary,
- Task Master summary,
- recent sessions,
- latest activity,
- quick actions.

### Project Workspace Tabs
Suggested tabs:
- Overview
- Chat
- Tasks
- Git
- Files
- Settings

### New Project Wizard
- create blank project,
- clone repo,
- import existing folder,
- Git + Task Master defaults,
- backend defaults.

### Kanban Board UX
- drag-and-drop cards,
- task detail drawer,
- priority badges,
- dependency indicators,
- filter/search,
- create session from task.

### Chat UX
- left sidebar: sessions,
- center: messages and events,
- right panel: task, git, or file context,
- composer supports mode/backend selection,
- live streaming output,
- clear distinction between message output and command/file actions.

---

## 8. Suggested Technical Architecture

## 8.1 High-Level Architecture

```text
Browser UI
   |
   v
Compatic Web App (Frontend + API)
   |
   +-- Project Service
   +-- Session Service
   +-- Task Service
   +-- Git Service
   +-- Agent Adapter Layer
   |
   +-- PostgreSQL / SQLite
   +-- Redis (optional for queues / streams)
   +-- Filesystem Workspace Root
   |
   +-- OpenCode Adapter
   +-- Claude Code Adapter
   +-- Codex CLI Adapter
   +-- Task Master CLI Wrapper
   +-- Git CLI / libgit wrapper
```

## 8.2 Recommended Stack

### Option A — Pragmatic Web Stack
- **Frontend:** Next.js or React + Vite
- **Backend:** Node.js with NestJS / Fastify / Express
- **Database:** PostgreSQL for production, SQLite for dev
- **Realtime:** WebSockets / Server-Sent Events
- **Queue:** BullMQ or lightweight job queue if needed
- **Process execution:** child processes with robust supervision
- **Deployment:** Docker Compose on Linux host

### Option B — Fullstack TypeScript Monorepo
- Web app
- API server
- shared types package
- adapters package
- UI components package

### Recommendation
Use a **TypeScript monorepo**. It fits the need for shared models between frontend, backend, adapters, and task parsing.

---

## 9. Backend Services Breakdown

## 9.1 Project Service
Responsible for:
- project CRUD,
- filesystem path validation,
- template/scaffold orchestration,
- clone/init workflows,
- metadata persistence.

## 9.2 Git Service
Responsible for:
- invoking Git,
- status inspection,
- commit/pull/push,
- branch operations,
- remote management,
- mapping Git events to project activity.

## 9.3 Task Service
Responsible for:
- detecting Task Master,
- initializing Task Master,
- reading task files / JSON,
- transforming tasks into API shape,
- updating task state,
- exposing kanban board data.

## 9.4 Session Service
Responsible for:
- session lifecycle,
- storage of messages and events,
- adapter invocation,
- stream fan-out to UI,
- session restore/resume logic,
- task/session linking.

## 9.5 Integration / Adapter Service
Responsible for:
- backend registry,
- agent health checks,
- adapter configuration,
- capability discovery,
- version and availability checks.

## 9.6 Activity Service
Responsible for:
- audit log,
- timeline entries,
- events like project created, repo cloned, task updated, agent ran command, commit created.

---

## 10. Data Model Proposal

## 10.1 Project
```ts
Project {
  id
  name
  slug
  description
  path
  workspaceRoot
  sourceType // blank | clone | import
  gitEnabled
  taskMasterEnabled
  defaultBackend
  status // active | archived
  createdAt
  updatedAt
}
```

## 10.2 Repository State
```ts
RepositoryState {
  projectId
  isGitRepo
  currentBranch
  remotes
  isDirty
  aheadCount
  behindCount
  lastFetchedAt
}
```

## 10.3 Session
```ts
Session {
  id
  projectId
  taskId?
  backend
  title
  status // active | stopped | failed | completed
  workingDirectory
  branchName?
  createdAt
  updatedAt
}
```

## 10.4 Message
```ts
Message {
  id
  sessionId
  role // user | assistant | system | tool
  content
  createdAt
}
```

## 10.5 Session Event
```ts
SessionEvent {
  id
  sessionId
  type // command | file_edit | tool_call | diff | stderr | stdout | status
  payload
  createdAt
}
```

## 10.6 Task
```ts
Task {
  id
  externalId
  projectId
  title
  status
  priority
  details
  testStrategy
  dependencies
  parentTaskId?
  sourcePath
  syncedAt
}
```

## 10.7 Activity Entry
```ts
ActivityEntry {
  id
  projectId
  actor
  type
  summary
  metadata
  createdAt
}
```

---

## 11. Core Flows

## 11.1 New Project Flow
1. User clicks **New Project**
2. Selects blank / clone / import
3. Defines local path and options
4. Backend validates path
5. Backend creates or clones directory
6. Git initialized if selected
7. Task Master initialized if selected
8. Project metadata persisted
9. Default session optionally created
10. Project overview shown

## 11.2 Clone Repository Flow
1. User enters Git URL
2. System validates target path
3. Clone starts as background job
4. Progress streamed to UI
5. Git state detected
6. Task Master detection or initialization runs
7. Project imported into Compatic

## 11.3 Start Chat Session Flow
1. User opens project
2. Selects backend
3. Optional task selected
4. Session starts in project working directory
5. Messages stream live
6. Actions logged as events
7. Session remains resumable

## 11.4 Task-Centric Flow
1. User opens board
2. Selects task card
3. Opens task drawer
4. Clicks **Start Working**
5. New session launches scoped to task context
6. Task status changes to In Progress
7. Commit/session linkage captured

---

## 12. Task Master Integration Strategy

## 12.1 Initialization
On project creation:
- detect whether Task Master already exists,
- if absent and enabled, run initialization command,
- save resulting config presence in project metadata,
- trigger initial task sync.

## 12.2 Sync Modes
### Passive Sync
Read task files and update board state at intervals or on page open.

### Active Sync
After Compatic-initiated actions, immediately refresh task state.

### Future Sync
File watcher on task files to detect external changes.

## 12.3 Compatic Abstraction
Do not hard-code Task Master internals into the UI. Use a normalization layer:
- raw Task Master files/commands on one side,
- Compatic task DTOs on the other.

This makes future support for other task systems possible.

---

## 13. Git Integration Strategy

## 13.1 First Iteration
Prefer shelling out to the system Git binary rather than adding a complex Git abstraction too early.

### Why
- highly compatible,
- easy to debug,
- behavior matches user expectations,
- works well on Linux hosts.

## 13.2 Required Safeguards
- whitelist working directory root,
- explicit timeouts,
- output capture,
- redaction of secrets in logs,
- controlled environment variables.

---

## 14. Agent Integration Strategy

## 14.1 OpenCode
OpenCode is the strongest candidate for the first deep integration because it already has:
- a web interface,
- a server mode,
- CORS/auth options,
- an SDK,
- multi-session concepts.

Compatic can either:
- embed/wrap an OpenCode server per project or host,
- or integrate at the SDK/API layer and keep Compatic as the main UI.

### Recommendation
Use OpenCode as the **reference backend** for the first full session implementation.

## 14.2 Claude Code
Claude Code should initially be treated as a CLI/session adapter.

Possible first-version approach:
- launch in project directory,
- capture stdout/stderr,
- preserve transcript where possible,
- expose it through Compatic's session event system.

## 14.3 Codex CLI
Codex CLI should follow the same initial adapter model:
- launch in project directory,
- capture interaction events,
- normalize outputs,
- keep backend-specific behavior behind the adapter.

## 14.4 Adapter Maturity Levels
### Level 1
Start command, send prompt, capture output.

### Level 2
Stream events, detect tool actions, restore session metadata.

### Level 3
Rich action introspection, structured diffs, advanced backend-specific controls.

---

## 15. Security and Isolation

## 15.1 Threat Model
Compatic controls tools that can modify files, run commands, and access repositories. That makes host safety a core design constraint.

## 15.2 Minimum Protections
- restrict projects to configured workspace roots,
- sanitize all paths,
- role separation between UI/API and execution workers if possible,
- per-project process working directory,
- configurable execution user,
- secret storage separate from logs,
- authentication for the web UI,
- audit log for destructive actions.

## 15.3 Later Protections
- containerized per-project runners,
- resource limits,
- network policy per runner,
- per-user RBAC,
- approval gates for dangerous actions.

---

## 16. Deployment Plan

## 16.1 Initial Deployment Target
Single Linux host via Docker Compose.

### Containers
- compatic-web
- compatic-api
- postgres
- redis (optional)
- reverse proxy (Caddy / Nginx / Traefik)

### Mounted Volumes
- application data,
- workspace root,
- logs,
- backups.

## 16.2 Configuration
Use environment variables for:
- database connection,
- workspace roots,
- auth secret,
- allowed Git settings,
- agent executable paths,
- Task Master executable path,
- default backend.

---

## 17. MVP Definition

## MVP Must Include
1. Authentication for one admin user
2. Project create / clone / import
3. Real filesystem-backed projects
4. Git init + status + commit
5. Task Master auto-init + task sync
6. Kanban board from Task Master data
7. One working chat backend end-to-end
8. Session history per project
9. Activity log
10. Docker Compose deployment

## MVP Should Include
- OpenCode as first-class backend
- Claude Code and Codex CLI as experimental adapters
- basic branch switching
- task-to-session linking

## MVP Does Not Need
- team permissions,
- advanced Git UI,
- full file browser editor,
- PR workflows,
- notifications,
- plugin marketplace.

---

## 18. Recommended Delivery Phases

## Phase 0 — Discovery and Spikes
Goal: validate integration feasibility.

Deliverables:
- test project directory lifecycle,
- test Git init/clone wrappers,
- test Task Master init and task parsing,
- spike OpenCode session integration,
- spike Claude Code and Codex CLI adapters,
- document unified event model.

## Phase 1 — Core Platform Foundation
Deliverables:
- authentication,
- project model + database,
- workspace root config,
- project CRUD,
- clone/import/init flows,
- activity log,
- basic dashboard.

## Phase 2 — Git + Task Master
Deliverables:
- Git service,
- Task Master initializer,
- task sync service,
- kanban board,
- task details,
- task/session linkage model.

## Phase 3 — Sessions and Chat
Deliverables:
- session service,
- normalized message/event model,
- streaming chat UI,
- first working adapter,
- session persistence.

## Phase 4 — Multi-Backend Support
Deliverables:
- adapter registry,
- Claude Code adapter,
- Codex CLI adapter,
- backend settings UI,
- health checks.

## Phase 5 — Hardening and UX Improvement
Deliverables:
- better logs,
- process supervision,
- safer execution boundaries,
- activity filtering,
- better onboarding,
- performance tuning.

---

## 19. Suggested Milestones

### Milestone 1
Create, clone, and register projects successfully.

### Milestone 2
Every new project can auto-initialize Git and Task Master.

### Milestone 3
Task Master tasks render correctly as kanban cards.

### Milestone 4
A user can start a chat session in a project and persist history.

### Milestone 5
OpenCode works end-to-end as the first fully integrated backend.

### Milestone 6
Claude Code and Codex CLI work through the adapter layer.

### Milestone 7
Compatic can be deployed reproducibly via Docker Compose.

---

## 20. Risks and Mitigations

### Risk 1 — Backend Incompatibility
Different agents expose different interaction models.

**Mitigation:** normalize to Compatic's own event schema and keep adapters isolated.

### Risk 2 — Fragile CLI Automation
CLI output formats may change.

**Mitigation:** build tolerant parsers, version checks, and degrade gracefully.

### Risk 3 — Unsafe Filesystem / Command Execution
A bad path or command flow could affect the host.

**Mitigation:** strict path controls, execution policy, timeouts, and audit logs.

### Risk 4 — Task Master Schema Drift
Task Master may evolve.

**Mitigation:** keep a versioned parser layer and test fixtures from real projects.

### Risk 5 — Overbuilding Too Early
Trying to match full IDEs will delay delivery.

**Mitigation:** stay focused on orchestration, sessions, tasks, and Git.

---

## 21. Open Design Questions

These are the main questions still worth deciding early:

1. Should Compatic support **one global agent runtime** or **per-project runtimes**?
2. Should chat sessions be stored only in Compatic DB, or also mirrored into project files?
3. Should Task Master status changes be written directly by Compatic, or should Compatic mostly call official Task Master commands?
4. Should Git actions use shell commands only, or move to a library later?
5. Do you want a built-in file viewer/editor in MVP, or only external editor links?
6. Should agents run as the same Linux user as Compatic, or through a dedicated runner user?
7. Will multi-user support matter soon, or can v1 stay single-admin?

---

## 22. Recommended Product Positioning

### One-Sentence Positioning
Compatic is a self-hosted web workspace for managing AI-assisted coding projects with Git, chat sessions, and Task Master workflows on a Linux host.

### What Makes It Distinct
- project-first, not just chat-first,
- filesystem-native,
- backend-agnostic,
- task-board integrated,
- self-hosted and operations-friendly.

---

## 23. Recommended First Build Order

If implementation starts immediately, the best order is:

1. Project creation/import/clone
2. Git initialization and status
3. Task Master init and sync
4. Project overview UI
5. Kanban board
6. Session model and storage
7. OpenCode backend integration
8. Claude Code adapter
9. Codex CLI adapter
10. hardening and polish

This sequence gets the highest-value workflow working as early as possible.

---

## 24. Acceptance Criteria for v1

Compatic v1 is successful if a user can:

1. Open the web UI on a Linux-hosted instance
2. Create a new project or clone an existing repo
3. Get Git and Task Master initialized automatically
4. See the project on disk and in the UI
5. Open a kanban board showing Task Master tasks
6. Start a chat session tied to that project
7. Use at least one backend to make changes in the repo
8. Review Git status and commit changes
9. Return later and resume work with session/task history intact

---

## 25. Final Recommendation

The goal is clear and strong.

The best way to build Compatic is to treat it as:

- a **project workspace manager**,
- a **session orchestrator** for local AI coding agents,
- a **Task Master visualization/control layer**,
- and a **Git-native self-hosted development cockpit**.

The biggest success factor will be disciplined scope control:
- start with one excellent backend integration,
- make Git + Task Master reliable,
- ensure project lifecycle management is solid,
- then expand backend depth and UI sophistication.

