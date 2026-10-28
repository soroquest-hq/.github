# SoroQuest — Drips Wave Contributor & Maintainer Roadmap

> **Status:** 🚀 **Core MVP Deployed on Stellar Testnet & Live on Vercel/Render**  
> **Open Contributor Capacity:** **125+ Scoped Tasks Across Multiple Wave Cycles**

This document serves as the master blueprint for SoroQuest maintainers and **Drips Wave evaluators & open-source contributors**. 

While the core protocol foundation is fully functional and deployed (Stellar Testnet escrow contract, Go event indexer on Render, Next.js dApp on Vercel), SoroQuest has been architected with a **massive, modular contributor surface area**. Over 125+ standalone tasks are structured across the 3 repositories to power continuous weekly Wave sprint cycles.

---

### Contributor Runway Overview

| Tier | Focus Area | Repositories Involved | Target Tasks | Complexity Spread |
| :--- | :--- | :--- | :---: | :--- |
| **Active Wave Sprint (Day 1)** | Immediate onboarding issues | `contracts`, `indexer`, `app` | **15 Issues** | 5 trivial, 5 medium, 5 high |
| **Wave Cycles 2 & 3** | Core protocol extensions & UX | `contracts`, `indexer`, `app` | **50+ Issues** | 15 trivial, 25 medium, 10 high |
| **Wave Cycles 4+ & Mainnet** | Ecosystem tooling & integrations | `contracts`, `indexer`, `app` | **60+ Issues** | 20 trivial, 25 medium, 15 high |
| **Total Program Capacity** | Full decentralization roadmap | Entire Organization | **125+ Issues** | Balanced across all skill tiers |

---

### Contributor Task Criteria
Every task in this roadmap adheres strictly to Drips Wave standards:
1. **Self-Contained**: No issue depends on another open issue completing first.
2. **Explicit Scope**: Exact target file paths, expected behaviors, and edge cases specified.
3. **Verified Definition of Done**: Clear automated test and review requirements (`Closes #<id>`).
4. **Graded Complexity**: Categorized into `complexity: trivial`, `complexity: medium`, and `complexity: high` for fair Drips point distribution.

---

## Phase 1 — Smart Contract (`soroquest-contracts`)

### 1.1 — Repository setup

#### 1.1.1 Initialise Rust workspace
Create the root `Cargo.toml` workspace file and the
`contracts/soroquest-escrow/` crate with a minimal `Cargo.toml`
importing `soroban-sdk`. The workspace must compile with
`cargo build` before any contract logic is written.

**Files:** `Cargo.toml`, `contracts/soroquest-escrow/Cargo.toml`
**Done when:** `cargo build` exits 0 on a clean checkout.

---

#### 1.1.2 Add Apache-2.0 licence file
Add `LICENSE` containing the full Apache-2.0 licence text.
Update `Cargo.toml` to set `license = "Apache-2.0"`.

**Files:** `LICENSE`, `Cargo.toml`
**Done when:** File exists, licence field set in manifest.

---

#### 1.1.3 Write `CONTRIBUTING.md`
Cover: prerequisites (Rust, stellar CLI), how to clone and build,
how to run tests (`cargo test`), branch naming convention
(`feat/`, `fix/`, `chore/`), commit message format
(Conventional Commits), PR requirements (linked issue, test output
in description, `Closes #<id>`), and the rule that assignment is
required before starting work.

**Files:** `CONTRIBUTING.md`
**Done when:** A first-time contributor can set up and run tests
using only this file.

---

#### 1.1.4 Write `SECURITY.md`
State that security vulnerabilities must be reported privately via
GitHub's security advisory flow, not as public issues. Include the
direct link to the advisory page for this repo. Do not include an
email address.

**Files:** `SECURITY.md`
**Done when:** File exists, advisory link is correct and resolves.

---

#### 1.1.5 Write `CODE_OF_CONDUCT.md`
Add the Contributor Covenant v2.1 in full.

**Files:** `CODE_OF_CONDUCT.md`
**Done when:** File exists with the full covenant text.

---

#### 1.1.6 Add GitHub issue templates
Create `.github/ISSUE_TEMPLATE/bug_report.md` and
`.github/ISSUE_TEMPLATE/feature_request.md`. Each template must
include sections for description, steps to reproduce or motivation,
expected behaviour, and environment. Add
`.github/ISSUE_TEMPLATE/config.yml` disabling blank issues and
pointing to Discussions for questions.

**Files:** `.github/ISSUE_TEMPLATE/`
**Done when:** Both templates appear in the GitHub UI when
opening a new issue.

---

#### 1.1.7 Add GitHub PR template
Create `.github/pull_request_template.md` with sections for:
linked issue (`Closes #`), description of changes, test output
(paste `cargo test` result), checklist (tests pass, clippy clean,
docs updated).

**Files:** `.github/pull_request_template.md`
**Done when:** Template auto-populates when opening a new PR.

---

#### 1.1.8 Set up GitHub Actions CI
Create `.github/workflows/ci.yml` that runs on every push and PR
to `main`. Steps: checkout, install Rust stable, `cargo build`,
`cargo clippy -- -D warnings`, `cargo test`. Add the CI status
badge to `README.md`.

**Files:** `.github/workflows/ci.yml`, `README.md`
**Done when:** CI passes on a clean push. Badge shows green.

---

#### 1.1.9 Add deployments directory
Create `deployments/testnet.json` and `deployments/mainnet.json`
with placeholder structure:
`{ "contract_id": "", "deployed_at": "", "network": "" }`.
Add a note in `README.md` explaining these files will be populated
after deployment.

**Files:** `deployments/testnet.json`, `deployments/mainnet.json`
**Done when:** Both files exist with the correct JSON structure.

---

### 1.2 — Data types and storage

#### 1.2.1 Define `BountyStatus` enum
In `contracts/soroquest-escrow/src/types.rs`, define and export
the `BountyStatus` enum with variants: `Open`, `Claimed`,
`Completed`, `Cancelled`. Annotate with `#[contracttype]` and
`#[derive(Clone, Debug, PartialEq)]`.

**Files:** `contracts/soroquest-escrow/src/types.rs`
**Done when:** File compiles. Unit test asserts each variant is
distinct.

---

#### 1.2.2 Define `Bounty` struct
In `types.rs`, define and export the `Bounty` struct with fields:
`id: u64`, `owner: Address`, `title: String`,
`description: String`, `amount: i128`, `token: Address`,
`status: BountyStatus`, `claimant: Option<Address>`,
`created_at: u64`, `claim_deadline: u64`.
Annotate with `#[contracttype]`.

**Files:** `contracts/soroquest-escrow/src/types.rs`
**Done when:** Struct compiles. Unit test constructs a `Bounty`
value and asserts field values.

---

#### 1.2.3 Define `SoroQuestError` enum
In `contracts/soroquest-escrow/src/error.rs`, define the
`SoroQuestError` enum with named variants and explicit integer
discriminants: `BountyNotFound = 1`, `BountyNotOpen = 2`,
`BountyNotClaimed = 3`, `AlreadyClaimed = 4`, `NotOwner = 5`,
`DeadlineNotPassed = 6`, `InvalidAmount = 7`,
`InvalidDeadline = 8`, `TransferFailed = 9`.
Annotate with `#[contracterror]`.

**Files:** `contracts/soroquest-escrow/src/error.rs`
**Done when:** Enum compiles. Unit test asserts each discriminant
value matches its integer.

---

#### 1.2.4 Define `StorageKey` enum
In `contracts/soroquest-escrow/src/storage.rs`, define the
`StorageKey` enum: `BountyCount` and `Bounty(u64)`.
Annotate with `#[contracttype]`.

**Files:** `contracts/soroquest-escrow/src/storage.rs`
**Done when:** Enum compiles cleanly.

---

#### 1.2.5 Implement storage read/write helpers
In `storage.rs`, implement: `get_bounty_count(env) -> u64`,
`set_bounty_count(env, count: u64)`, `get_bounty(env, id: u64) -> Result<Bounty, SoroQuestError>`,
`set_bounty(env, bounty: Bounty)`. All persistent storage writes
must extend TTL with `env.storage().persistent().extend_ttl()`.

**Files:** `contracts/soroquest-escrow/src/storage.rs`
**Done when:** Unit tests in the Soroban test environment assert
each helper reads back what was written and that TTL extension
is called.

---

### 1.3 — Events

#### 1.3.1 Implement event emission helpers
In `contracts/soroquest-escrow/src/events.rs`, implement four
functions: `emit_posted(env, id, owner, amount, token, deadline)`,
`emit_claimed(env, id, claimant)`,
`emit_completed(env, id, claimant, amount)`,
`emit_cancelled(env, id, owner, amount)`. Each uses
`env.events().publish()` with a two-element topic tuple and a
data payload.

