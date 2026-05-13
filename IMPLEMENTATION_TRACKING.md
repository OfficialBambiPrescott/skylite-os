# Skylite OS — Implementation Roadmap

**Status:** Phase 1 — EdgeFront VM (Starting Week of 2026-05-20)

This file tracks the development progression from specification to executable infrastructure.

---

## Active Development Milestones

### ✅ Phase 1: EdgeFront Bytecode VM (Weeks 1-4)

**Objective:** Working scripting language interpreter with basic operations.

#### Milestone 1.1: Tokenizer ✅ Ready to start
- [ ] Design token type enum (PLUS, MINUS, EQ, STAR, etc.)
- [ ] Implement character → token mapping (base-9 + extended)
- [ ] Handle multi-character operators (== → EQ_VERIFY)
- [ ] Position tracking (line, column) for error reporting
- [ ] 20+ unit tests

**Target:** Complete by 2026-05-23

#### Milestone 1.2: Parser 
- [ ] Design AST node types (BinaryOp, FunctionCall, IfStatement, etc.)
- [ ] Implement recursive descent parser
- [ ] Validate mandatory `=` and `{}` in command chains
- [ ] Error recovery (report multiple errors per pass)
- [ ] 20+ unit tests

**Target:** Complete by 2026-05-30

#### Milestone 1.3: Compiler
- [ ] Design bytecode instruction set (PUSH, VERIFY, NR, GCD, OUTPUT)
- [ ] Lower AST to bytecode
- [ ] Constant folding optimization
- [ ] Dead code elimination
- [ ] 10+ test cases

**Target:** Complete by 2026-06-06

#### Milestone 1.4: Bytecode VM
- [ ] Implement instruction dispatch loop
- [ ] Built-in functions: `NR(K)`, `GCD(a,b)`, `VERIFY(expr)`, `PRINT(val)`
- [ ] Stack-based execution
- [ ] Error handling (division by zero, stack underflow)
- [ ] 25+ test cases

**Target:** Complete by 2026-06-13

#### Milestone 1.5: REPL + Testing
- [ ] Interactive shell with prompt
- [ ] Command history
- [ ] Help system (`:help NR`, etc.)
- [ ] Integration test suite (end-to-end scripts)

**Target:** Complete by 2026-06-16

### ⏳ Phase 2: Cryptography & Storage (Weeks 5-8)

**Objective:** Persistent encrypted document storage with real key derivation.

#### Milestone 2.1: SHA-256 Hashing
- [ ] Correct Windows CNG implementation (`BCryptCreateHash` + `BCryptHashData` + `BCryptFinishHash`)
- [ ] Cross-platform fallback (libsodium)
- [ ] Unit tests with NIST test vectors
- [ ] Benchmark: target < 1ms for 1 MB

#### Milestone 2.2: AES-256-GCM
- [ ] Encrypt/decrypt pipeline
- [ ] Random IV generation per message
- [ ] Authentication tag validation
- [ ] NIST test vectors

#### Milestone 2.3: Key Derivation
- [ ] Correct Newton-Raphson implementation (29 iterations, φ damping)
- [ ] Hardware seed extraction (GCD + TPM integration)
- [ ] SHA-256 expansion to 32-byte key
- [ ] Determinism verification (same input → same output)

#### Milestone 2.4: SQLite Integration
- [ ] Document metadata schema
- [ ] CRUD operations (Create, Read, Update, Delete)
- [ ] Indexed lookups
- [ ] Transaction support
- [ ] Version 1.0 schema

#### Milestone 2.5: Filesystem Replication
- [ ] Chunked blob storage
- [ ] SHA-256 integrity checksums
- [ ] Redundancy (2-3 copies)
- [ ] Background rebalancing

### ⏳ Phase 3: Zero Trust & Governance (Weeks 9-12)

**Objective:** Authentication, authorization, and policy enforcement.

#### Milestone 3.1: Ed25519 Authentication
- [ ] User keypair generation
- [ ] Document signing/verification
- [ ] JWT token creation
- [ ] Session management

#### Milestone 3.2: Capability-Based Access
- [ ] Permission token model
- [ ] HMAC-SHA256 validation
- [ ] Role-based policy evaluation

