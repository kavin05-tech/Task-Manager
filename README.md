# Task Manager

A Python desktop application for creating, updating, prioritizing, tracking, and deleting tasks. Built with Tkinter and SQLite, it combines persistent task storage with modular application logic, undo support, and a FIFO task-processing workflow.

## Features

- Create, edit, delete, and inspect tasks with a title, description, priority, status, and optional deadline.
- Track Pending, In Progress, Completed, and Overdue tasks.
- Filter by status and sort by priority, title, deadline, or creation date.
- Search task titles using exact, prefix, and substring matching.
- Undo task edits and deletions during the current session.
- Start the oldest pending task with **Process Next Task**.
- View session activity logs and dashboard counts with completion progress.
- Switch between light and dark themes.

## Technology

| Component | Technology |
| --- | --- |
| Language | Python |
| Desktop interface | Tkinter / ttk |
| Persistent storage | SQLite |
| Domain objects | Python dataclasses |

No third-party Python packages are required. Tkinter and SQLite are standard-library modules; your Python installation must include Tk support.

## Quick start

Use Python 3.12+ with a desktop environment.

```bash
git clone https://github.com/kavin05-tech/Task-Manager-with-custom-dsa.git
cd Task-Manager-with-custom-dsa
python main.py
```

An optional virtual environment can be created with `python -m venv .venv`.

On first launch, the application creates `task_manager.db` beside `main.py`. Fresh clones start with an empty task list.

**Existing users:** before adopting the new layout, back up your database from
`Task Manager/outputs/TaskManager/task_manager.db`.
Copy the backup beside the new root-level `main.py` to retain your tasks.
Local database files are excluded from future commits.

## Using the application

1. Click **Add Task** and enter a title, priority, status, and optional deadline in `YYYY-MM-DD` format.
2. Use **Status** to filter tasks and **Sort** to change their ordering.
3. Enter a title or part of a title and click **Search**.
4. Select a task to **Edit Task**, **Delete Task**, or **View Details**. Double-clicking also opens its details.
5. Click **Undo** to restore the most recently edited or deleted task.
6. Click **Process Next Task** to move the oldest pending task to **In Progress**.
7. Use **View Logs** to inspect activity from the current session.

Past deadlines automatically mark Pending and In Progress tasks as Overdue when the task list or dashboard refreshes. Completed tasks remain Completed.

## Project structure

| Path | Responsibility |
| --- | --- |
| `main.py` | Compose application components and launch Tkinter |
| `gui.py` | Task forms, table, dashboard, dialogs, and theme switching |
| `controller.py` | Task workflows, undo snapshots, search, sorting, and activity logging |
| `database.py` | SQLite schema and parameterized task CRUD queries |
| `models.py` | Task, Activity, and UndoAction dataclasses |
| `utils.py` | Priority/status constants, timestamps, and input validation |
| `data_structures/` | Custom stack, queue, linked list, search, and sorting helpers |

The GUI delegates task operations to `TaskManager`, which coordinates persistence and the workflow helpers. SQL remains in `DatabaseManager`.

## Database design

The `Tasks` table stores:

| Field | Purpose |
| --- | --- |
| `id` | Integer primary key |
| `title` | Task name |
| `description` | Optional task details |
| `priority` | Low = 1, Medium = 2, High = 3 |
| `status` | Current workflow status |
| `created_at` | Local creation timestamp |
| `deadline` | Optional deadline string |

Queries use parameters for task values. Tasks persist across application restarts.

## Workflow helpers

| Component | Use |
| --- | --- |
| Linked-node stack | Stores pre-edit and pre-delete snapshots for undo |
| Linked-node queue | Starts pending tasks in creation order |
| Singly linked list | Holds session activity entries |
| Binary search | Finds exact titles and the start of prefix matches |
| Selection sort | Orders tasks by priority, title, or deadline |

Creation-date ordering is performed by SQL. Title search sorts the task list before searching; substring matching uses a linear scan when no exact or prefix match is found. Selection sort has quadratic time complexity, so the project is intended for small local datasets.

## Current scope

- Undo supports edits and deletions; task creation, FIFO processing, and automatic overdue changes are not undoable.
- Redo is not implemented.
- Activity logs and undo snapshots are kept in memory and reset when the application closes.
- Search returns one exact title match first, otherwise prefix matches, otherwise substring matches.
- Overdue tasks do not automatically return to Pending when their deadline changes; their status must be updated.
- This is a local desktop application. User accounts, a REST API, and reminders are future improvements.

## Manual verification

- Add tasks with different priorities and verify sorting and status filters.
- Try a blank title or an invalid deadline and check the validation message.
- Edit a task, undo the change, delete it, and undo the deletion.
- Add two pending tasks and verify that **Process Next Task** starts the oldest.
- Give an unfinished task a past deadline and refresh to verify Overdue status.
- Close and reopen the application to verify task persistence.
- Inspect **View Logs**, task details, and the dark theme.

## Suggested improvements

- Add redo and persist activity history.
- Validate priority and status consistently for edits.
- Make database connection cleanup explicit.
- Add automated regression tests for task workflows.
- Add CSV import/export, backups, and deadline reminders.