**Files:** `contracts/soroquest-escrow/src/events.rs`
**Done when:** Unit tests in the Soroban test environment assert
each function publishes an event with the correct topic and data.

---

### 1.4 — Contract functions

#### 1.4.1 Implement `post_bounty`
In `lib.rs`, implement `post_bounty(env, owner, title, description,
amount, token, claim_deadline) -> Result<u64, SoroQuestError>`.
Requirements: call `owner.require_auth()`, validate `amount > 0`,
transfer `amount` of `token` from `owner` to the contract using
the SAC token interface, create and store a new `Bounty` with
status `Open`, increment `BountyCount`, emit `bounty_posted`
event, return the new bounty ID. Return
`SoroQuestError::InvalidAmount` if amount is zero or negative.

**Files:** `contracts/soroquest-escrow/src/lib.rs`
**Done when:** Test registers contract, calls `post_bounty`,
asserts returned ID is `0`, asserts stored bounty has correct
fields and status `Open`, asserts token was transferred.

---

#### 1.4.2 Implement `claim_bounty`
In `lib.rs`, implement `claim_bounty(env, claimant, bounty_id) -> Result<(), SoroQuestError>`.
Requirements: call `claimant.require_auth()`, load bounty (return
`BountyNotFound` if missing), return `BountyNotOpen` if status is
not `Open`, set status to `Claimed`, set `claimant` field, store
updated bounty, emit `bounty_claimed` event.

**Files:** `contracts/soroquest-escrow/src/lib.rs`
**Done when:** Tests assert: happy path sets status to `Claimed`;
calling on a non-existent bounty returns `BountyNotFound`;
calling on a `Claimed` bounty returns `BountyNotOpen`.

---

#### 1.4.3 Implement `approve_completion`
In `lib.rs`, implement `approve_completion(env, owner, bounty_id) -> Result<(), SoroQuestError>`.
Requirements: call `owner.require_auth()`, load bounty, return
`NotOwner` if caller is not the bounty owner, return
`BountyNotClaimed` if status is not `Claimed`, transfer full
`amount` from contract to `claimant`, set status to `Completed`,
store updated bounty, emit `bounty_completed` event.

**Files:** `contracts/soroquest-escrow/src/lib.rs`
**Done when:** Tests assert: happy path transfers USDC to claimant
and sets status to `Completed`; non-owner call returns `NotOwner`;
calling on `Open` bounty returns `BountyNotClaimed`.

---

#### 1.4.4 Implement `cancel_bounty`
In `lib.rs`, implement `cancel_bounty(env, owner, bounty_id) -> Result<(), SoroQuestError>`.
Requirements: call `owner.require_auth()`, load bounty, return
`NotOwner` if caller is not owner, allow cancellation if status
is `Open`, allow cancellation if status is `Claimed` AND
`claim_deadline > 0` AND current ledger sequence exceeds deadline
(return `DeadlineNotPassed` otherwise), transfer full `amount`
back to owner, set status to `Cancelled`, clear `claimant`, store,
emit `bounty_cancelled` event.

**Files:** `contracts/soroquest-escrow/src/lib.rs`
**Done when:** Tests assert: cancel on `Open` bounty succeeds;
cancel on `Claimed` bounty before deadline returns
`DeadlineNotPassed`; cancel on `Claimed` bounty after deadline
succeeds and returns USDC.

---

#### 1.4.5 Implement `get_bounty`
In `lib.rs`, implement `get_bounty(env, bounty_id: u64) -> Result<Bounty, SoroQuestError>`.
This is a read-only view function — no auth, no state change.
Return `BountyNotFound` if the ID does not exist.

**Files:** `contracts/soroquest-escrow/src/lib.rs`
**Done when:** Test asserts correct bounty is returned after
posting; calling with a non-existent ID returns `BountyNotFound`.

---

#### 1.4.6 Implement `list_bounties`
In `lib.rs`, implement `list_bounties(env, status: Option<BountyStatus>, offset: u64, limit: u64) -> Vec<Bounty>`.
Iterates storage from `offset` to `offset + limit`, filters by
status if provided. Returns an empty vec if no bounties match.
Limit must be capped at 50 — reject or clamp higher values.

**Files:** `contracts/soroquest-escrow/src/lib.rs`
**Done when:** Tests assert: returns all bounties when no filter;
returns only matching bounties when status filter is set;
pagination returns correct subset; empty result for out-of-range
offset.

---

#### 1.4.7 Implement `get_bounty_count`
In `lib.rs`, implement `get_bounty_count(env) -> u64`.
Returns the total number of bounties ever posted (not just open ones).
No auth required.

**Files:** `contracts/soroquest-escrow/src/lib.rs`
**Done when:** Test asserts count is 0 before any posts, 1 after
one post, 3 after three posts regardless of status.

---

### 1.5 — Tests

#### 1.5.1 Write full lifecycle integration test
Write a single test that walks the complete happy path:
post bounty → verify `Open` → claim → verify `Claimed` →
approve → verify `Completed` → assert claimant USDC balance
increased by bounty amount → assert contract USDC balance is 0.

**Files:** `contracts/soroquest-escrow/src/test.rs`
**Done when:** Test passes. `cargo test` exits 0.

---

#### 1.5.2 Write cancellation path tests
Write tests for every cancellation scenario: cancel open bounty
(succeeds, USDC returned to owner), cancel claimed bounty before
deadline (fails with `DeadlineNotPassed`), cancel claimed bounty
after deadline (succeeds, USDC returned), cancel bounty with no
deadline set (always succeeds when claimed).

**Files:** `contracts/soroquest-escrow/src/test.rs`
**Done when:** All four tests pass.

---

#### 1.5.3 Write authorization failure tests
Write tests that confirm every `require_auth` is enforced:
`post_bounty` without owner auth fails, `claim_bounty` without
claimant auth fails, `approve_completion` without owner auth fails,
`cancel_bounty` without owner auth fails,
`approve_completion` by non-owner returns `NotOwner`,
`cancel_bounty` by non-owner returns `NotOwner`.

**Files:** `contracts/soroquest-escrow/src/test.rs`
**Done when:** All six tests pass.

---

#### 1.5.4 Write event emission tests
Write tests that assert the correct event is published after each
state change. Use `env.events().all()` to inspect published events.
Assert topic and data payload for: `posted`, `claimed`,
`completed`, `cancelled`.

**Files:** `contracts/soroquest-escrow/src/test.rs`
**Done when:** All four event assertions pass.

---

#### 1.5.5 Write edge case tests
Write tests for: posting a bounty with `amount = 0` returns
`InvalidAmount`, claiming an already-claimed bounty returns
`BountyNotOpen`, approving a cancelled bounty returns
`BountyNotClaimed`, `list_bounties` with limit above 50 is clamped,
`get_bounty` with non-existent ID returns `BountyNotFound`.

**Files:** `contracts/soroquest-escrow/src/test.rs`
**Done when:** All five tests pass.

---

### 1.6 — Documentation

#### 1.6.1 Write `README.md`
Cover: what the contract does (two sentences), prerequisites,
how to build (`stellar contract build`), how to run tests
(`cargo test`), deployed contract addresses (testnet + mainnet),
links to Stellar Expert for both networks, link to
`docs/contract-reference.md`, CI badge.

**Files:** `README.md`
**Done when:** A developer unfamiliar with the project can build
and test it using only the README.

---

#### 1.6.2 Write `docs/contract-reference.md`
Document every public function: description, parameters with
types, return type, auth requirement, errors it can return,
and a `stellar contract invoke` CLI example. Include the
`BountyStatus` and `SoroQuestError` enum reference tables.

**Files:** `docs/contract-reference.md`
**Done when:** Every public function is documented with at least
one CLI example.

---

#### 1.6.3 Write `docs/architecture.md`
Describe: overall contract design, storage layout (`StorageKey`
enum and what each key holds), token transfer pattern (SAC
interface), authorization flow (step-by-step pattern), event
schema, and instructions for adding a new contract function.

**Files:** `docs/architecture.md`
**Done when:** A new contributor can understand the full contract
design without reading source code.

---

### 1.7 — Deployment

#### 1.7.1 Deploy contract to testnet
Build the WASM with `stellar contract build`. Deploy to testnet
with `stellar contract deploy --network testnet`. Record the
contract address and deployment ledger in
`deployments/testnet.json`. Invoke `get_bounty_count` on-chain
to confirm liveness.

**Files:** `deployments/testnet.json`
**Done when:** Contract address is recorded. `get_bounty_count`
returns `0` via CLI invoke on testnet.

---

