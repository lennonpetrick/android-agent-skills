# android-engineering-skills

Skills that teach AI coding agents how to structure, write and test Android apps with Kotlin and Jetpack Compose.

> **Works alongside Google's official [Android skills](https://github.com/android/skills).**
> Google's skills handle specific tasks, like migrating XML to Compose or upgrading AGP.
> These skills cover how an Android project is structured, written and tested.
> Use both together.

**Code is read more often than it is written.** These skills optimize for the next engineer reading the code, not the current engineer writing it.

## Skills

| Skill | What it covers | When the agent uses it |
| --- | --- | --- |
| [`android-architecture`](android-architecture/SKILL.md) | Feature-first package layout, layer boundaries, naming, visibility and Hilt setup | Creating a project or feature, adding DI bindings, naming classes, deciding where code belongs |
| [`android-patterns`](android-patterns/SKILL.md) | Unidirectional data flow, ViewModels, state and actions, Flow composition, error handling, repositories, use cases and Compose UI | Writing ViewModels, state models, repositories, use cases or Compose screens |
| [`android-testing`](android-testing/SKILL.md) | JUnit 5, Turbine, MockK or Mockito-Kotlin, test naming and structure, parameterized tests | Writing or reviewing unit tests for ViewModels, use cases, mappers and repositories |

Agents load a skill only when a task matches it, so the skills don't add to every prompt.

## What the skills enforce

These are opinionated. Check that they match how your team works before adopting them.

**Architecture**

- Layers are `ui → presentation → domain ← data`, and dependencies always point inward.
- `domain` has no Android, Compose, Retrofit, database or DI dependencies.
- Code is organized by feature. No `utils` or `helpers` dumping grounds.

**Patterns**

- State flows down from the ViewModel, and actions flow up from the UI through callbacks.
- Screens expose a single UI state through `StateFlow`. One-shot events go through `SharedFlow`.
- Operations that can fail return `Result`, with errors mapped to explicit types.

**Testing**

- Test names use backticks and read like a sentence: `` `When amount is valid then it returns parsed value` ``.
- The class under test is always named `sut`.
- Each test follows the same order: create objects and mocks, stub, act, assert.
- Flows are tested with Turbine inside `runTest`.

## Assumed stack

Kotlin, Jetpack Compose, Coroutines and Flow, Hilt, Retrofit, JUnit 5, Turbine, and MockK or Mockito-Kotlin.

If your project uses something else, like Koin or XML views, the skills still apply in principle. You can also fork them and adjust them (see [Customize the skills](#customize-the-skills)).

## How to Set Up

Each coding agent reads skills from its own folder:

| Agent | Project folder | User folder (all projects) |
| --- | --- | --- |
| Gemini in Android Studio | `.agents/skills/` | `~/.agents/skills/` |
| Codex | `.agents/skills/` | `~/.agents/skills/` |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |

### 1. Clone this repo

Clone it somewhere outside your project.

macOS / Linux:

```bash
git clone --depth 1 https://github.com/lennonpetrick/android-engineering-skills.git /tmp/android-engineering-skills
```

Windows (PowerShell):

```powershell
git clone --depth 1 https://github.com/lennonpetrick/android-engineering-skills.git "$env:TEMP\android-engineering-skills"
```

### 2. Copy the skills into your project

Run the commands for your agent from your project's root folder. They never overwrite anything: if you already have a skill with the same name, yours is kept.

#### Gemini in Android Studio and Codex

Both read `.agents/skills/`, so one copy works for both.

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

#### Other agents

Any agent that supports the Agent Skills format works the same way. Copy the three folders into the skills folder it reads.

### Use the skills in all your projects

Use the user folder from the table as the destination instead. For example, for Claude Code:

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

## Try It

Agents pick up the skills on their own when a request matches. Some prompts to try:

- "Add a transactions screen that loads a list from the API and supports pull to refresh."
- "Write unit tests for `TransactionsViewModel`."
- "Review this feature for layer boundary violations."

You can also call a skill directly:

| Agent | How to call a skill |
| --- | --- |
| Gemini in Android Studio | Type `@` in the chat and pick the skill |
| Codex | `$android-patterns` |
| Claude Code | `/android-patterns` |

## Update the Skills

Pull the latest version, then copy again with overwriting allowed. Change `.agents/skills` to the folder where you installed them.

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

## Customize the Skills

The skills are plain Markdown, so you can edit them to match your team's conventions. For example, you can switch the mocking library, change the package layout or add your own review checklist items.

If you customize a skill, rename its folder and the `name` in its frontmatter. That way an update won't overwrite your changes.

## Contributing

Issues and pull requests are welcome, especially:

- corrections where a rule is wrong, unclear or outdated
- examples that make a rule easier for an agent to follow
- new skills for topics not covered yet, such as navigation, Room, modularization or instrumented tests

When editing or adding a skill:

- Keep one skill per folder, with a `SKILL.md` that has `name` and `description` in its frontmatter.
- Write the `description` so an agent can tell when to use the skill. Lead with the tasks it applies to.
- Keep each `SKILL.md` under 20,000 characters, the limit Android Studio recommends.

## Disclaimer

AI can make mistakes, so always double-check the results.

## License

android-engineering-skills is licensed under the [MIT License](LICENSE). See the `LICENSE` file for details.
