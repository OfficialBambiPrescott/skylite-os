# Skylite OS / EdgeFront — Production Engineering Roadmap

**Last Updated:** 2026-05-13  
**Status:** Pre-Alpha Specification → MVP Implementation  
**Repository:** OfficialBambiPrescott/skylite-os

---

## Executive Summary

The Skylite OS ecosystem consists of:

1. **#EdgeFront** — Symbolic DSL for verification-first scripting
2. **SovereignCoreOS** — Policy orchestration kernel
3. **Newton-Raphson Key Derivation** — Deterministic cryptographic root
4. **Sacred Geometry UI** — Vesica Piscis / Golden Ratio composition
5. **AuroraHex** — Distributed consensus validator cluster
6. **Zero Trust / Zero Tolerance** — Mandatory verification enforcement

This document defines the **MVP path** from specification to executable infrastructure, identifies critical engineering gaps, and provides concrete implementation priorities.

---

## Part 1: Critical Engineering Issues (Current State)

### ⚠️ Issue 1: SHA-256 Implementation Non-Functional

**Problem:** Current CNG usage is incorrect.

```cpp
BCryptHash(...)  // Does not exist
```

**Solution:** Use proper three-step hashing:

```cpp
BCryptCreateHash() → BCryptHashData() → BCryptFinishHash()
```

**Priority:** CRITICAL (blocks all cryptography)

**Effort:** 2 hours

---

### ⚠️ Issue 2: No Thread Safety

**Problem:** Global mutable state without synchronization.

```cpp
std::unordered_map<int, std::vector<...>> m_storageNodes;  // no mutex
```

**Solution:** Add `std::shared_mutex` for read/write operations.

**Priority:** CRITICAL (data corruption under concurrency)

**Effort:** 4 hours

---

### ⚠️ Issue 3: Storage Is In-Memory Only

**Problem:** No persistence, durability, or replication.

**Solution:** Implement three-tier storage:

- **Metadata layer:** SQLite
- **Blob layer:** Filesystem chunks with SHA-256 checksums
- **Replication:** Background worker + WAL (Write-Ahead Log)

**Priority:** HIGH (required for real usage)

**Effort:** 1 week

---

### ⚠️ Issue 4: "DNA" Naming Is Misleading

**Problem:** Hash-based identity labeled as biological.

**Solution:** Rename to:
- `IdentitySignature`
- `BiometricAnchor`
- `HolographicDocumentID`

**Priority:** MEDIUM (documentation clarity)

**Effort:** 2 hours

---

### ⚠️ Issue 5: Self-Healing Logic Has Recursion Bug

**Problem:** Recovery can return corrupted data.

```cpp
retrieveData(holographicId);  // May return same corrupted record
```

**Solution:** Use exclusion-aware recovery:

```cpp
retrieveHealthyReplica(holographicId, excludeNodeId);
```

**Priority:** HIGH (data integrity)

**Effort:** 3 hours

---

### ⚠️ Issue 6: Zero Trust GCD Check Is Cryptographically Weak

**Problem:** Nearly all random numbers are coprime.

```cpp
gcd(hash(user), hash(resource)) == 1  // ~99.9% pass rate
```

**Solution:** Use real cryptographic mechanisms:

- **Identity:** Ed25519 signatures
- **Session auth:** JWT or signed nonce
- **Access control:** Capability tokens (keep GCD as symbolic supplemental)
- **Integrity:** HMAC-SHA256

**Priority:** CRITICAL (security)

**Effort:** 2 weeks

---

### ⚠️ Issue 7: "R.A.T." Terminology Is Dangerous

**Problem:** R.A.T. = "Remote Access Trojan" (malware term).

**Solution:** Rename to:
- `RelayAccessTopology`
- `SecureRelayNode`
- `TriangulationMesh`

**Priority:** HIGH (operational safety)

**Effort:** 3 hours

---

### ⚠️ Issue 8: No Actual Encryption

**Problem:** References to encrypted storage but no AES-256-GCM implementation.

**Solution:** Implement full pipeline:

- AES-256-GCM with libsodium
- HKDF key derivation
- Random nonce generation
- Authenticated encryption with GCD verification chain

**Priority:** CRITICAL (core feature)

**Effort:** 3 weeks

---

### ⚠️ Issue 9: VM Built-ins Lack Type Validation

**Problem:** Unsafe `std::get<T>()` will crash on type mismatch.

**Solution:** Use `std::holds_alternative<T>()` with error propagation.

**Priority:** MEDIUM (robustness)

**Effort:** 1 day

---

### ⚠️ Issue 10: No Serialization Layer