#### 1.7.2 Deploy contract to mainnet
Deploy to mainnet with `stellar contract deploy --network mainnet`.
Record address and deployment ledger in
`deployments/mainnet.json`. Invoke `get_bounty_count` on mainnet
to confirm liveness. Update `README.md` with both final addresses.

**Files:** `deployments/mainnet.json`, `README.md`
**Done when:** Contract address is recorded. `get_bounty_count`
returns `0` via CLI invoke on mainnet. README shows both addresses.

---

## Phase 2 — Indexer (`soroquest-indexer`)

### 2.1 — Repository setup

#### 2.1.1 Initialise Go module
Run `go mod init github.com/soroquest-hq/soroquest-indexer`.
Create `cmd/indexer/main.go` with an empty `main()` that prints
`"soroquest-indexer starting"` and exits 0. Verify with
`go build ./...`.

**Files:** `go.mod`, `cmd/indexer/main.go`
**Done when:** `go build ./...` exits 0.

---

#### 2.1.2 Add Apache-2.0 licence, `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`
Same requirements as `soroquest-contracts` tasks 1.1.2–1.1.5,
adapted for Go: prerequisites are Go 1.22+, `golangci-lint`,
and PostgreSQL. Build command is `go build ./...`.
Test command is `go test ./...`.

**Files:** `LICENSE`, `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`
**Done when:** All four files exist and are correct.

---

#### 2.1.3 Add GitHub issue templates, PR template
Same requirements as task 1.1.6 and 1.1.7, adapted for Go.
PR template checklist must include: `go test ./...` passes,
`golangci-lint run` clean, `Closes #<id>` in description.

**Files:** `.github/ISSUE_TEMPLATE/`, `.github/pull_request_template.md`
**Done when:** Templates appear in GitHub UI.

---

#### 2.1.4 Set up GitHub Actions CI
Create `.github/workflows/ci.yml`. Steps: checkout, install Go
1.22, install `golangci-lint`, `go build ./...`, `go vet ./...`,
`golangci-lint run`, `go test -race ./...`. Add CI badge to
`README.md`.

**Files:** `.github/workflows/ci.yml`, `README.md`
**Done when:** CI passes on clean push. Badge shows green.

---

#### 2.1.5 Add `golangci-lint` config
Create `.golangci.yml` enabling: `errcheck`, `gosimple`, `govet`,
`ineffassign`, `staticcheck`, `unused`. Set `timeout: 5m`.

**Files:** `.golangci.yml`
**Done when:** `golangci-lint run` exits 0 on the initial codebase.

---

### 2.2 — Database

#### 2.2.1 Write migration 001 — create `bounties` table
Create `migrations/001_create_bounties.up.sql` and
`migrations/001_create_bounties.down.sql`. Up: create `bounties`
table with columns: `id BIGINT PRIMARY KEY`, `owner TEXT NOT NULL`,
`title TEXT NOT NULL`, `description TEXT NOT NULL`,
`amount NUMERIC NOT NULL`, `token TEXT NOT NULL`,
`status TEXT NOT NULL`, `claimant TEXT`,
`created_at BIGINT NOT NULL`, `claim_deadline BIGINT NOT NULL`,
`updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()`. Add indexes on
`status` and `owner`. Down: drop table.

**Files:** `migrations/001_create_bounties.up.sql`,
`migrations/001_create_bounties.down.sql`
**Done when:** Migration applies and rolls back cleanly against
a local PostgreSQL instance.

---

#### 2.2.2 Write migration 002 — create `events` table
Create `migrations/002_create_events.up.sql` and down.
Up: create `events` table: `id BIGSERIAL PRIMARY KEY`,
`bounty_id BIGINT NOT NULL REFERENCES bounties(id)`,
`event_type TEXT NOT NULL`, `ledger BIGINT NOT NULL`,
`tx_hash TEXT NOT NULL`, `payload JSONB NOT NULL`,
`created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()`. Index on
`bounty_id` and `ledger`. Down: drop table.

**Files:** `migrations/002_create_events.up.sql`,
`migrations/002_create_events.down.sql`
**Done when:** Migration applies and rolls back cleanly.

---

#### 2.2.3 Write migration 003 — create `cursor` table
Create `migrations/003_create_cursor.up.sql` and down.
Up: create `cursor` table: `id INT PRIMARY KEY DEFAULT 1`,
`last_ledger BIGINT NOT NULL DEFAULT 0`,
`updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()`.
Insert the single row: `INSERT INTO cursor (id) VALUES (1)`.
Down: drop table.

**Files:** `migrations/003_create_cursor.up.sql`,
`migrations/003_create_cursor.down.sql`
**Done when:** Migration applies, inserts the row, rolls back.

---

#### 2.2.4 Implement database connection and migration runner
In `internal/db/db.go`, implement `Connect(databaseURL string) (*pgxpool.Pool, error)` using `pgx/v5/pgxpool`. In `cmd/indexer/main.go`, read `DATABASE_URL` from env, call `Connect`, run `golang-migrate` migrations from `migrations/` on startup.

**Files:** `internal/db/db.go`, `cmd/indexer/main.go`, `go.mod`
**Done when:** Running the binary against a local PostgreSQL
instance applies all three migrations on first start and is
idempotent on subsequent starts.

---

#### 2.2.5 Implement bounty database queries
In `internal/db/bounty.go`, implement:
`InsertBounty(ctx, pool, bounty Bounty) error`,
`UpdateBounty(ctx, pool, bounty Bounty) error`,
`GetBounty(ctx, pool, id uint64) (Bounty, error)`,
`ListBounties(ctx, pool, status *string, limit, offset int) ([]Bounty, int, error)`,
`GetStats(ctx, pool) (Stats, error)`.

**Files:** `internal/db/bounty.go`
**Done when:** Each function has a test using a real test
PostgreSQL database. Tests cover: insert and read-back,
list with status filter, list pagination, stats aggregation.

---

#### 2.2.6 Implement event database queries
In `internal/db/event.go`, implement:
`InsertEvent(ctx, pool, event Event) error`,
`ListEventsByBounty(ctx, pool, bountyID uint64) ([]Event, error)`.

**Files:** `internal/db/event.go`
**Done when:** Tests assert insert and read-back for each
event type.

---

#### 2.2.7 Implement cursor read/write
In `internal/db/cursor.go`, implement:
`GetLastLedger(ctx, pool) (uint64, error)`,
`SetLastLedger(ctx, pool, ledger uint64) error`.
Both operate on the single row in the `cursor` table.

**Files:** `internal/db/cursor.go`
**Done when:** Test asserts: initial read returns 0, write then
read returns the written value.

---

### 2.3 — Stellar client

#### 2.3.1 Implement Soroban RPC client wrapper
In `internal/stellar/client.go`, define a `Client` interface with
method `GetContractEvents(ctx, contractID, fromLedger, toLedger)
([]ContractEvent, error)`. Implement a real `RpcClient` struct
using `github.com/stellar/go` that calls the Soroban RPC
`getEvents` method. Implement a `FakeClient` that returns
pre-built events from a slice — used in ingestion tests.

**Files:** `internal/stellar/client.go`
**Done when:** `FakeClient` compiles and returns events correctly.
`RpcClient` compiles. Integration test against testnet is
optional but noted in a code comment.

---

#### 2.3.2 Implement XDR event decoder
In `internal/stellar/decoder.go`, implement
`DecodeEvent(raw RawEvent) (ContractEvent, error)` that decodes
a raw Stellar RPC event into a typed `ContractEvent` struct with
fields: `BountyID`, `EventType`, `Ledger`, `TxHash`, `Payload`.
Identify event type from the topic symbol (`posted`, `claimed`,
`completed`, `cancelled`). Return an error for unknown types.

**Files:** `internal/stellar/decoder.go`
**Done when:** Unit tests decode pre-built raw event fixtures for
all four event types and assert correct field values. Unknown
event type returns a non-nil error.

---

### 2.4 — Ingestion

#### 2.4.1 Implement `posted` event handler
In `internal/ingest/handlers.go`, implement
`HandlePosted(ctx, db, event ContractEvent) error`. Reads bounty
fields from event payload, calls `db.InsertBounty`, calls
`db.InsertEvent`. Wraps both in a single database transaction.

**Files:** `internal/ingest/handlers.go`
**Done when:** Test with real DB asserts bounty row and event row
are inserted correctly. Test with DB failure asserts neither row
is inserted (transaction rolled back).

---

#### 2.4.2 Implement `claimed` event handler
Implement `HandleClaimed(ctx, db, event ContractEvent) error`.
Updates the bounty: `status = claimed`, `claimant = <address>`.
Inserts event row. Wraps in a transaction.

