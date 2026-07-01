# ☀️ Daily Dashboard & Habit Tracker Pro - Detailed Project Description

The **Daily Dashboard & Habit Tracker Pro (Enhanced Edition)** is a fully automated, GitHub-native habit tracking application implementing a serverless, event-driven architecture. By using GitHub Issues as the user interface and GitHub Actions as the backend controller, the application updates static databases and HTML/markdown dashboards dynamically.

---

## 📂 Project Structure & Files Involved
The codebase is lightweight, optimized for GitHub's native environment, and consists of:
1. **`.github/workflows/app.yml`**: The workflow orchestrator containing GitHub Script triggers, parsing logic, and git-commit shell tasks.
2. **`.github/ISSUE_TEMPLATE/habit-tracker.yml`**: The YAML form schema rendering structured fields in the issue creator interface.
3. **`database.json`**: The persistent file-based database storing tasks, metadata, historical completions, and streaks.
4. **`analytics.json`**: An auto-generated output calculating real-time aggregates and highlights (e.g. Needs Attention).
5. **`README.md`**: The rendered front-end dashboard detailing tasks grouped by categories, completion percentages, and usage instructions.
6. **`IMPLEMENTATION.md` & `PROJECT_COMPLETION_GUIDE.md`**: Architectural documentation outlining features and setup directions.

---

## 🛠️ System Architecture & Event-Driven Data Flow
The core application logic runs inside a GitHub Actions runner, triggered by issue webhooks or schedule webhooks:
1. **User Interaction**: The user interacts with the system by creating a structured issue or clicking a "Tick It" link in the README.
2. **Event Dispatching**: This action creates a new issue in the GitHub repository, which fires the `issues` webhook event.
3. **Template Parsing**: The workflow parses template fields (using `actions/github-script` JavaScript step) and assigns labels.
4. **State Machine execution**: A Bash script uses `jq` to read/write state in the file-based database `database.json`.
5. **Analytics Calculation**: Metrics like completion rate, streaks, and category performance are updated in `analytics.json`.
6. **Dashboard Generation**: The script regenerates a beautiful `README.md` showing current stats.
7. **Persistence**: Git commits the updated databases and README back to the repository and closes the issue automatically.

---

## 🎯 Key Technical Features

### 1. File-Based JSON Database (`database.json`)
A comprehensive, flat JSON array storing full habit definitions and transaction history:
- **Task Metadata**: `task` (name), `category` (Learning, Health, Work, Mental, Personal), `priority` (High, Medium, Low), `frequency` (daily, weekly, monthly), and `createdAt` (ISO Date).
- **State Fields**: `completed` (boolean state showing whether the habit was ticked off today).
- **Time-Series Stats**: `streak` (consecutive completions counter), `totalCompleted` (total completions tally), and `completedDates` (array of all completion dates).
- **Transaction Log**: `history` array containing objects with `date`, `time`, and `action` to provide an audit trail.

### 2. Event-Driven Workflow Controller (`.github/workflows/app.yml`)
The GitHub Action acts as the system controller, routing payloads into 9 distinct command paths:
- **PATH A (The Janitor)**: Runs on Sunday midnight schedule; cleans up old issue logs to prevent repository clutter.
- **PATH B (Daily Reset)**: Reset daily habits to uncompleted every day at 6:00 AM IST. Also resets weekly/monthly tasks accordingly.
- **PATH C (Template Processing)**: Processes structured issue forms and adds tasks with complete metadata validation.
- **PATH D (Remove Task)**: Removes a specific task from `database.json` via issue title commands.
- **PATH E (Mark Completed)**: Targets a habit, updates its state to completed, increments streaks, and appends logs to `history`.
- **PATH F (Category Assignment)**: Updates the category of a target task in the DB array.
- **PATH G (Priority Assignment)**: Sets or updates priority of a target task.
- **PATH H (Frequency Assignment)**: Alters recurrence behavior (daily/weekly/monthly).
- **PATH I (Issue Non-Template)**: Appends new tasks with default metadata when created without issue templates.

### 3. Dynamic Analytics Generation (`analytics.json`)
Calculates real-time aggregates like overall completion rate, best performing streaks, categories distribution, and habits needing immediate attention (0 completions).

---

## 📋 Comprehensive Database Schema Example
```json
[
  {
    "task": "Leetcode",
    "completed": false,
    "category": "Learning",
    "priority": "High",
    "frequency": "daily",
    "completedDates": [],
    "streak": 0,
    "totalCompleted": 0,
    "createdAt": "2026-05-15",
    "lastCompletedDate": null,
    "history": []
  }
]
```

---

## 💻 Issue Interface Commands (DSL)
The system supports text commands issued in the title of GitHub Issues. These commands simulate CLI API endpoints:
- `DONE <TaskName>` - Marks a habit completed.
- `REMOVE <TaskName>` - Deletes a habit from the tracking list.
- `CATEGORY <TaskName> | <Category>` - Updates category classification.
- `PRIORITY <TaskName> | <Priority>` - Sets task urgency level.
- `FREQUENCY <TaskName> | <Frequency>` - Schedules the recurrence rate.
- `REFRESH` - Manually triggers the reset workflow for testing/debugging.
- `<TaskName>` - Adds a new habit with default parameters.

---

## 📈 Dashboard Compilation Mechanics
The compilation of `README.md` is handled programmatically:
1. **Header Assembly**: Writes static headers and dynamic timezone-aware update timestamps.
2. **Stats Calculation**: Summarizes completion rate (`completedToday / totalTasks * 100`) and displays a progress bar representation.
3. **Category Loop**: Sorts habits by category, checks completion state, and formats unchecked items with a dynamic `[Tick It](url)` link containing pre-populated query parameters to trigger completion issues automatically.
4. **Highlights Section**: Appends lists of "Top Performers" (highest streak and completed counts) and "Needs Attention" (habits with zero completions).

---

## ⚙️ How to Deploy & Initialize
To launch a copy of this Habit Tracker, you only need to follow these steps:
1. **Fork the Repository**: Copy the code structure into your own GitHub account.
2. **Enable Write Permissions**: Go to Repository Settings -> Actions -> General -> Workflow permissions, select "Read and write permissions" and save.
3. **Trigger the Workflow**: Manually trigger the "Habit Tracker Pro" workflow from the Actions tab or open an issue with a habit name to initialize database entries.

---

## 🚀 DevOps & Portfolio Talking Points
- **Bash Scripting & jq**: Demonstrates advanced string manipulation and nested JSON schema querying.
- **Automated Workflow Orchestration**: Showcases event routing, cron triggers, secure token access, and repository write permissions.
- **Data Engineering**: Tracks time-series data, streaks, and logs historic transitions without an external SQL/NoSQL engine.
- **UX & Markdown Dashboarding**: Formats data visually using GitHub-flavored markdown markdown tables, lists, and emojis.
- **Continuous Integration / Continuous Deployment (CI/CD)**: Automates git commits and workflow triggers directly within the environment.
- **Serverless Paradigm**: Proves that structured backend databases and dynamic views can run fully within repository environments.