**Problem:** No versioning, schema, or migration support.

**Solution:** Adopt binary serialization:
- **FlatBuffers** (high-speed VM)
- **Protocol Buffers** (compatibility)
- **CBOR** (compact structured)
- **MessagePack** (simple)

**Priority:** HIGH (long-term maintainability)

**Effort:** 1 week

---

## Part 2: Recommended MVP Stack (Production-Grade)

| Layer        | Technology           | Rationale                            |
| ------------ | -------------------- | ------------------------------------ |
| **VM**       | C++20 / Rust         | High-performance, memory-safe        |
| **UI**       | WinUI 3 + React      | Fluent Design, cross-platform        |
| **Browser**  | WebView2             | Windows-native web integration       |
| **Storage**  | SQLite + filesystem  | Embedded, proven, durable            |
| **Crypto**   | libsodium            | Audited, portable, modern primitives |
| **Network**  | Boost.ASIO or gRPC   | High-performance communication       |
| **Scripting** | EdgeFront bytecode   | Custom deterministic VM              |
| **Docs**     | OpenXML (Word)       | Microsoft ecosystem alignment        |
| **Sync**     | gRPC + Protocol Buf. | Type-safe distributed communication  |
| **Package**  | MSIX                 | Windows AppStore ready               |

---

## Part 3: MVP Implementation Path (Phased)

### Phase 1: Core VM (Weeks 1-4)

**Goal:** Working EdgeFront bytecode interpreter with basic operations.

#### Deliverables

1. **Tokenizer** (`tokenizer.cpp`)
   - Recognize all #EdgeFront symbols: `+`, `-`, `=`, `*`, `/`, `^`, `$`, `<`, `>`, `~`, `:`, `;`, `#`
   - Parse numeric and string literals
   - Handle base-9 numerology (0-9 + AE)

2. **Parser** (`parser.cpp`)
   - Convert token stream to AST
   - Validate syntax rules (every chain must have `=` and `{}`)
   - Error reporting with line/column positions

3. **Bytecode Compiler** (`compiler.cpp`)
   - Lower AST to simple opcodes: `PUSH`, `VERIFY`, `BIND`, `NR`, `GCD`, `OUTPUT`
   - Validate type consistency

4. **VM Interpreter** (`vm.cpp`)
   - Execute bytecode
   - Implement core built-ins:
     - `NR(K)` — Newton-Raphson root finding (29 iterations with φ damping)
     - `GCD(a, b)` — Euclidean algorithm
     - `VERIFY(expr)` — Check condition, abort on false
     - `PRINT(val)` — Output to console
     - `DENY()` — Reject and return to void (AE)

5. **REPL** (`repl.cpp`)
   - Interactive shell for testing
   - Command history
   - Help system

6. **Test Suite** (`tests/`)
   - 50+ unit tests for tokenizer, parser, compiler, VM
   - Integration tests with real #EdgeFront scripts
   - Performance benchmarks

#### Minimal #EdgeFront Test Program

```edgefront
ROOT = NR(528)
CHECK = GCD(ROOT, 17)
IF CHECK == 1
    PRINT("ACCESS_GRANTED")
ELSE
    PRINT("DENIED")
;
```

**Expected output:** `"ACCESS_GRANTED"` (after convergence)

#### Effort

- Tokenizer: 8 hours
- Parser: 12 hours
- Compiler: 8 hours
- VM: 16 hours
- REPL: 4 hours
- Tests: 12 hours
- **Total Phase 1: 60 hours (~2 weeks at full-time)**

---

### Phase 2: Cryptography & Storage (Weeks 5-8)

**Goal:** Persistent, encrypted document storage with authentic key derivation.

#### Deliverables

1. **SHA-256 Hashing** (`crypto/sha256.cpp`)
   - Correct Windows CNG implementation
   - Cross-platform fallback (OpenSSL / libsodium)
   - Unit tests with NIST test vectors

2. **AES-256-GCM Encryption** (`crypto/aes_gcm.cpp`)
   - Full encrypt/decrypt pipeline
   - Random IV generation
   - Authentication tag validation
   - Test vectors from NIST

3. **Key Derivation** (`crypto/key_derivation.cpp`)
   - Newton-Raphson root finding
   - Golden Ratio φ damping
   - SHA-256 expansion to 32-byte key
   - Integration with `NR()` VM built-in

4. **SQLite Storage** (`storage/document_store.cpp`)
   - Create/read/update/delete (CRUD) documents
   - Indexed lookups by document ID and owner
   - Transaction support
   - Schema versioning (Version 1: basic schema)

