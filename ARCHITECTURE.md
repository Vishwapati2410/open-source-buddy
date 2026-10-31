# Architecture: Open Source Buddy

> Draft. Open Source Buddy helps newcomers find, understand, and contribute to open source projects. Sections marked **(adjust)** are assumptions to align with the real codebase.

## 1. Goals

- Help contributors find beginner-friendly issues that match their skills and interests.
- Explain a repository quickly: structure, setup steps, contribution rules.
- Guide a contributor from "I picked an issue" to "my PR is open".
- Stay lightweight, easy to self-host, and easy for others to contribute to.

## 2. High-Level Overview

```
        +-----------+
        |   Client  |  (CLI / Web UI / Bot)  (adjust)
        +-----+-----+
              |
        +-----v-----+
        |    API    |  request validation, auth, rate limiting
        +-----+-----+
              |
   +----------+-----------+-------------------+
   |                      |                   |
+--v---------+   +--------v-------+   +-------v--------+
| Issue      |   | Repo Analyzer  |   | Guide          |
| Finder     |   | (structure,    |   | Generator      |
| (search +  |   | setup, CONTRIB |   | (step-by-step  |
| ranking)   |   | parsing)       |   | contribution)  |
+--+---------+   +--------+-------+   +-------+--------+
   |                      |                   |
   +----------+-----------+-------------------+
              |
      +-------v--------+        +-------------+
      | GitHub/GitLab  |        |  Storage    |
      | Integration    |        | (cache, user|
      | Layer          |        |  profiles)  |
      +----------------+        +-------------+
```

## 3. Components

### 3.1 Client layer **(adjust)**
Entry points for users: a CLI, a web frontend, or a chat/bot interface. Clients are thin; all logic lives behind the API.

### 3.2 API layer
- Validates input and handles authentication (OAuth with the hosting platform).
- Applies rate limiting to protect upstream platform quotas.
- Routes requests to the core services below.

### 3.3 Issue Finder
- Queries the platform for issues labeled `good first issue`, `help wanted`, etc.
- Ranks results by match with the user's skills, language preferences, repo activity, and maintainer responsiveness.
- Filters out stale, assigned, or already-claimed issues.

### 3.4 Repo Analyzer
- Reads README, CONTRIBUTING, CODE_OF_CONDUCT, and CI configuration.
- Produces a summary: tech stack, directory layout, how to build and run tests.
- Results are cached to avoid repeated API calls.

### 3.5 Guide Generator
- Combines issue details and repo analysis into an actionable checklist: fork, clone, set up, branch, test, commit, open PR.
- Surfaces project-specific rules (commit message style, PR template, required checks).

### 3.6 Platform Integration Layer
- Single abstraction over GitHub (and optionally GitLab) APIs.
- Handles pagination, retries, ETag/conditional requests, and token management.
- Keeps core services independent of any one platform.

### 3.7 Storage
- **Cache:** repo summaries and issue lists with TTLs.
- **User profile store:** skills, interests, past contributions, saved issues (optional, privacy-minimal).
- Start with SQLite or a file-based store; swap to Postgres if scaling is needed.

## 4. Key Data Flows

**Find an issue**
1. User submits skills/interests.
2. API calls Issue Finder, which queries the platform layer.
3. Results are scored, filtered, and returned.

**Understand a repo**
1. User selects an issue or repo.
2. Repo Analyzer fetches and parses docs and config (cache first).
3. Summary is returned and passed to the Guide Generator.

**Start contributing**
1. Guide Generator produces a tailored checklist.
2. Optionally, the tool tracks progress (issue claimed, PR opened).

## 5. Directory Structure **(adjust)**

```
open-source-buddy/
├── src/
│   ├── api/            # routes, auth, middleware
│   ├── core/
│   │   ├── issues/     # finder and ranking
│   │   ├── analyzer/   # repo analysis
│   │   └── guide/      # guide generation
│   ├── integrations/   # GitHub/GitLab clients
│   ├── storage/        # cache and profile stores
│   └── cli/ or web/    # client code
├── tests/
├── docs/
├── ARCHITECTURE.md
├── CONTRIBUTING.md
└── README.md
```

## 6. Design Decisions

| Decision | Rationale |
|---|---|
| Platform abstraction layer | Avoid lock-in to one host; simplifies testing with mocks |
| Aggressive caching | Respect API rate limits and speed up responses |
| Thin clients | One logic path, multiple interfaces |
| Minimal user data | Privacy and easier self-hosting |

## 7. Cross-Cutting Concerns

- **Security:** store tokens encrypted, request least-privilege OAuth scopes, never log secrets.
- **Rate limiting:** respect upstream quotas with backoff and conditional requests.
- **Testing:** unit tests for ranking and parsing; integration tests against recorded API fixtures.
- **Observability:** structured logs and basic metrics (cache hit rate, API errors).

## 8. Extending the Project

- **New platform:** implement the integration interface in `integrations/`.
- **New ranking signal:** add a scorer in `core/issues/` and register it in the ranking pipeline.
- **New client:** call the existing API; no core changes needed.

## 9. Future Work

- Mentor matching and community Q&A.
- Contribution progress tracking and badges.
- Support for non-code contributions (docs, translations, design).
- Webhooks for notifying users about new matching issues.
