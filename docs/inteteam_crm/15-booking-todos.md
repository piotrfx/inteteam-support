# Booking Task Templates

Create reusable checklists that can be applied to bookings with one click. Saves time when the same tasks repeat across jobs.

**Bookings -> Settings -> Todo Templates**, or navigate to `/admin/bookings/settings/todo-templates`

---

## Two Types of Templates

### 1. Stage Tasks (Automatic)

Tasks that are **automatically added** when a booking enters a specific stage.

For example, when a booking moves to **Undergoing**, automatically add:
- "Diagnose fault"
- "Contact customer with quote"
- "Order parts if needed"

**To set up:**
1. Select the **Stage Tasks** tab
2. Choose the stage (Incoming, Undergoing, or Completed)
3. Add tasks — they'll be auto-applied to every booking entering that stage
4. Drag to reorder

### 2. Department/Group Templates (Manual)

Named groups of tasks that can be applied manually to any booking.

For example, a group called **"Phone Repair"** might contain:
- "Check screen"
- "Test battery"
- "Test charging port"
- "Test speakers and mic"
- "Factory reset"

**To create a group:**
1. Click **Add Group**
2. Name it (e.g. "Phone Repair", "Laptop Repair", "Web Dev")
3. Add tasks to the group
4. Drag to reorder

---

## Applying a Template to a Booking

1. Open a booking detail page
2. Switch to the **Tasks tab**
3. Click **Apply Template**
4. Select the group you want to apply
5. The tasks are added to the booking's checklist

You can apply multiple groups to the same booking. Tasks from templates can be individually edited or deleted after being applied.

---

## Managing Templates

Each group tab shows:

- Task count (e.g. "5 tasks")
- Add/edit/delete individual tasks
- Drag to reorder tasks within the group
- Rename the group
- Delete the entire group