5. **Filesystem Replication** (`storage/replica_manager.cpp`)
   - Store encrypted blob chunks on disk
   - SHA-256 integrity checksums
   - Redundancy: 2 copies minimum
   - Background rebalancing

6. **Self-Healing** (`storage/healing.cpp`)
   - Detect corruption via checksum mismatch
   - Restore from healthy replica (with exclusion)
   - Log repair events

#### Integration

```cpp
// Example: Save encrypted document
Document doc;
doc.id = "doc-001";
doc.owner_token = user_token;
doc.plaintext = "Sensitive content...";

// Encrypt using Newton-Raphson + AES-256-GCM
EncryptionResult result = keyDeriver.encryptDocument(doc, timestamp);

// Store metadata in SQLite + blob on filesystem
documentStore.save(result.metadata, result.ciphertext);
replicaManager.replicate(result.ciphertext, redundancy=2);
```

#### Effort

- SHA-256: 6 hours
- AES-256-GCM: 10 hours
- Key derivation: 8 hours
- SQLite integration: 12 hours
- Filesystem replication: 10 hours
- Self-healing: 6 hours
- Tests + integration: 20 hours
- **Total Phase 2: 72 hours (~3 weeks)**

---

### Phase 3: Zero Trust & Governance (Weeks 9-12)

**Goal:** Authentication, authorization, and policy enforcement.

#### Deliverables

1. **Ed25519 Authentication** (`auth/ed25519.cpp`)
   - User keypair generation
   - Document signing / verification
   - Session token creation
   - JWT integration

2. **Capability-Based Access Control** (`auth/capabilities.cpp`)
   - Represent permissions as signed capabilities
   - Token validation with HMAC-SHA256
   - Role-based policy evaluation

3. **GCD Supplemental Validation** (`auth/gcd_validation.cpp`)
   - Use GCD as symbolic verification layer (not primary security)
   - Pair with Ed25519 for real authentication
   - Audit logging of GCD checks

4. **Zero Tolerance Enforcement** (`governance/zero_tolerance.cpp`)
   - Mandatory verification at every step
   - Abort on policy violation
   - Immutable audit trail

5. **RoyalCodeCheck** (`governance/royal_code_check.cpp`)
   - Scan transaction logs for violations
   - Ethical rule evaluation
   - Escalation to AI Official Board

6. **AI Official Board** (`governance/board.cpp`)
   - Weighted voting on sensitive decisions
   - Consensus threshold (60%)
   - Board member registry with weights
   - Decision logging

#### Integration

```cpp
// Zero Trust example
AuthResult auth = authenticator.verify(user_token, document_id);
if (!auth.valid) {
    return REJECT;  // -DENY_ACCESS in EdgeFront
}

// GCD supplemental check
int gcd = euclidean_gcd(hash(user_token), hash(resource_token));
if (gcd != 1) {
    logger.log("GCD_MISMATCH", user_token, resource_token);
    // Still allow if primary auth passed, but audit it
}

// Governance check
RoyalCodeResult royal = royalChecker.runDiagnostics(transaction_log);
if (!royal.compliant) {
    logger.escalateToBoard(royal.violations);
    return RESTRICT;  // Hold for board review
}
```

#### Effort

- Ed25519: 8 hours
- Capabilities: 10 hours
- GCD validation: 4 hours
- Zero Tolerance: 8 hours
- RoyalCodeCheck: 10 hours
- AI Board: 12 hours
- Integration + tests: 24 hours
- **Total Phase 3: 76 hours (~3 weeks)**

---

### Phase 4: UI & Integration (Weeks 13-16)

**Goal:** Windows application with Sacred Geometry UI.

#### Deliverables

1. **XAML-based UI** (`ui/main_window.xaml`)
   - WinUI 3 framework
   - Fluent Design system
   - Vesica Piscis window proportions
   - Golden Ratio scaling for buttons / status bars

2. **Document Editor** (`ui/editor.cpp`)
   - Rich text editing
   - Format preservation
   - Auto-save with encryption
   - Change tracking

3. **Settings Panel** (`ui/settings.xaml`)
   - Key management
   - Storage preferences
   - Governance board configuration
   - Privacy policy display

4. **Threat Heatmap** (`ui/threat_map.cpp`)
   - Visualize R.A.T. node coordinates
   - Color-coded threat levels (grey, yellow, red)
   - Real-time updates

5. **Audit Dashboard** (`ui/audit_dashboard.xaml`)
   - View transaction logs
   - Policy compliance status
   - Board voting history
   - Export reports

#### Integration

