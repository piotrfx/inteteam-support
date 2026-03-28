# Entering a VIP Session

## Starting a Session

1. Log into OOR-HQ
2. Go to your **VIP Dashboard**
3. Click **Enter VIP**
4. Wait a few seconds while the system:
   - Starts your container (if it was stopped)
   - Generates a secure token
   - Redirects you to your VIP URL
5. A full browser appears — you can now browse any website

---

## Reconnecting

If you close your browser tab or lose connection:

1. Go back to OOR-HQ
2. Click **Enter VIP** again
3. You'll reconnect to the same session — your open tabs are preserved

This works because the browser process keeps running on the server even when you disconnect.

---

## Session Heartbeat

While you're in a VIP session, the OOR-HQ tab sends a heartbeat every 30 seconds to confirm you're still active. Keep the OOR-HQ tab open (it can be in the background).

If the heartbeat stops (e.g. you close all OOR-HQ tabs), the idle timer begins.
