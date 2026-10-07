# android-engineering-skills
A collection of modern Android architecture, implementation patterns, and testing conventions designed for AI coding agents and teams.

> **Works alongside Google's official [Android skills](https://github.com/android/skills).**
> Google's skills handle specific tasks, like migrating XML to Compose or upgrading AGP.
> This repo covers how an Android project is structured, written and tested.
> Use both together.

This repository provides opinionated guidelines for building maintainable, scalable, and production-ready Android applications using Kotlin and Jetpack Compose.

The goal is to optimize for readability, explicit state, separation of concerns, and long-term maintainability.

## Principles

* Correctness over cleverness
* Explicit state over implicit behavior
* Testability first
* Reactive architecture
* Separation of concerns
* Maintainable solutions
* Code that new engineers can understand quickly

## Technology Stack

* Kotlin
* Jetpack Compose
* Coroutines
* Flow / StateFlow
* Hilt
* Retrofit
* Kotlin Serialization
* JUnit 5
* Turbine
* Mockito or MockK

## Skills

### Android Architecture

Defines:

* Project structure
* Layer boundaries
* Naming conventions
* Package organization
* Dependency injection setup

Use when:

* Creating a new project
* Adding a feature
* Refactoring existing code
* Designing modules

### Android Patterns

Defines:

* Unidirectional Data Flow
* ViewModel design
* State management
* Flow composition
* Repository patterns
* UseCases
* Compose patterns
* Error handling

Use when:

* Implementing features
* Creating ViewModels
* Designing state models
* Building Compose screens

### Android Testing

Defines:

* JUnit 5 conventions
* Test naming patterns
* Arrange / Stub / Act / Assert structure
* Turbine usage
* Mockito or MockK usage
* Parameterized tests
* Dynamic tests

Use when:

* Writing unit tests
* Testing Flows
* Creating test suites
* Reviewing test quality

## How to Set Up

Each coding agent looks for skills in its own folder. Copy the three skill folders into the folder your agent reads, and it picks them up automatically when a task matches the skill's description.

### 1. Clone this repo

Clone it somewhere outside your project:

macOS / Linux:

```bash
git clone --depth 1 https://github.com/lennonpetrick/android-engineering-skills.git /tmp/android-engineering-skills
```

Windows (PowerShell):

```powershell
git clone --depth 1 https://github.com/lennonpetrick/android-engineering-skills.git "$env:TEMP\android-engineering-skills"
```

### 2. Copy the skills into your project

Run the commands for your agent from your project's root folder.

The commands never overwrite anything. If you already have a skill with the same name, yours is kept.

#### Gemini in Android Studio

macOS / Linux:

```bash
mkdir -p .agents/skills
cp -Rn /tmp/android-engineering-skills/android-* .agents/skills/
```

Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force .agents\skills | Out-Null
Get-ChildItem "$env:TEMP\android-engineering-skills\android-*" -Directory | Where-Object { -not (Test-Path ".agents\skills\$($_.Name)") } | Copy-Item -Destination .agents\skills -Recurse
```

Android Studio also reads `.android-studio/skills/`. Versions before Android Studio Quail read `.skills/` instead.

Invoke a skill manually by typing `@` in the Gemini chat.

#### Codex

macOS / Linux:

```bash
mkdir -p .agents/skills
cp -Rn /tmp/android-engineering-skills/android-* .agents/skills/
```

Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force .agents\skills | Out-Null
Get-ChildItem "$env:TEMP\android-engineering-skills\android-*" -Directory | Where-Object { -not (Test-Path ".agents\skills\$($_.Name)") } | Copy-Item -Destination .agents\skills -Recurse
```

Codex and Gemini in Android Studio share `.agents/skills/`, so one copy works for both.

Invoke a skill manually with `$android-architecture`, `$android-patterns` or `$android-testing`.

#### Claude Code

macOS / Linux:

```bash
mkdir -p .claude/skills
cp -Rn /tmp/android-engineering-skills/android-* .claude/skills/
```

Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force .claude\skills | Out-Null
Get-ChildItem "$env:TEMP\android-engineering-skills\android-*" -Directory | Where-Object { -not (Test-Path ".claude\skills\$($_.Name)") } | Copy-Item -Destination .claude\skills -Recurse
```

Invoke a skill manually with `/android-architecture`, `/android-patterns` or `/android-testing`.

#### Other agents

Any agent that supports the Agent Skills format works the same way. Copy the three folders into the skills folder it reads.

### Use the skills in all your projects

To install the skills for your user instead of one project, use your home folder as the destination: `~/.agents/skills` or `~/.claude/skills` on macOS and Linux, `$HOME\.agents\skills` or `$HOME\.claude\skills` on Windows.

For example, for Claude Code:

macOS / Linux:

```bash
mkdir -p ~/.claude/skills
cp -Rn /tmp/android-engineering-skills/android-* ~/.claude/skills/
```

Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force $HOME\.claude\skills | Out-Null
Get-ChildItem "$env:TEMP\android-engineering-skills\android-*" -Directory | Where-Object { -not (Test-Path "$HOME\.claude\skills\$($_.Name)") } | Copy-Item -Destination $HOME\.claude\skills -Recurse
```

### Update the skills

Pull the latest version, then copy again with overwriting allowed. This example updates `.agents/skills`; change the folder to match where you installed them:

macOS / Linux:

```bash
git -C /tmp/android-engineering-skills pull
cp -R /tmp/android-engineering-skills/android-* .agents/skills/
```

Windows (PowerShell):

```powershell
git -C "$env:TEMP\android-engineering-skills" pull
Get-ChildItem "$env:TEMP\android-engineering-skills\android-*" -Directory | Copy-Item -Destination .agents\skills -Recurse -Force
```

If you customized a skill, rename its folder first so the update doesn't overwrite your changes.

## Intended Audience

These documents are useful for:

* Android engineers
* Tech leads
* Staff engineers
* Teams defining coding standards
* AI coding assistants
* Cursor
* Claude Code
* Gemini
* GitHub Copilot
* ChatGPT
* Codex

## Repository Structure

Each skill is a folder with a `SKILL.md` file, following the Agent Skills format.

```
android-architecture/SKILL.md
android-patterns/SKILL.md
android-testing/SKILL.md
```

## Philosophy

Code is read more often than it is written.

Optimize for the next engineer reading the code, not the current engineer writing it.

## Contributions

Suggestions and improvements are welcome.
Open an issue or submit a pull request.

## License

MIT License.
