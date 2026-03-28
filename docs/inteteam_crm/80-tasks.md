# Task Management

Create and track tasks for your team, linked to bookings or standalone.

**Business -> Tasks -> All Tasks**, or navigate to `/admin/tasks`

---

## Task List

A table showing all tasks:

| Column | What it shows |
|--------|-------------|
| **Title** | Task name (click to view) |
| **Status** | Coloured badge (customisable) |
| **Booking** | Linked booking reference (if attached) |
| **Assignee** | Who's responsible (or "Unassigned") |
| **Deadline** | Due date (overdue tasks highlighted in red) |
| **Created** | When the task was created |

On mobile, tasks display as cards instead of a table.

Use the **filters** to narrow by status or assignee.

---

## Creating a Task

1. Click **New Task**
2. Fill in:

| Field | Required | Description |
|-------|----------|------------|
| **Title** | Yes | What needs to be done |
| **Description** | No | More detail |
| **Status** | Yes | Choose from your custom statuses |
| **Assignee** | No | Who should do it |
| **Task Date** | No | When to start |
| **Deadline** | No | When it's due |

3. Click **Create Task**

---

## Task Detail Page

Click any task to see the full detail:

**Header:**
- Task title, overdue badge (if past deadline)
- Created by and date
- Edit and Delete buttons

**Main section:**
- **Description** — full text
- **Activity log** — timeline of all changes (status changes, assignments, etc.)

**Sidebar:**
- **Status** badge
- **Assignee**
- **Task Date** (if set)
- **Deadline** (if set, with overdue indicator)
- **Linked Booking** — click to go to the booking
- **Customer** — phone number and WhatsApp link (if available)

---

## Task Statuses

**Business -> Tasks -> Statuses**, or navigate to `/admin/task-statuses`

Create custom statuses for your workflow:

1. Click **New Status**
2. Enter:
   - **Name** (e.g. "To Do", "In Progress", "Blocked", "Done")
   - **Colour** — pick a colour for the badge
   - **Default status** — tick if this should be the default for new tasks
   - **Marks task complete** — tick if this status means the task is finished
3. Save

Drag the **grip handle** to reorder statuses in the dropdown.

---

## Task Categories

**Business -> Tasks -> Categories**, or navigate to `/admin/task-categories`

Organise tasks into categories:

1. Click **New Category**
2. Enter:
   - **Name** (e.g. "Bug Fix", "Feature Request", "Admin")
   - **Colour**
   - **Description** (optional)
3. Save

Drag to reorder. Deleting a category makes its tasks uncategorised (not deleted).