#### Milestone 3.3: RoyalCodeCheck
- [ ] Transaction log scanning
- [ ] Violation detection rules
- [ ] Audit event creation

#### Milestone 3.4: AI Official Board
- [ ] Member registry with weights
- [ ] Weighted voting consensus
- [ ] Decision logging

### ⏳ Phase 4: UI & Integration (Weeks 13-16)

**Objective:** Windows application with Fluent Design UI.

#### Milestone 4.1: XAML Framework
- [ ] WinUI 3 setup
- [ ] Main window layout
- [ ] Golden Ratio proportions

#### Milestone 4.2: Document Editor
- [ ] Rich text editing
- [ ] Save/open encrypted documents
- [ ] Auto-save

#### Milestone 4.3: Settings & Dashboard
- [ ] Configuration UI
- [ ] Audit log viewer
- [ ] Threat heatmap visualization

### ⏳ Phase 5: Deployment (Weeks 17-20)

**Objective:** Production-ready Windows application.

#### Milestone 5.1: Code Signing
- [ ] EV certificate acquisition
- [ ] Binary signing
- [ ] SmartScreen reputation setup

#### Milestone 5.2: Packaging
- [ ] MSIX creation
- [ ] Auto-update configuration

#### Milestone 5.3: Security Audit
- [ ] Static analysis (Clang-Tidy)
- [ ] Dependency scanning (OWASP)
- [ ] Third-party review

---

## Current Focus: Phase 1 — Week 1

**This Week's Goals:**

1. ✅ Finalize tokenizer specification
2. ✅ Create `src/vm/tokenizer.cpp` skeleton
3. ✅ Write 20 tokenizer tests
4. ⏳ Begin parser design

**Deadline:** Friday 2026-05-23

---

## Repository Structure

```
skylite-os/
├── src/
│   ├── vm/
│   │   ├── tokenizer.cpp         ← CURRENT FOCUS
│   │   ├── tokenizer.h
│   │   ├── parser.cpp
│   │   ├── parser.h
│   │   ├── compiler.cpp
│   │   ├── compiler.h
│   │   ├── vm.cpp
│   │   └── vm.h
│   ├── crypto/
│   ├── storage/
│   ├── auth/
│   ├── governance/
│   └── ui/
├── tests/
│   ├── vm_tests.cpp
│   ├── crypto_tests.cpp
│   └── integration_tests.cpp
├── examples/
│   ├── hello.ef
│   ├── encryption.ef
│   └── governance.ef
├── docs/
├── build/
│   └── CMakeLists.txt
└── README.md
```

---

## Issue Tracking

**Open Issues:**

- [ ] [Phase 1.1] Implement tokenizer with base-9 + extended character support
- [ ] [Phase 1.2] Design and implement parser for #EdgeFront syntax
- [ ] [Phase 1.3] Create bytecode instruction set and compiler
- [ ] [Phase 1.4] Implement Newton-Raphson + GCD built-ins
- [ ] [Phase 2.1] Fix SHA-256 Windows CNG implementation
- [ ] [Phase 2.2] Integrate AES-256-GCM with libsodium
- [ ] [Phase 2.3] Hardware seed extraction (GCD + TPM)
- [ ] [Phase 3.1] Ed25519 authentication layer
- [ ] [Phase 4.1] WinUI 3 application setup
- [ ] [Phase 5.1] Security audit coordination

---

## Branching Strategy

- `main` — Production-ready code (Phase 5 completion)
- `develop` — Integration branch (current development)
- `feature/phase-1-vm` — Feature branch for Phase 1
- `feature/phase-2-crypto` — Feature branch for Phase 2
- `bugfix/*` — Bug fixes with tests

---

## Definition of Done (Per Phase)

**For each phase:**

- ✅ All milestone tests passing
- ✅ Code review complete (2+ reviewers)
- ✅ Documentation updated
- ✅ Performance benchmarks recorded
- ✅ Security audit if cryptography-related
- ✅ No breaking changes (or migration guide provided)

---

## Questions?

Open an issue with the `[ROADMAP]` tag or comment on the relevant phase milestone.

---

**🖤👑 The journey to execution begins.** 💎🛡️
