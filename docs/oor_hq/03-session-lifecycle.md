# Session Lifecycle

## Active Session

While you're using VIP, the session is active. Your usage minutes are being counted.

---

## Idle Warning

If the system doesn't receive a heartbeat for **5 minutes**, it marks your session as idle and sends a warning notification.

You'll see a message: "Your VIP session is idle. Click to keep it alive."

---

## Grace Period

After the idle warning, you have a **5-minute grace period**:

- **Click "Keep session alive"** — the timer resets and your session continues
- **Do nothing** — after 5 minutes, the session is automatically stopped

---

## Auto-Stop

When a session is stopped (idle timeout or manual):

1. The container is shut down
2. Your usage minutes are calculated and recorded
3. The session is marked as ended

Your data inside the container is preserved — next time you start a session, it picks up where you left off.

---

## Monthly Usage Limit

Each company has an allocated number of VIP minutes per month.

- **Final hour warning:** When you have approximately 60 minutes remaining, you'll receive a notification
- **Limit reached:** When all minutes are used, the session is force-stopped regardless of activity
- **Usage resets** at the start of each month

---

## Checking Usage

Go to your **VIP Dashboard** to see:

- Minutes used this month
- Minutes remaining
- Usage percentage
- Session history
