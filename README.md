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

```
AGENTS.md
skills/
    android-architecture-skill.md
    android-patterns-skill.md
    android-testing-skill.md
```

## Philosophy

Code is read more often than it is written.

Optimize for the next engineer reading the code, not the current engineer writing it.

## Contributions

Suggestions and improvements are welcome.
Open an issue or submit a pull request.

## License

MIT License.
