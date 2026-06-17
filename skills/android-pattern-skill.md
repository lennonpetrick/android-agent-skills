---
name: android-patterns
description: >
  Defines all implementation patterns for Android features: Unidirectional Data Flow
  (UDF), ViewModel design, Flow composition, error handling, repository pattern,
  domain UseCases, and Compose UI patterns. Use this skill whenever implementing a
  ViewModel, designing state or action models, writing a repository or UseCase,
  composing Flows, handling errors, building Compose screens or components, or
  reviewing code for correctness of reactive patterns or state management.
---

# Android Patterns

Use this skill to implement Android features with explicit state, reactive flows, and testable logic.

## 1. Unidirectional Data Flow

Use a single direction of data movement.

User action -> ViewModel -> state update -> UI render -> optional event

Rules:

- Composables do not own business state.
- Composables emit actions upward through callbacks.
- ViewModels own screen behavior and coordinate the data flow.
- State flows down from the ViewModel into Compose.
- Events flow out through one-shot streams such as `SharedFlow`.

Prefer this structure:

```text
UiAction -> onAction() -> internal state -> derived state -> UiState -> Composable
```

## 2. State model design

Use explicit state types instead of boolean flags when possible.

### Screen state

Use a sealed interface for high-level screen lifecycle state.

```kotlin
@Immutable
internal sealed interface FeatureScreenUiState {
    data object Loading : FeatureScreenUiState
    data class Loaded(val data: FeatureUiState) : FeatureScreenUiState
    data object Error : FeatureScreenUiState
}
```

### Content state

Use a data class for the rendered content.

```kotlin
@Immutable
internal data class FeatureUiState(
    val title: String,
    val subtitle: String,
    val items: List<ItemUiState>,
)
```

### Actions

Use a sealed interface for user intents.

```kotlin
@Immutable
internal sealed interface FeatureUiAction {
    data object Retry : FeatureUiAction
    data class QueryChanged(val value: String) : FeatureUiAction
}
```

### Events

Use a sealed interface for one-time side effects.

```kotlin
internal sealed interface FeatureUiEvent {
    data class ShowError(val message: String) : FeatureUiEvent
    data object NavigateBack : FeatureUiEvent
}
```

Rules:

- Keep actions raw and user driven.
- Keep events transient.
- Keep UiState focused on rendering.
- Keep internal state separate from UiState whenever the ViewModel needs raw typed values.

## 3. ViewModel pattern

ViewModels should coordinate, not calculate everything.

Responsibilities:

- receive actions
- update internal state
- connect domain flows
- expose `StateFlow`
- emit one-time events
- orchestrate loading, retry, and recovery behavior

ViewModels should not:

- parse API responses
- contain complex business rules
- format display values
- leak DTOs or Retrofit types
- make UI rendering decisions that belong in Compose

Recommended shape:

```kotlin
@HiltViewModel
internal class FeatureViewModel @Inject constructor(
    private val observeItemsUseCase: ObserveItemsUseCase,
    private val mapper: FeatureUiStateMapper,
) : ViewModel() {

    private val _internalState = MutableStateFlow<FeatureInput?>(null)
    private val _uiEvents = MutableSharedFlow<FeatureUiEvent>()
    val uiEvents: SharedFlow<FeatureUiEvent> = _uiEvents.asSharedFlow()

    val uiState: StateFlow<FeatureScreenUiState> =
        someFlow
            .map { result -> result.fold(...) }
            .stateIn(
                scope = viewModelScope,
                started = SharingStarted.WhileSubscribed(5_000),
                initialValue = FeatureScreenUiState.Loading,
            )

    fun onAction(action: FeatureUiAction) {
        when (action) {
            FeatureUiAction.Retry -> retry()
            is FeatureUiAction.QueryChanged -> updateQuery(action.value)
        }
    }
}
```

Rules:

- Use `stateIn` for `StateFlow` exposure.
- Prefer `SharingStarted.WhileSubscribed(...)` for UI-bound streams.
- Keep `onAction(action)` as the single entry point for user input.
- Use private helpers for large branches.
- Keep mutable state private.

## 4. Flow composition patterns

Use Flow operators to compose asynchronous work.

### `combine`

Use when output depends on multiple latest values.

```kotlin
combine(firstFlow, secondFlow) { first, second ->
    ...
}
```

### `flatMapLatest`

Use when a new upstream value should cancel previous work.

```kotlin
queryFlow.flatMapLatest { query ->
    repository.search(query)
}
```

