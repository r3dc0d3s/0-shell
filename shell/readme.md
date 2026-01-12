# 0-Shell

A minimalist Unix-like shell implemented in Rust, designed to run core Unix commands using system-level abstractions — without relying on external binaries or built-in shells like bash.

This project emphasizes **parallel development, modularity, and safety**, making it suitable for embedded Linux environments or systems programming exercises.

---

## Table of Contents

- [Overview](#overview)
- [Learning Objectives](#learning-objectives)
- [Core Features](#core-features)
- [Architecture](#architecture)
- [Folder Structure](#folder-structure)
- [Command Development & Testing](#command-development--testing)
- [Parallel Development Strategy](#parallel-development-strategy)
- [Future Enhancements](#future-enhancements)

---

## Overview

0-Shell is a lightweight shell designed to mimic essential Unix behaviors:

- Display a prompt (`$ `)
- Accept and parse user commands
- Execute commands and return to the prompt
- Handle `Ctrl+D` gracefully to exit

All commands are implemented from scratch in Rust, without invoking external binaries.

---

## Learning Objectives

- Work with file and directory operations
- Manage user input and output within a shell loop
- Implement robust error handling
- Gain experience with Unix system calls in Rust

---

## Core Features

Implemented commands include:

- `echo`
- `cd`
- `ls` (`-l`, `-a`, `-F`)
- `pwd`
- `cat`
- `cp`
- `rm` (`-r`)
- `mv`
- `mkdir`
- `exit`

Additional shell behavior:

- Returns to prompt after each command
- Prints `Command '<name>' not found` for unknown commands
- Handles Ctrl+D for graceful exit

---

## Architecture

The project follows a **layered, decoupled design**:

### Layers

1. **Shell Core (Person 1)**
   - Reads input and dispatches commands
   - Handles parsing, errors, and the main loop
   - Owns shared state (`ShellState`) and error definitions

2. **Command Interface (Trait)**
   - Defines the contract between the shell and commands
   - All commands implement this interface
   - Guarantees:
     - Input arguments
     - Access to shared state
     - Success/error reporting

3. **Commands (Person 2 & 3)**
   - Independent capabilities (`ls`, `rm`, `cd`, etc.)
   - Only rely on interface and state
   - Can be tested without the shell loop
   - Must not control the shell or read input directly

---

## Folder Structure


src
├── main.rs
├── shell
│ ├── loop.rs
│ ├── parser.rs
│ ├── dispatcher.rs
│ └── error.rs
├── interface
│ ├── command.rs
│ ├── state.rs
│ └── error.rs
├── builtins
│ ├── fs (Person 2)
│ │ ├── ls.rs
│ │ ├── rm.rs
│ │ ├── cp.rs
│ │ ├── mv.rs
│ │ └── mkdir.rs
│ └── env (Person 3)
│ ├── cd.rs
│ ├── pwd.rs
│ ├── echo.rs
│ ├── cat.rs
│ └── exit.rs
└── testing
├── harness.rs (command runner)
└── fixtures
└── fs_tree (controlled filesystem for tests)


---

## Command Development & Testing

To allow **independent development**:

1. Each command implements the `BuiltinCommand` trait.
2. The **testing harness** allows commands to be executed without the shell loop.
3. The harness provides:
   - A fake `ShellState`
   - Arguments
   - Controlled filesystem (`fixtures/fs_tree`)
4. Developers can iterate on commands safely, verify behavior, and catch errors before shell integration.

> **Key insight:** Commands don’t depend on the shell — the shell depends on commands.

---

## Parallel Development Strategy

- **Person 1:** Shell core & interface  
- **Person 2:** Filesystem commands (`ls`, `rm`, `cp`, `mv`, `mkdir`)  
- **Person 3:** Environment commands (`cd`, `pwd`, `echo`, `cat`, `exit`)  

**Rules:**

- Interfaces are frozen early
- Each developer owns only their folder
- Commands tested via harness
- Integration is additive; merge conflicts are rare

**Dependency Graph:**

shell
↓
interface
↓
builtins/fs
↓
builtins/env

testing
↓
interface
↓
builtins/*


---

## Future Enhancements (Optional / Bonus)

- Ctrl+C (SIGINT) handling
- Auto-completion
- Command history
- Prompt showing current directory
- Colorized output
- Command chaining (`;`)
- Pipes (`|`)
- I/O redirection (`>`, `<`)
- Environment variable support
- Custom `help` command

---

## TL;DR

> **0-Shell is a minimalist Rust shell with decoupled architecture. Commands are pure capabilities; the shell orchestrates. Testing harnesses allow independent development and safe iteration.**