**Files:** `internal/ingest/handlers.go`
**Done when:** Test asserts bounty status and claimant updated.
Test asserts event row inserted. Rollback on DB failure.

---

#### 2.4.3 Implement `completed` event handler
Implement `HandleCompleted(ctx, db, event ContractEvent) error`.
Updates the bounty: `status = completed`. Inserts event row.
Wraps in a transaction.

**Files:** `internal/ingest/handlers.go`
**Done when:** Test asserts status updated to `completed`. Rollback
on DB failure.

---

#### 2.4.4 Implement `cancelled` event handler
Implement `HandleCancelled(ctx, db, event ContractEvent) error`.
Updates the bounty: `status = cancelled`, `claimant = null`.
Inserts event row. Wraps in a transaction.

**Files:** `internal/ingest/handlers.go`
**Done when:** Test asserts status updated to `cancelled` and
claimant is null. Rollback on DB failure.

---

#### 2.4.5 Implement main ingestion loop
In `internal/ingest/ingest.go`, implement `Run(ctx, db, stellar,
contractID string)`. Loop: read `last_ledger` from cursor, fetch
events from `last_ledger+1` to latest ledger, dispatch each event
to the correct handler, advance cursor inside the same transaction
as the handler. Sleep 5 seconds between polls. On RPC error: log
the error with `last_ledger`, wait 10 seconds, retry. Honour
context cancellation.

**Files:** `internal/ingest/ingest.go`
**Done when:** Test using `FakeClient` with 10 pre-built events
asserts all 10 are processed in order, cursor ends at the correct
ledger, and context cancellation stops the loop cleanly.

---

#### 2.4.6 Test ingestion gap recovery
Write a test where `FakeClient` returns events for ledgers 1–5,
then simulates an RPC error, then returns events for ledgers 6–10.
Assert that all 10 events are processed, cursor ends at ledger 10,
and no events are duplicated or skipped.

**Files:** `internal/ingest/ingest_test.go`
**Done when:** Test passes with zero failures.

---

### 2.5 — REST API

#### 2.5.1 Set up HTTP server
In `internal/api/server.go`, implement `NewServer(db, port string) *http.Server`
using `chi` router. Register a `GET /health` route returning
`{"status":"ok"}`. Wire the server into `cmd/indexer/main.go`
alongside the ingestion loop — both run concurrently using
goroutines with graceful shutdown on SIGINT/SIGTERM.

**Files:** `internal/api/server.go`, `cmd/indexer/main.go`
**Done when:** Running the binary, `curl localhost:8080/health`
returns `{"status":"ok"}` with HTTP 200.

---

#### 2.5.2 Implement `GET /health` with indexer lag
Update `GET /health` to return
`{"status":"ok","last_ledger":<n>,"lag":<n>}` where `lag` is
the difference between the latest ledger on the network and
`last_ledger`. Read `last_ledger` from the cursor table.

**Files:** `internal/api/health.go`
**Done when:** Response includes correct `last_ledger` value from
the DB. Unit test mocks the DB call and asserts JSON shape.

---

#### 2.5.3 Implement `GET /api/v1/bounties`
In `internal/api/bounties.go`, handle `GET /api/v1/bounties`.
Query params: `status` (optional), `limit` (default 20, max 100),
`offset` (default 0). Response: `{"bounties":[...],"total":<n>,"limit":<n>,"offset":<n>}`.
Returns HTTP 400 for invalid query params. Returns HTTP 200 with
empty array if no results.

**Files:** `internal/api/bounties.go`
**Done when:** Handler test asserts: correct response shape,
status filter works, pagination works, invalid params return 400.

---

#### 2.5.4 Implement `GET /api/v1/bounties/:id`
Handle `GET /api/v1/bounties/:id`. Returns the single bounty as
JSON. Returns HTTP 404 if not found. Returns HTTP 400 if ID is
not a valid integer.

**Files:** `internal/api/bounties.go`
**Done when:** Tests assert: correct bounty returned, 404 for
missing ID, 400 for non-integer ID.

---

#### 2.5.5 Implement `GET /api/v1/bounties/:id/events`
Handle `GET /api/v1/bounties/:id/events`. Returns all events for
a bounty as `{"events":[...]}`. Returns HTTP 404 if the bounty
does not exist. Returns HTTP 200 with empty array if bounty exists
but has no events.

**Files:** `internal/api/bounties.go`
**Done when:** Tests assert correct event list returned, 404 for
missing bounty, empty array for bounty with no events.

---

#### 2.5.6 Implement `GET /api/v1/stats`
Handle `GET /api/v1/stats`. Returns:
`{"total":<n>,"open":<n>,"claimed":<n>,"completed":<n>,"cancelled":<n>,"total_usdc":"<string>"}`.
`total_usdc` is the sum of all completed bounty amounts as a
decimal string.

**Files:** `internal/api/bounties.go`
**Done when:** Test asserts correct counts and total_usdc after
inserting test bounties with known amounts.

---

#### 2.5.7 Add CORS middleware
Add CORS middleware to the chi router allowing requests from
`NEXT_PUBLIC_APP_URL` env var and `http://localhost:3000`.
Set allowed methods: `GET, OPTIONS`. Set allowed headers:
`Content-Type`.

**Files:** `internal/api/server.go`
**Done when:** `OPTIONS` preflight request from a different origin
returns correct CORS headers. Test asserts headers present.

---

### 2.6 — Configuration and deployment

#### 2.6.1 Implement environment config loader
In `internal/config/config.go`, define a `Config` struct with
fields: `DatabaseURL`, `StellarRPCURL`, `ContractID`,
`NetworkPassphrase`, `Port`, `StartLedger`. Implement
`Load() (Config, error)` that reads from environment variables
and returns an error listing all missing required variables.

**Files:** `internal/config/config.go`
**Done when:** Test asserts: all required vars present returns
valid config, any missing required var returns descriptive error
naming the missing variable.

---

#### 2.6.2 Write `README.md`
Cover: what the indexer does, public API URL (once deployed),
local setup (clone, set env vars, run migrations, start server),
all environment variables with descriptions, API endpoint
reference (brief), CI badge.

**Files:** `README.md`
**Done when:** A developer can run the indexer locally using
only the README.

---

#### 2.6.3 Write `docs/api.md`
Full REST API reference: every endpoint, query parameters,
response shape with field descriptions, error responses with
HTTP codes, and a `curl` example for each endpoint.

**Files:** `docs/api.md`
**Done when:** Every endpoint is documented with at least one
`curl` example showing a real response shape.

---

#### 2.6.4 Deploy to Render
Create a `render.yaml` service definition for the indexer web
service and a Render PostgreSQL instance. Document the full
deployment steps in `docs/deployment.md`: create Render account,
connect GitHub repo, set environment variables, trigger deploy,
run migrations, verify `/health`.

**Files:** `render.yaml`, `docs/deployment.md`
**Done when:** Service is live at a public Render URL.
`/health` returns 200 with a valid `last_ledger`.

---

## Phase 3 — Web App (`soroquest-app`)

### 3.1 — Repository setup

#### 3.1.1 Initialise Next.js project
Run `npx create-next-app@latest soroquest-app --typescript
--tailwind --eslint --app --no-src-dir`. Verify `npm run build`
exits 0. Remove boilerplate content from `app/page.tsx`.

**Files:** All Next.js scaffold files
**Done when:** `npm run build` exits 0 on clean checkout.

---

#### 3.1.2 Add Apache-2.0 licence, `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`
Same requirements as previous repos, adapted for TypeScript/Next.js.
Prerequisites: Node.js 20+, npm. Build: `npm run build`.
Lint: `npm run lint`.

**Files:** `LICENSE`, `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`
**Done when:** All four files exist and are correct.

---

#### 3.1.3 Add GitHub issue templates and PR template
Same as task 1.1.6 and 1.1.7 adapted for TypeScript. PR checklist:
`npm run build` passes, `npm run lint` clean, `Closes #<id>` present.

**Files:** `.github/ISSUE_TEMPLATE/`, `.github/pull_request_template.md`
**Done when:** Templates appear in GitHub UI.

---

#### 3.1.4 Set up GitHub Actions CI
Create `.github/workflows/ci.yml`. Steps: checkout, install
Node 20, `npm ci`, `npm run lint`, `npm run build`. Add badge
to `README.md`.

**Files:** `.github/workflows/ci.yml`, `README.md`
**Done when:** CI passes on clean push. Badge shows green.

---

#### 3.1.5 Configure environment variables
Create `.env.local.example` with all required variables:
`NEXT_PUBLIC_TESTNET_CONTRACT_ID`, `NEXT_PUBLIC_MAINNET_CONTRACT_ID`,
`NEXT_PUBLIC_MAINNET_RPC_URL`, `NEXT_PUBLIC_INDEXER_URL`.
Create `.env.local` with real testnet contract ID and indexer URL.
Add `.env.local` to `.gitignore`.