### `map`

Use for pure transformations.

```kotlin
flow.map { value -> transform(value) }
```

### `scan`

Use when the next value depends on the previous one.

```kotlin
flow.scan(initialValue) { previous, current ->
    ...
}
```

### `distinctUntilChanged`

Use to prevent unnecessary recomputation and re-rendering.

### `shareIn` and `stateIn`

Use to share upstream work and keep the latest state available.

Rules:

- Prefer reactive composition over manual callbacks.
- Avoid creating duplicate subscriptions for the same source.
- Derive new flows from existing flows instead of copying state by hand.
- Use `mapLatest` or `flatMapLatest` when newer input should cancel older work.

## 5. Error handling

Use explicit domain errors when possible.

Prefer:

```kotlin
sealed interface FeatureError {
    data object Network : FeatureError
    data object Unauthorized : FeatureError
    data object Unknown : FeatureError
}
```

Rules:

- Prefer `Result<T>` for operations that may fail.
- Do not throw exceptions through flow pipelines when a recoverable result model is enough.
- Map low-level errors into domain or presentation friendly errors.
- Handle errors near the boundary where they happen.
- Keep one-time UI errors in events, not in durable state, unless the UI must render them persistently.

Recommended patterns:

- initial load failure -> `Error` screen state or explicit error state
- recoverable refresh failure -> keep last good data and emit a snack bar event
- unrecoverable failure -> show an error state with retry action

## 6. Repository pattern

Repositories should expose domain models and flows, not transport models.

Prefer:

- `Flow<Result<List<Model>>>`
- `Flow<Result<Model>>`
- suspend functions returning domain models or `Result`

Avoid:

- DTOs in repository interfaces
- Retrofit response types in presentation
- callbacks for data sources when Flow is a better fit

Repository responsibilities may include:

- network access
- local cache access
- synchronization
- mapping between layers
- retry or refresh coordination where appropriate

Keep mapping explicit and dedicated to mapper classes.

## 7. Use case pattern

Use cases represent application actions.

Naming:

- `FetchItemsUseCase`
- `ObserveItemsUseCase`
- `SubmitFormUseCase`

Rules:

- Use `fun [verb](...)`. For example, `fetchItemsUseCase.fetch()` or `observeItemsUseCase.observe()`
- Keep use cases small and focused.
- Use use cases to preserve a stable boundary between presentation and domain.
- Put reusable business logic behind use cases or domain services.

## 8. Compose patterns

### Screen and content split

Use a screen composable to connect to the ViewModel, and a stateless content composable for rendering.

```kotlin
@Composable
internal fun FeatureScreen(viewModel: FeatureViewModel) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()
    FeatureContent(
        uiState = uiState,
        onAction = viewModel::onAction,
    )
}
```

Rules:

- Screen composable owns collection and side effects.
- Content composable receives state and callbacks only.
- Do not pass ViewModel into deeply nested UI components.
- Always create preview for composable components and content composable when possible.

### One-shot events

Collect events in `LaunchedEffect` from the screen layer.

### Local UI state

Keep purely visual or temporary state in Compose when it does not belong in the ViewModel.

Examples:

- bottom sheet visibility
- text field focus
- local expanded state
- transient animations

### State hoisting

Hoist state to the nearest composable that needs to share it.

### List rendering

Always provide stable keys in lazy lists when item identity exists.

### Text input

Use a local `TextFieldValue` only when the UI must preserve cursor position or selection.

Use a visual transformation only for display formatting. Keep the raw input value separate.

## 9. Mapping and calculation

Keep mapping and calculations out of composables.

Use dedicated classes for:

- mappers
- parsers
- formatters
- calculators

Rules:

- parsing converts text to typed input
- formatting converts typed values to display values
- mapping converts model A to model B
- calculators implement domain rules and state derivation

Do not mix these concerns in one place unless the feature is extremely small.

## 10. Practical review checklist

Check the code against these questions:

- Does the ViewModel coordinate rather than compute everything?
- Are actions, state, and events distinct?
- Are flows shared and lifecycle aware?
- Does the UI remain stateless where possible?
- Are errors handled explicitly?
- Are mapping and formatting extracted into named classes?
- Is the reactive behavior easy to explain from the code?

Refactor when:

- a composable mutates business state
- a ViewModel directly formats UI strings
- repository interfaces leak DTOs
- flow subscriptions are duplicated unnecessarily
- a feature uses flags where sealed state would be clearer
