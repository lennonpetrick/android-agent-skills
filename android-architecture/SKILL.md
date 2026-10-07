---
name: android-architecture
description: >
  Defines project structure, package layout, layer boundaries, naming conventions,
  and dependency injection setup for Android projects. Use this skill whenever
  creating a new Android project, adding a new feature module, scaffolding a new
  screen or flow, setting up DI bindings, naming any class or file, or deciding
  which layer a piece of code belongs in. Also use it when reviewing or refactoring
  existing code to ensure it follows the established architecture.
---

# Android Architecture

Use this skill to keep Android projects consistent, scalable, and easy to navigate.

## 1. Core architecture rules

Follow Clean Architecture inspired layering:

- `domain` is the business core.
- `data` handles remote, local, and other external data sources.
- `presentation` coordinates state and mapping for the UI.
- `ui` contains Compose screens, reusable components, and theme.
- `di` contains dependency injection modules.

Dependency flow always points inward:

`ui -> presentation -> domain <- data`

Rules:

- `domain` must not depend on Android, Compose, Retrofit, database, or DI APIs.
- `presentation` must not depend on Retrofit, Room, or DTOs.
- `data` may depend on `domain`, not the other way around.
- `ui` must stay focused on rendering and user interaction.
- Keep business rules out of UI and Android framework classes.

## 2. Recommended project structure

Prefer a feature-first layout with shared infrastructure only where it truly belongs.

```text
com.company.app/
├── AppApplication.kt
├── MainActivity.kt
│
├── di/
│   ├── NetworkModule.kt
│   ├── DatabaseModule.kt
│   ├── RepositoryModule.kt
│   └── DispatcherModule.kt
│
├── domain/
│   ├── model/
│   ├── repository/
│   └── usecase/
│
├── data/
│   ├── api/
│   │   └── model/
│   ├── datasource/
│   ├── mapper/
│   └── repository/
│
├── presentation/
│   ├── <feature>/
│   │   ├── <Feature>ViewModel.kt
│   │   ├── model/
│   │   ├── mapper/
│
└── ui/
    ├── <feature>/
    │   ├── <Feature>Screen.kt
    │   ├── components/
    └── theme/
```

Rules:

- Keep each feature self-contained.
- Place shared abstractions in the lowest common layer possible.
- Avoid large folders full of unrelated classes.
- Avoid creating `utils` or `helpers` folders as a dumping ground.

## 3. Layer responsibilities

### domain

Contains only pure Kotlin code.

Typical contents:

- entities and value objects
- repository interfaces
- use cases
- domain errors
- domain calculators when the logic is reusable and not UI-specific

### data

Handles anything outside the app process or outside the pure domain model.

Typical contents:

- Retrofit APIs
- DTOs and network models
- local datasource implementations
- mappers between external models and domain models
- repository implementations
- caching, persistence, synchronization logic

### presentation

Owns screen behavior and state orchestration.

Typical contents:

- ViewModels
- screen UiState, UiAction, UiEvent
- input state and internal state models
- pure calculators, parsers, and mappers used only by the screen flow

### ui

Owns rendering.

Typical contents:

- Screen composables
- stateless content composables
- reusable UI components
- theme, typography, colors, shapes, previews

### di

Owns object graph wiring only.

Typical contents:

- bindings
- providers
- qualifiers
- module objects and abstract modules

## 4. Naming conventions

Use explicit and consistent names.

### Common patterns

- ViewModel: `[Feature]ViewModel`
- Screen: `[Feature]Screen`
- Screen state: `[Feature]ScreenUiState`
- Content state: `[Feature]UiState`
- Actions: `[Feature]UiAction`
- Events: `[Feature]UiEvent`
- Repository interface: `[Feature]Repository`
- Repository implementation: `[Feature]RepositoryImpl`
- Use case: `[Verb][Subject]UseCase`
- Mapper: `[Source]Mapper` or `[Target]Mapper`
- Parser: `[Type]Parser`
- Provider: `[Name]Provider`
- DTO: `[Entity]Dto`

Examples:

- `ObserveRateForCurrenciesUseCase`
- `ExchangeViewModel`
- `ExchangeScreenUiState`
- `ExchangeRepositoryImpl`
- `CurrencyMapper`
- `AmountParser`

Rules:

- Prefer names that reveal responsibility immediately.
- Do not use vague names such as `Manager`, `Helper`, `Processor`, or `Utils`.
- Use `internal` by default for implementation classes.
- Keep file names aligned with class names.

## 5. Visibility rules

Use the narrowest visibility that still works.

Default rules:

- `internal` for most app implementation classes
- package-private or public only when needed for module boundaries
- `public` only for intended shared APIs
- `private` for local helpers and implementation details

Practical guidance:

- ViewModels should usually be `internal`.
- Use cases should usually be `internal`.
- repository implementations should usually be `internal`.
- Compose screen and content functions should usually be `internal` or `private` if only used locally.
- Expose only the minimum needed surface area.

## 6. Dependency injection

Use Hilt with constructor injection.

Preferred pattern:

```kotlin
@HiltViewModel
internal class ExampleViewModel @Inject constructor(
    private val repository: ExampleRepository,
) : ViewModel()
```

Rules:

- Prefer constructor injection everywhere.
- Use `@Binds` for interface to implementation bindings.
- Use `@Provides` for objects that need construction logic.
- Keep all modules in `di/`.
- Separate modules by concern.
- Use `javax.inject.Inject`.

Common module types:

- repository bindings
- network setup
- database setup
- dispatcher providers
- feature specific object providers

Do not use:

- service locators
- manual singleton containers
- global mutable dependency access
- `jakarta.inject.Inject`

## 7. Android entry points

Recommended app entry points:

- `Application` annotated with `@HiltAndroidApp`
- single `MainActivity` annotated with `@AndroidEntryPoint`

Prefer:

- one activity architecture
- Compose Navigation for routing
- no fragments for new work unless a specific legacy constraint requires them

## 8. Module and package boundaries

When adding a new feature:

1. Define the feature boundary first.
2. Create domain contracts.
3. Add data implementations.
4. Add presentation state and logic.
5. Add UI composables.
6. Wire everything in `di`.

When moving code:

- move pure business rules down to `domain`
- move external data handling to `data`
- move screen orchestration to `presentation`
- keep rendering in `ui`

## 9. Code review checklist

A piece of code belongs in the correct place only if:

- its dependencies point in the right direction
- its responsibility is clear
- the layer contains only concerns allowed by that layer
- its name matches its role
- the code can be tested in isolation
- the implementation does not leak framework details into lower layers

Refactor when:

- a ViewModel contains parsing or formatting logic that belongs elsewhere
- a repository returns DTOs instead of domain models
- UI code performs business calculations
- a module mixes unrelated responsibilities
- naming does not make the feature easy to understand

## 10. Practical decision rules

Use this skill when deciding where code belongs.

- Business rule with no UI dependency: `domain`
- Network or persistence logic: `data`
- Screen orchestration or state transformation: `presentation`
- Compose rendering: `ui`
- Dependency wiring: `di`

When uncertain, choose the lowest layer that can own the logic without leaking framework concerns upward.