**Files:** `.env.local.example`, `.gitignore`
**Done when:** `.env.local` is gitignored. `.env.local.example`
is committed with placeholder values and comments explaining each.

---

### 3.2 — Core library layer

#### 3.2.1 Implement USDC formatting utilities
Create `lib/usdc.ts`. Export `toDisplayAmount(raw: bigint): string`
— converts raw i128 to a display string with 7 decimal places
(e.g. `10000000n → "1.0000000"`). Export
`toContractAmount(display: string): bigint` — converts display
string to raw i128. Export `formatUSDC(raw: bigint): string`
— formats for display with trailing zeros trimmed
(`10000000n → "1.00 USDC"`).

**Files:** `lib/usdc.ts`
**Done when:** Unit tests (Jest or Vitest) assert:
`toDisplayAmount(10000000n) === "1.0000000"`,
`toContractAmount("1.00") === 10000000n`,
`formatUSDC(10000000n) === "1.00 USDC"`,
`formatUSDC(15500000n) === "1.55 USDC"`,
`formatUSDC(100n) === "0.0000100 USDC"`.

---

#### 3.2.2 Implement `lib/constants.ts`
Export `CONTRACT_ID: Record<"testnet"|"mainnet", string>`,
`RPC_URL: Record<"testnet"|"mainnet", string>`,
`NETWORK_PASSPHRASE: Record<"testnet"|"mainnet", string>`,
`INDEXER_URL: string`, `STELLAR_EXPERT_URL: Record<"testnet"|"mainnet", string>`.
All values read from `process.env.NEXT_PUBLIC_*` variables.
Throw a descriptive error at import time if any required variable
is missing.

**Files:** `lib/constants.ts`
**Done when:** Build fails with a clear error if a required env
var is missing. Unit test with all vars set asserts correct values.

---

#### 3.2.3 Implement Freighter wallet helpers
Create `lib/freighter.ts`. Export:
`isFreighterInstalled(): Promise<boolean>`,
`isConnected(): Promise<boolean>`,
`getPublicKey(): Promise<string>`,
`getNetwork(): Promise<"testnet"|"mainnet">`,
`signTransaction(xdr: string, network: string): Promise<string>`.
All functions handle the case where Freighter is not installed
by returning `false`, `null`, or throwing a typed
`FreighterNotInstalledError`. Never import `@stellar/freighter-api`
outside this file.

**Files:** `lib/freighter.ts`
**Done when:** TypeScript compiles with no errors. Unit test
with a mocked freighter-api module asserts each function's
not-installed behaviour.

---

#### 3.2.4 Implement indexer API client
Create `lib/indexer.ts`. Export:
`fetchBounties(params: { status?: string, limit?: number, offset?: number }): Promise<BountyListResponse>`,
`fetchBounty(id: bigint): Promise<Bounty>`,
`fetchBountyEvents(id: bigint): Promise<Event[]>`,
`fetchStats(): Promise<Stats>`.
All functions call `INDEXER_URL` endpoints. On non-200 response,
throw a typed `IndexerError` with the status code and message.
Never use `any` types.

**Files:** `lib/indexer.ts`
**Done when:** TypeScript compiles with no errors. Unit tests
with mocked `fetch` assert: correct URL constructed, response
parsed correctly, non-200 throws `IndexerError`.

---

#### 3.2.5 Implement contract interaction layer
Create `lib/contract.ts`. Export read functions that call
Soroban RPC directly (fallback when indexer is unavailable):
`getContractBountyCount(network): Promise<bigint>`.
Export write functions that build, simulate, and return an
unsigned XDR for signing:
`buildPostBountyTx(params, network): Promise<string>`,
`buildClaimBountyTx(bountyId, network): Promise<string>`,
`buildApproveCompletionTx(bountyId, network): Promise<string>`,
`buildCancelBountyTx(bountyId, network): Promise<string>`.
Never import `@stellar/stellar-sdk` outside this file.

**Files:** `lib/contract.ts`
**Done when:** TypeScript compiles. Each build function returns
a non-empty XDR string when called with valid parameters against
testnet. Test asserts non-empty string returned.

---

### 3.3 — Shared types

#### 3.3.1 Define shared TypeScript types
Create `types/index.ts`. Export:
`Bounty`, `BountyStatus`, `Event`, `Stats`,
`BountyListResponse`, `Network`, `TxState`,
`FreighterNotInstalledError`, `IndexerError`. All types must be
used by at least one other file — no unused type definitions.

**Files:** `types/index.ts`
**Done when:** TypeScript compiles with no errors. No `any` types.

---

### 3.4 — React hooks

#### 3.4.1 Implement `useWallet` hook
Create `hooks/useWallet.ts`. Returns: `{ publicKey, network,
isConnected, isLoading, error, connect, disconnect }`.
On connect: calls `lib/freighter.ts` helpers, stores `publicKey`
and `network` in React state and `localStorage`. On disconnect:
clears state and storage. On mount: reads from `localStorage`
and re-validates with Freighter.

**Files:** `hooks/useWallet.ts`
**Done when:** TypeScript compiles. Unit test with mocked
freighter lib asserts: connect sets publicKey, disconnect clears
it, not-installed returns correct error.

---

#### 3.4.2 Implement `useBounties` hook
Create `hooks/useBounties.ts`. Accepts `{ status?, limit?, offset? }`.
Fetches from indexer on mount and on param change. Returns
`{ bounties, total, isLoading, error, refetch }`. On error,
falls back to direct RPC read for `getBountyCount` only.

**Files:** `hooks/useBounties.ts`
**Done when:** TypeScript compiles. Unit test with mocked indexer
asserts: loading state set on fetch, bounties populated on success,
error set on failure, refetch triggers a new fetch.

---

#### 3.4.3 Implement `useBounty` hook
Create `hooks/useBounty.ts`. Accepts `id: bigint`. Fetches single
bounty from indexer. Returns `{ bounty, isLoading, error, refetch }`.

**Files:** `hooks/useBounty.ts`
**Done when:** TypeScript compiles. Unit test asserts loading,
success, and error states.

---

### 3.5 — UI components

#### 3.5.1 Build `WalletConnect` component
Create `components/nav/WalletConnect.tsx`. Shows "Connect Wallet"
button when disconnected. Shows truncated address (first 4 + last 4
chars) and "Disconnect" when connected. Shows "Install Freighter"
link if Freighter is not installed. Uses `useWallet` hook.
Mobile responsive.

**Files:** `components/nav/WalletConnect.tsx`
**Done when:** Component renders in all three states with no
TypeScript errors.

---

#### 3.5.2 Build `NetworkBadge` component
Create `components/ui/NetworkBadge.tsx`. Displays current network
(`testnet` or `mainnet`) as a coloured badge — yellow for testnet,
green for mainnet. Reads from `useWallet` hook. Renders nothing
when wallet is not connected.

**Files:** `components/ui/NetworkBadge.tsx`
**Done when:** Component renders correct badge for each network
state. TypeScript compiles.

---

#### 3.5.3 Build `StatusBadge` component
Create `components/bounty/StatusBadge.tsx`. Renders a coloured
badge for each `BountyStatus`: Open (blue), Claimed (yellow),
Completed (green), Cancelled (grey). Accepts `status: BountyStatus`
as a prop. No `any` types.

**Files:** `components/bounty/StatusBadge.tsx`
**Done when:** All four statuses render with correct colour and
label. TypeScript compiles.

---

#### 3.5.4 Build `BountyCard` component
Create `components/bounty/BountyCard.tsx`. Displays: title,
USDC amount (via `formatUSDC`), status badge, owner address
(truncated), time posted (ledger sequence formatted as relative
time where possible). Clicking the card navigates to
`/bounty/[id]`. Accepts `bounty: Bounty` prop.

**Files:** `components/bounty/BountyCard.tsx`
**Done when:** Component renders with a test `Bounty` value.
Amount displays via `formatUSDC`. TypeScript compiles.

---