```cpp
// XAML example (pseudocode)
<Window>
    <Grid ColumnDefinitions="*,*" RowDefinitions="*,*">
        <TextBlock Text="Skylite Word Processor" />
        <Button Content="Save" Click="OnSave" />
        <Canvas x:Name="ThreatHeatmap" />  <!-- from Phase 4 -->
    </Grid>
</Window>
```

#### Effort

- XAML & WinUI setup: 10 hours
- Document editor: 16 hours
- Settings panel: 8 hours
- Threat heatmap: 12 hours
- Audit dashboard: 10 hours
- Tests + styling: 16 hours
- **Total Phase 4: 72 hours (~3 weeks)**

---

### Phase 5: Deployment & Hardening (Weeks 17-20)

**Goal:** Production-ready Windows application.

#### Deliverables

1. **Code Signing** (`build/signing.cmake`)
   - Acquire EV code signing certificate (DigiCert / Sectigo)
   - Sign all `.exe` and `.dll` files
   - Configure SmartScreen reputation

2. **MSIX Packaging** (`build/Package.appxmanifest`)
   - Define app capabilities
   - Set up auto-update
   - Create app store bundle

3. **Installer** (`installer/`)
   - WiX Toolset or NSIS
   - System dependencies check (Visual C++ runtime)
   - Registry entries for protocol handlers

4. **Security Audit** (`security/`)
   - Static analysis (Clang-Tidy, MSVC analyzer)
   - Dependency scanning (OWASP)
   - Penetration testing scope
   - Third-party code review

5. **Documentation** (`docs/`)
   - User manual (markdown)
   - Developer API documentation
   - Security model whitepaper
   - Deployment guide

6. **Continuous Integration** (`.github/workflows/`)
   - Automated builds on push
   - Unit test execution
   - Artifact signing
   - Release preparation

#### Effort

- Code signing: 4 hours
- MSIX packaging: 8 hours
- Installer: 8 hours
- Security audit: 20 hours
- Documentation: 16 hours
- CI/CD setup: 12 hours
- **Total Phase 5: 68 hours (~2.5 weeks)**

---

## Part 4: Critical Path (Minimum 20 Weeks)

**Timeline to MVP:**

| Phase | Deliverable                      | Duration | Cumulative |
| ----- | -------------------------------- | -------- | ---------- |
| 1     | EdgeFront VM                     | 2 weeks  | Week 2     |
| 2     | Cryptography & Storage           | 3 weeks  | Week 5     |
| 3     | Zero Trust & Governance          | 3 weeks  | Week 8     |
| 4     | UI & Integration                 | 3 weeks  | Week 11    |
| 5     | Deployment & Hardening           | 2.5 wks  | Week 13.5  |

**Parallel tracks (can overlap):**

- Security audit (weeks 8-13)
- Documentation (weeks 2-13)
- CI/CD setup (week 1)

---

## Part 5: Repository Structure (Target)

```
skylite-os/
├── .github/
│   └── workflows/
│       ├── build.yml
│       ├── test.yml
│       └── release.yml
├── src/
│   ├── vm/
│   │   ├── tokenizer.cpp
│   │   ├── parser.cpp
│   │   ├── compiler.cpp
│   │   └── vm.cpp
│   ├── crypto/
│   │   ├── sha256.cpp
│   │   ├── aes_gcm.cpp
│   │   └── key_derivation.cpp
│   ├── storage/
│   │   ├── document_store.cpp
│   │   ├── replica_manager.cpp
│   │   └── healing.cpp
│   ├── auth/
│   │   ├── ed25519.cpp
│   │   ├── capabilities.cpp
│   │   └── gcd_validation.cpp
│   ├── governance/
│   │   ├── zero_tolerance.cpp
│   │   ├── royal_code_check.cpp
│   │   └── board.cpp
│   └── ui/
│       ├── main_window.xaml
│       ├── editor.cpp
│       └── threat_map.cpp
├── tests/
│   ├── vm_tests.cpp
│   ├── crypto_tests.cpp
│   ├── storage_tests.cpp
│   ├── auth_tests.cpp
│   └── integration_tests.cpp
├── examples/
│   ├── secure_note.ef
│   ├── document_encryption.ef
│   └── governance_policy.ef
├── docs/
│   ├── ARCHITECTURE.md
│   ├── SPECIFICATION.md
│   ├── SECURITY.md
│   ├── API.md
│   └── USER_GUIDE.md
├── build/
│   ├── CMakeLists.txt
│   ├── Package.appxmanifest
│   └── signing.cmake
└── README.md
```

---

## Part 6: Success Criteria (MVP Definition)

