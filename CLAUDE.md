# Project: NAME_PROJECT

## 🏗 Project Structure (`src/`)
```

src/
...
...

```

# Rust Development Context & Tooling Protocol

## 🚨 CRITICAL: TOKEN ECONOMY
**CONTEXT WINDOW IS EXPENSIVE.** Raw shell output wastes tokens and degrades reasoning.

### 🚫 BANNED SHELL COMMANDS (use tools instead)
| ❌ Shell | ✅ Tool | Reason |
|:---|:---|:---|
| `cargo check` | `cargo-check` | Structured errors |
| `cargo build` | `cargo-build` | Summarized output |
| `cargo test` | `cargo-test` | Parsed results |
| `cargo clippy` | `cargo-clippy` | Lint data |
| `cargo new` | `cargo-new` | Correct templates |
| `cat Cargo.toml` | `cargo-info` / `workspace-info` | Efficient metadata |

**Shell fallback**: ONLY if tool fails or edge-case not covered.

## 🧠 Tool-First Workflow

### 1. Exploration
- `workspace-info` → project topology
- `cargo-info [crate]` → dependency details  
- `cargo-machete` → find unused deps

### 2. Build Cycle
1. Modify code
2. `cargo-check` (NOT shell)
3. `cargo-clippy` (NOT shell)
4. `cargo-test` (NOT shell)
5. `cargo-hack` for feature combinations

### 3. Documentation (Anti-Hallucination)
**DO NOT GENERATE CODE FROM MEMORY.** Crates change often.
1. `search_crate` → find package
2. `search_documentation_items` → fuzzy search
3. `retrieve_documentation_page` → READ docs
4. `retrieve_documentation_all_items` → fallback only

### 4. Error Resolution
On error code (e.g. `E0507`):
- **RUN** `rustc-explain E0507` BEFORE fixing
- Apply fix based on official explanation

### 5. Code Quality
- `cargo-clippy` before final changes
- `cargo-fmt` for formatting
- `cargo-deny-check` for security audits

## 📝 Coding Style & Etiquette
- **Idiomatic Rust**: Prefer `Result`/`Option` combinators (`map`, `and_then`) over explicit `match` where readable.
- **Error Handling**: Use `thiserror` for libraries and `anyhow` for applications unless specified otherwise.
- **Async**: Assume `tokio` runtime unless `async-std` is present in `workspace-info`.
- **Comments**: Write doc comments (`///`) for public APIs.

## ⚡ Tool Map (Intent -> Command)
| Intent | Preferred Tool |
| :--- | :--- |
| "Does this code compile?" | `cargo-check` |
| "What features does X have?" | `cargo-info X` |
| "What is error E0xxx?" | `rustc-explain E0xxx` |
| "Clean up unused deps" | `cargo-machete` |
| "Check detailed compatibility" | `cargo-hack` |
| "Find how to use Vec" | `search_documentation_items` -> `retrieve_documentation_page` |