#### 3.5.5 Build `TransactionState` component
Create `components/ui/TransactionState.tsx`. Renders each tx
state: Idle (nothing), Signing (spinner + "Waiting for wallet
signature..."), Pending (spinner + "Transaction submitted,
waiting for confirmation..."), Confirmed (checkmark + "Done"),
Failed (error icon + specific error message). Accepts
`state: TxState` and `error?: string` props.

**Files:** `components/ui/TransactionState.tsx`
**Done when:** All five states render correctly. TypeScript
compiles.

---

#### 3.5.6 Build `Navbar` component
Create `components/nav/Navbar.tsx`. Contains: SoroQuest logo/name
linking to `/`, nav links to `/bounties` and `/post`,
`NetworkBadge`, `WalletConnect`. Fully responsive — collapses to
a hamburger menu on mobile.

**Files:** `components/nav/Navbar.tsx`
**Done when:** Renders on desktop and mobile. All links navigate
correctly. TypeScript compiles.

---

### 3.6 — Pages

#### 3.6.1 Build landing page (`app/page.tsx`)
Content: headline, one-sentence description, live bounty count
fetched from indexer, two CTAs (Browse Bounties → `/bounties`,
Post a Bounty → `/post`), three-step how-it-works section (Post,
Claim, Get Paid), links to contract on Stellar Expert for both
networks. Works without a wallet connected.

**Files:** `app/page.tsx`
**Done when:** Page builds. Bounty count loads from indexer.
Page is readable on mobile. TypeScript compiles.

---

#### 3.6.2 Build bounties list page (`app/bounties/page.tsx`)
Displays all bounties using `useBounties` hook and `BountyCard`
component. Status filter tabs: All, Open, Claimed, Completed,
Cancelled. Load more button that appends next page. Loading
skeleton while fetching. Empty state message when no bounties
match filter. Works without a wallet connected.

**Files:** `app/bounties/page.tsx`
**Done when:** Page builds. Filter tabs update list. Load more
works. Empty state shows. TypeScript compiles.

---

#### 3.6.3 Build bounty detail page (`app/bounty/[id]/page.tsx`)
Displays full bounty: title, description, USDC amount, owner
address (linked to Stellar Expert), status badge, claimant address
if claimed, claim deadline if set, link to verify on Stellar Expert.
Conditional action buttons: Claim (Open + not owner + wallet
connected), Approve Completion (Claimed + is owner), Cancel (Open
or deadline-passed Claimed + is owner). Shows "Connect wallet" in
place of action buttons when disconnected.

**Files:** `app/bounty/[id]/page.tsx`
**Done when:** Page builds. All conditional states render correctly.
TypeScript compiles.

---

#### 3.6.4 Implement Claim action on bounty detail page
Wire the Claim button to `buildClaimBountyTx`, sign with
`signTransaction`, submit to network, show `TransactionState`
throughout. On confirmed: call `useBounty.refetch()` to update
the displayed status. On error: show specific error message from
`CONTRACT_ERRORS` map.

**Files:** `app/bounty/[id]/page.tsx`
**Done when:** Full claim flow works on testnet with a real
Freighter wallet. All transaction states display correctly.

---

#### 3.6.5 Implement Approve Completion action on bounty detail page
Wire the Approve button to `buildApproveCompletionTx`, sign,
submit, show `TransactionState`. On confirmed: refetch bounty.
On error: specific error message.

**Files:** `app/bounty/[id]/page.tsx`
**Done when:** Full approve flow works on testnet. USDC transfers
to claimant confirmed on Stellar Expert.

---

#### 3.6.6 Implement Cancel action on bounty detail page
Wire the Cancel button. Show a confirmation dialog before
proceeding. Build tx, sign, submit, show state. On confirmed:
refetch bounty. On error: specific message.

**Files:** `app/bounty/[id]/page.tsx`
**Done when:** Confirmation dialog shown. Full cancel flow works
on testnet. USDC returned to owner confirmed on Stellar Expert.

---

#### 3.6.7 Build post bounty page (`app/post/page.tsx`)
Form fields: title (required), description (required), USDC amount
(required, numeric, > 0), claim deadline (optional, date picker).
Show connected wallet's USDC balance. On submit: validate form,
call `buildPostBountyTx`, sign, submit, show `TransactionState`.
On confirmed: redirect to the new bounty's detail page. Wallet
required — show "Connect wallet" prompt if disconnected.

**Files:** `app/post/page.tsx`
**Done when:** Form validates client-side. Full post flow works
on testnet. After confirmation, new bounty appears at `/bounty/[id]`.

---

#### 3.6.8 Build dashboard page (`app/dashboard/page.tsx`)
Two sections: My Posted Bounties (bounties where owner === connected
wallet), My Claimed Bounties (bounties where claimant === connected
wallet). Each bounty shows status and available actions (same rules
as detail page). Wallet required — redirect to `/` if disconnected.
Fetches from indexer using status and owner/claimant filters.

**Files:** `app/dashboard/page.tsx`
**Done when:** Both sections populate correctly for a wallet that
has posted and claimed bounties on testnet. TypeScript compiles.

---

### 3.7 — Deployment

#### 3.7.1 Deploy to Vercel
Connect `soroquest-app` GitHub repo to a Vercel project. Set all
`NEXT_PUBLIC_*` environment variables in Vercel dashboard.
Trigger first deploy. Verify the live URL loads the landing page
and bounty count is populated from the indexer.

**Files:** none (Vercel config is in the dashboard)
**Done when:** App is live at a public Vercel URL. Landing page
loads. Bounty count is non-zero (or zero if no bounties posted
yet, but the request completes without error).

---

#### 3.7.2 Add live URL to repo About section
On the `soroquest-app` GitHub repo page, click the ⚙️ gear next
to About. Paste the Vercel URL into the Website field. Add a
one-sentence description.

**Done when:** Live URL appears in the repo's About section.

---

## Phase 4 — Repo Polish (All Three Repos)

### 4.1 — GitHub settings

#### 4.1.1 Enable Discussions on all three repos
In each repo: Settings → General → Features → tick Discussions.

**Done when:** Discussions tab visible on all three repos.

---

#### 4.1.2 Enable private vulnerability reporting on all three repos
In each repo: Settings → Code security and analysis →
Private vulnerability reporting → Enable. Only works on
public repos.

**Done when:** Security tab shows advisory reporting enabled
on all three repos.

---

#### 4.1.3 Add topics to all three repos
All three: `stellar`, `soroban`, `drips-wave`, `bounty-platform`.
`soroquest-contracts`: add `rust`, `smart-contract`.
`soroquest-indexer`: add `golang`, `event-indexer`, `postgresql`.
`soroquest-app`: add `typescript`, `nextjs`, `web3`.

**Done when:** Topics visible on each repo's main page.

---

#### 4.1.4 Enable branch protection on `main` for all three repos
In each repo: Settings → Branches → Add rule → Branch name
pattern: `main`. Tick: Require status checks to pass before
merging. Select the CI job name. Tick: Require branches to be
up to date before merging.

**Done when:** A PR cannot be merged to main without green CI
on all three repos.

---

### 4.2 — Org profile

#### 4.2.1 Create org profile README
Create the `soroquest-hq/.github` repo (public). Add
`profile/README.md` describing: what SoroQuest is, the three
tools and their roles, live app URL, testnet and mainnet contract
addresses, contributing section pointing to each repo's
`CONTRIBUTING.md`.

**Files:** `profile/README.md` in the `.github` repo
**Done when:** README renders on `github.com/soroquest-hq`.

---

## Phase 5 — Wave Issues

### 5.1 — Issue writing

#### 5.1.1 Write issues for `soroquest-contracts`
Write one issue per task from Phase 1 that is not yet complete
at submission time. Each issue must follow the Drips Wave issue
format: Description, Context (with file paths), Requirements,
Suggested execution, Definition of done, How I'll review this,
Guidelines (assignment required, `Closes #<id>` in PR).

**Done when:** At least 5 issues open on `soroquest-contracts`,
each tagged with correct complexity label.

---

#### 5.1.2 Write issues for `soroquest-indexer`
Write one issue per incomplete Phase 2 task. Same format and
requirements as above.

**Done when:** At least 5 issues open on `soroquest-indexer`.

---

#### 5.1.3 Write issues for `soroquest-app`
Write one issue per incomplete Phase 3 task. Same format
and requirements.

**Done when:** At least 5 issues open on `soroquest-app`.

---

#### 5.1.4 Add complexity labels to all repos
Create three labels on each repo: `complexity: trivial` (grey),
`complexity: medium` (yellow), `complexity: high` (orange).
Apply the correct label to every open issue.

**Done when:** Every open issue has exactly one complexity label.

---

## Phase 6 — Wave Application

#### 6.1 Final pre-submission checklist
Verify all of the following before submitting:
- [ ] `soroquest-contracts` CI green, contract deployed on testnet and mainnet
- [ ] `soroquest-indexer` deployed on Render, `/health` returns 200
- [ ] `soroquest-app` deployed on Vercel, full bounty lifecycle works on testnet
- [ ] All three repos have green CI badges in their READMEs
- [ ] All three repos have Discussions enabled
- [ ] All three repos have branch protection on main
- [ ] Org profile README published at `github.com/soroquest-hq`
- [ ] At least 5 issues open on each repo with complexity labels

---

#### 6.2 Submit application
1. Sign in to drips.network/wave with GitHub
2. Maintainers → Orgs and Repos
3. Install Drips Wave GitHub App on `soroquest-hq`
4. Sync all three repos
5. Apply to Stellar Wave Program
6. Complete all application questions

**Done when:** Application submitted. Confirmation shown in
Orgs and Repos dashboard.

---

## Phase 7 — First Wave Cycle

#### 7.1 Publish issues to Wave Program
Once approved, go to Maintainers → Issues dashboard. Select
issues to publish. Assign complexity levels. Publish to the
active Wave.

**Done when:** Issues visible in the Wave Program feed.

---

#### 7.2 Review and assign contributors
Review incoming applications within 24 hours. Check Code Metrics
scorecard. Assign via dashboard or GitHub.

**Done when:** Every open issue has an assigned contributor.

---

#### 7.3 Review PRs and merge
Review submitted PRs within 48 hours. Merge completed work.
Leave a review within 14 days of issue close.

**Done when:** At least one issue resolved and merged.
Contributor review submitted.

---

## Phase 8 — Multi-Cycle Contributor Expansion Roadmap (125+ Scoped Tasks)

The following sections define the backlog of 125+ open-source tasks to be published progressively across Drips Wave sprint cycles. Each task is self-contained and tagged by target repository and complexity.

---

### 8.1 Smart Contracts (`soroquest-contracts`) — 40 Contributor Tasks

#### Epic: Milestone & Staged Payouts
- **8.1.1** `[complexity: medium]` Define `Milestone` struct (`id`, `description`, `percentage`, `approved`, `released`) in `storage.rs`.
- **8.1.2** `[complexity: high]` Implement `post_bounty_milestones` entrypoint allowing funders to allocate percentage tranches.
- **8.1.3** `[complexity: medium]` Implement `approve_milestone` entrypoint releasing fractional escrow to claimant.
- **8.1.4** `[complexity: medium]` Emit `MilestoneApproved` and `MilestoneReleased` Soroban events in `events.rs`.
- **8.1.5** `[complexity: medium]` Unit tests verifying proportional token balance transfers across multiple milestones.

#### Epic: Dispute & Arbitration Mechanics
- **8.1.6** `[complexity: medium]` Add `DataKey::Arbiter` and admin configuration entrypoint.
- **8.1.7** `[complexity: high]` Implement `raise_dispute` entrypoint callable by claimant or owner when deadline passes.
- **8.1.8** `[complexity: high]` Implement `resolve_dispute(split_funder, split_claimant)` arbiter judgment function.
- **8.1.9** `[complexity: medium]` Add dispute timeout grace period preventing permanent fund freeze.
- **8.1.10** `[complexity: medium]` Invariant property tests verifying total balance conservation during split dispute resolution.

#### Epic: Contributor Staking & Anti-Ghosting
- **8.1.11** `[complexity: medium]` Add `stake_required` field to `Bounty` struct.
- **8.1.12** `[complexity: high]` Require claimant token transfer into contract upon invoking `claim_bounty`.
- **8.1.13** `[complexity: medium]` Refund staked deposit to claimant upon `approve_completion`.
- **8.1.14** `[complexity: high]` Slash claimant stake and transfer to owner if claim deadline expires without submission.
- **8.1.15** `[complexity: medium]` Negative test cases for insufficient stake balance.

#### Epic: Multi-Token & SEP-41 Compatibility
- **8.1.16** `[complexity: trivial]` Add `is_token_supported` query function checking contract whitelist.
- **8.1.17** `[complexity: medium]` Implement admin entrypoints `add_whitelisted_token` and `remove_whitelisted_token`.
- **8.1.18** `[complexity: medium]` Validate token contract interface compliance during `post_bounty`.
- **8.1.19** `[complexity: trivial]` Unit tests for posting bounties with non-USDC Stellar assets.
- **8.1.20** `[complexity: medium]` Event emission for new token whitelist additions.

#### Epic: Protocol Fee & Treasury Splitting
- **8.1.21** `[complexity: trivial]` Add `DataKey::FeeBasisPoints` (default 0, max 250 bps / 2.5%).
- **8.1.22** `[complexity: medium]` Add `DataKey::TreasuryAddress` with admin setter and timelock.
- **8.1.23** `[complexity: medium]` Calculate protocol fee deduction during `approve_completion`.
- **8.1.24** `[complexity: medium]` Transfer fee tranche to treasury and remainder to contributor in atomic operation.
- **8.1.25** `[complexity: medium]` Invariant tests verifying `claimant_payout + treasury_fee == total_escrow`.

#### Epic: Proof-of-Completion On-Chain Credentials
- **8.1.26** `[complexity: high]` Mint non-transferable proof-of-work badge (Soulbound token) upon bounty completion.
- **8.1.27** `[complexity: medium]` Store cumulative completed bounty count per contributor address.
- **8.1.28** `[complexity: trivial]` Query endpoint `get_contributor_stats(address)` returning lifetime earnings and tasks.
- **8.1.29** `[complexity: medium]` Emit `CredentialMinted(contributor, bounty_id, tier)` event.
- **8.1.30** `[complexity: medium]` Unit tests for credential counter increments.

#### Epic: Gas Optimization & Security Hardening
- **8.1.31** `[complexity: medium]` Implement automatic TTL extension (`env.storage().instance().extend_ttl(...)`) on all writes.
- **8.1.32** `[complexity: high]` Automated fuzz testing harness with `proptest` for random call sequences.
- **8.1.33** `[complexity: medium]` Storage footprint audit verifying persistent storage keys remain within 1KB bounds.
- **8.1.34** `[complexity: trivial]` Code comments and Rust docstrings for all public entrypoints adhering to Rustdoc conventions.
- **8.1.35** `[complexity: medium]` Gas benchmark comparison between single-owner and multi-sig claim calls.
- **8.1.36** `[complexity: high]` Implement upgradeable contract pattern using `env.deployer().update_current_contract_wasm(...)`.
- **8.1.37** `[complexity: medium]` Emergency pause mechanism callable only by multi-sig admin.
- **8.1.38** `[complexity: trivial]` Add lint rule enforcing `deny(clippy::unwrap_used)` across all contract crates.
- **8.1.39** `[complexity: medium]` Test suite validating state recovery after ledger archival simulation.
- **8.1.40** `[complexity: medium]` Formal specification document mapping contract invariants to TLA+ or state machine proofs.

---

### 8.2 Event Indexer (`soroquest-indexer`) — 40 Contributor Tasks

#### Epic: Webhooks & Social Notification Bot
- **8.2.1** `[complexity: medium]` Create `webhooks` table schema (`url`, `secret`, `events`, `status`, `created_at`).
- **8.2.2** `[complexity: high]` Implement HMAC-SHA256 signature generation for outgoing webhook payloads.
- **8.2.3** `[complexity: medium]` Worker loop dispatching webhooks with exponential backoff retry on HTTP 5xx errors.
- **8.2.4** `[complexity: medium]` REST endpoints `POST /api/webhooks` and `DELETE /api/webhooks/{id}`.
- **8.2.5** `[complexity: medium]` Telegram notification worker publishing new open bounties to Telegram channels.
- **8.2.6** `[complexity: medium]` Discord bot integration formatting embedded Discord messages on `bounty.created`.
- **8.2.7** `[complexity: high]` GitHub Bot listening to GitHub comments (`/bounty $100`) and verifying on-chain escrow.
- **8.2.8** `[complexity: trivial]` Unit tests for webhook payload serialization and signature verification.

#### Epic: Real-Time Streaming & Live Updates
- **8.2.9** `[complexity: high]` Implement Server-Sent Events (SSE) endpoint `GET /api/events/stream` for live frontend updates.
- **8.2.10** `[complexity: high]` WebSocket server supporting pub/sub channels (`channel:bounty_{id}`, `channel:all`).
- **8.2.11** `[complexity: medium]` Heartbeat ping/pong mechanism for active client WebSocket connections.
- **8.2.12** `[complexity: medium]` In-memory fan-out broadcast broker distributing new ledger events to subscribers.
- **8.2.13** `[complexity: trivial]` Unit test suite verifying SSE client disconnection cleanup.

#### Epic: Full-Text Search & Analytics
- **8.2.14** `[complexity: medium]` Add PostgreSQL `tsvector` column and GIN index for bounty titles and descriptions.
- **8.2.15** `[complexity: medium]` Implement search query parameter `GET /api/bounties?q={query}` using `to_tsquery`.
- **8.2.16** `[complexity: trivial]` Support fuzzy matching with `pg_trgm` extension for typo tolerance.
- **8.2.17** `[complexity: medium]` Leaderboard endpoint `GET /api/stats/leaderboard` ranking contributors by volume.
- **8.2.18** `[complexity: medium]` Volume endpoint `GET /api/stats/volume` aggregating daily/weekly USDC escrow flows.
- **8.2.19** `[complexity: trivial]` Average completion duration query `GET /api/stats/metrics`.
- **8.2.20** `[complexity: medium]` Filter bounties by reward range `min_amount` and `max_amount`.

#### Epic: Reliability, DevOps & Observability
- **8.2.21** `[complexity: trivial]` Add Prometheus metrics exporter endpoint `GET /metrics` using `client_golang`.
- **8.2.22** `[complexity: trivial]` Instrument metric `soroquest_ingestion_ledger_height` (Gauge).
- **8.2.23** `[complexity: trivial]` Instrument metric `soroquest_bounty_events_total` by event type (Counter).
- **8.2.24** `[complexity: trivial]` Instrument metric `soroquest_rpc_request_duration_seconds` (Histogram).
- **8.2.25** `[complexity: medium]` Redis caching layer for `/api/bounties` queries with 10-second TTL.
- **8.2.26** `[complexity: medium]` Redis cache invalidation triggered immediately upon ingesting new contract event.
- **8.2.27** `[complexity: high]` CLI tool `cmd/replay` allowing maintainers to re-index ledgers between sequence X and Y.
- **8.2.28** `[complexity: medium]` PostgreSQL advisory lock ensuring single-active ingestion replica across clusters.
- **8.2.29** `[complexity: medium]` Graceful shutdown handler waiting for pending database transactions before exit.
- **8.2.30** `[complexity: trivial]` Health check endpoint returning detailed JSON diagnostics for database pool connection count.

#### Epic: Multi-Contract & Ecosystem Aggregation
- **8.2.31** `[complexity: high]` Extend indexer configuration to accept a list of contract IDs (`CONTRACT_IDS=id1,id2`).
- **8.2.32** `[complexity: medium]` Add `contract_address` column to `bounties` and `events` tables.
- **8.2.33** `[complexity: medium]` Fetch Stellar Horizon asset pricing to compute real-time USD equivalent valuations.
- **8.2.34** `[complexity: trivial]` Rate limiting middleware restricting clients to 60 req/min with `X-RateLimit` headers.
- **8.2.35** `[complexity: medium]` Database read replica connection support for high-traffic query separation.
- **8.2.36** `[complexity: trivial]` Add structured JSON log output with correlation IDs.
- **8.2.37** `[complexity: medium]` Integration test using `testcontainers-go` to spin up real PostgreSQL during CI.
- **8.2.38** `[complexity: trivial]` Makefile target `make seed-stress` creating 1,000 mock bounties for performance tests.
- **8.2.39** `[complexity: medium]` API schema validation middleware checking query parameters against OpenAPI definitions.
- **8.2.40** `[complexity: trivial]` Postman / Bruno collection export in `docs/postman_collection.json`.

---

### 8.3 Web Application (`soroquest-app`) — 45 Contributor Tasks

#### Epic: Contributor Profiles & Reputation UI
- **8.3.1** `[complexity: medium]` Create dynamic route `/profile/[address]` displaying user public key and avatar.
- **8.3.2** `[complexity: medium]` Implement multihash Identicon or Jazzicon avatar generated from Stellar public key.
- **8.3.3** `[complexity: trivial]` User profile tab: "Claimed Bounties" with live progress status.
- **8.3.4** `[complexity: trivial]` User profile tab: "Created Bounties" with funder management options.
- **8.3.5** `[complexity: medium]` User profile stat cards: Total Earned (USDC), Completed Bounties, On-time Success Rate.
- **8.3.6** `[complexity: trivial]` Shareable profile card component with Twitter/X and Farcaster intent links.
- **8.3.7** `[complexity: medium]` Unit tests for Profile route components and stat calculations.

#### Epic: Rich Text Markdown Editor & Work Submission
- **8.3.8** `[complexity: medium]` Integrate `@uiw/react-md-editor` or `react-markdown` on `/post` bounty page.
- **8.3.9** `[complexity: trivial]` Live tabbed preview ("Write" vs "Preview") for bounty description input.
- **8.3.10** `[complexity: medium]` Markdown rendering on `/bounty/[id]` supporting code syntax highlighting.
- **8.3.11** `[complexity: medium]` Work submission modal allowing contributors to submit GitHub PR URLs and work summaries.
- **8.3.12** `[complexity: high]` GitHub API integration verifying pull request existence, status (open/merged), and author.
- **8.3.13** `[complexity: trivial]` Input validation preventing empty task descriptions or invalid URLs.

#### Epic: Multi-Wallet Connectivity & Onboarding
- **8.3.14** `[complexity: high]` Integrate `@creit.tech/stellar-wallets-kit` to support Albedo, xBull, Hana, and Rabet.
- **8.3.15** `[complexity: medium]` Wallet modal allowing users to choose their preferred Stellar wallet extension.
- **8.3.16** `[complexity: trivial]` Auto-detection of installed browser extensions with dynamic "Installed" badge.
- **8.3.17** `[complexity: medium]` Remember user's last connected wallet type in `localStorage`.
- **8.3.18** `[complexity: trivial]` Friendly "Install Wallet" walkthrough modal for new Web3 users.

#### Epic: UI/UX Polish, Themes & Accessibility
- **8.3.19** `[complexity: medium]` Dark mode / Light mode toggle using Tailwind CSS dark classes and `next-themes`.
- **8.3.20** `[complexity: trivial]` Persist theme preference in local storage and synchronize with system preference.
- **8.3.21** `[complexity: trivial]` Confetti celebration animation using `canvas-confetti` when a bounty payout is approved.
- **8.3.22** `[complexity: medium]` Mobile responsive bottom navigation bar for quick navigation on mobile screens.
- **8.3.23** `[complexity: medium]` Slide-over filter drawer for mobile viewports.
- **8.3.24** `[complexity: trivial]` Keyboard shortcut `/` to focus the search bar from anywhere in the app.
- **8.3.25** `[complexity: medium]` WCAG 2.1 AA accessibility audit: ARIA labels for icon buttons and screen reader announcements.
- **8.3.26** `[complexity: trivial]` Sound effect feedback on transaction confirmation (user-toggleable in settings).
- **8.3.27** `[complexity: trivial]` Breadcrumbs navigation on bounty detail and profile subpages.

#### Epic: Filtering, Sorting & Watchlist
- **8.3.28** `[complexity: trivial]` Sort dropdown: "Highest Reward", "Lowest Reward", "Newest", "Closing Soon".
- **8.3.29** `[complexity: trivial]` Filter pill: "Expiring Soon" (bounties within 10,000 ledgers of deadline).
- **8.3.30** `[complexity: medium]` Local storage Watchlist / Bookmarking allowing users to star bounties.
- **8.3.31** `[complexity: trivial]` "Watchlist" filter toggle in browse catalog showing only saved bounties.
- **8.3.32** `[complexity: trivial]` Tag filter pills (e.g. `frontend`, `contracts`, `docs`, `bug`, `feature`).

#### Epic: Testing, Tooling & Internationalization (i18n)
- **8.3.33** `[complexity: high]` Playwright automated end-to-end test suite for bounty browsing and filtering.
- **8.3.34** `[complexity: high]` Mock Freighter provider fixture for end-to-end transaction signing tests in CI.
- **8.3.35** `[complexity: medium]` Set up `next-intl` localization framework.
- **8.3.36** `[complexity: medium]` Add Spanish (`es`) translation catalog.
- **8.3.37** `[complexity: medium]` Add Portuguese (`pt`) translation catalog.
- **8.3.38** `[complexity: trivial]` Language switcher dropdown in navbar.
- **8.3.39** `[complexity: medium]` Storybook setup showcasing `BountyCard`, `Badge`, `EmptyState`, and `CopyButton`.
- **8.3.40** `[complexity: trivial]` PWA offline support with custom service worker caching catalog responses.
- **8.3.41** `[complexity: trivial]` Add transaction speed estimator based on recent Stellar ledger close times.
- **8.3.42** `[complexity: medium]` Component unit tests for `BountyForm` with invalid input assertions.
- **8.3.43** `[complexity: trivial]` Add copyable cURL and API query snippets on bounty details page for developers.
- **8.3.44** `[complexity: medium]` Automatic balance check warning users before submission if account lacks fee reserves (XLM) or USDC.
- **8.3.45** `[complexity: trivial]` Add link to Stellar Friendbot testnet faucet when connected balance is zero.