✅ **VM Phase Complete When:**
- EdgeFront tokenizer passes 50 test cases
- Parser validates syntax on 20+ real scripts
- `NR(528)` converges to ≈22.525 ± 0.0001
- `GCD(a, b)` produces correct results
- REPL runs interactively

✅ **Cryptography Phase Complete When:**
- SHA-256 output matches NIST vectors
- AES-256-GCM encrypts/decrypts correctly
- Key derivation is deterministic (same input → same output)
- SQLite stores 100+ documents reliably
- Replication redundancy survives single node failure

✅ **Governance Phase Complete When:**
- Ed25519 authentication works end-to-end
- RoyalCodeCheck detects policy violations
- AI Board votes reach consensus on test cases
- Zero Tolerance aborts on verification failure
- Audit logs capture all decisions

✅ **UI Phase Complete When:**
- XAML compiles without errors
- Document editor opens/saves encrypted files
- UI respects Golden Ratio proportions
- Threat heatmap displays R.A.T. nodes
- Settings persist across sessions

✅ **Deployment Phase Complete When:**
- Binaries are signed and SmartScreen approved
- MSIX package installs cleanly
- No critical security issues identified
- Documentation is complete and accurate
- CI/CD builds automatically on commit

---

## Part 7: Decisions Required Now

### Decision 1: Language for Core VM

**Options:**
- **C++20** (faster, tighter control, Windows ecosystem, harder to maintain)
- **Rust** (memory-safe, moderate performance, steeper learning curve)

**Recommendation:** C++20 for initial MVP (leverage Windows CNG). Migrate to Rust later if required.

### Decision 2: Cryptography Library

**Options:**
- **libsodium** (portable, audited, simple API)
- **Windows CNG** (native, integrated with TPM, harder to use)
- **OpenSSL** (universal, but requires external dependency)

**Recommendation:** libsodium with Windows CNG as fallback for TPM integration.

### Decision 3: Storage Backend

**Options:**
- **SQLite + filesystem** (simple, proven, good enough for MVP)
- **RocksDB** (faster, more complex, overkill)
- **PostgreSQL** (distributed, requires external server)

**Recommendation:** SQLite + filesystem for MVP. Upgrade to distributed storage post-launch.

### Decision 4: UI Framework

**Options:**
- **WinUI 3** (modern, Fluent Design native, Windows-only)
- **Qt** (cross-platform, more complex, less native feel)
- **Electron** (cross-platform, heavy, electron stigma)

**Recommendation:** WinUI 3 for MVP (aligns with "Windows-inspired ecosystem").

### Decision 5: Open Source License

**Current:** MIT

**Assessment:** MIT is permissive, allows commercial use, minimal restriction. Good choice.

**Alternative:** Consider adding a **Contributor License Agreement (CLA)** if you plan to accept external contributions.

---

## Part 8: Immediate Next Steps (This Week)

### Day 1-2: Decide on language/tools
- Confirm C++20 + libsodium stack
- Set up VS 2022 + CMake project structure

### Day 3-4: Create VM skeleton
- Implement tokenizer for core symbols
- Add test file with 5 basic cases
- Submit as PR to `main` branch

### Day 5: Create issues for each phase
- Break Phase 1 into 8-10 GitHub issues
- Assign effort estimates
- Add to milestones

### Day 6-7: Documentation sprint
- Update `README.md` with this roadmap
- Create `docs/ARCHITECTURE.md`
- Create `docs/SPECIFICATION.md` (formalized)

---

## Appendix: Risk Mitigation

| Risk                        | Mitigation                              |
| --------------------------- | --------------------------------------- |
| **Crypto implementation flaws** | Use libsodium; audit with Verifone team |
| **Performance degradation** | Benchmark every phase; profiling gate   |
| **Windows-only lock-in**    | Plan macOS port after MVP               |
| **Supply chain attacks**    | Pin dependency versions; SBOM tracking  |
| **Scope creep**             | Strict MVP definition; defer features   |
| **Regulatory uncertainty**   | Legal review Q2 2026; patent filing Q3  |

---

## Conclusion

The Skylite OS ecosystem has an ambitious and coherent specification. The critical next step is converting **narrative architecture into executable infrastructure**.

**The highest-impact deliverable is a working EdgeFront VM.** Once bytecode execution works, every other subsystem becomes scriptable and testable.

**Estimated timeline to production-ready MVP: 16-20 weeks** with a full-time team of 2-3 engineers.

---

**Questions or changes to this roadmap? Open an issue on GitHub.**

**🖤👑 Heart secured. Infrastructure ready to build.** 💎🛡️
