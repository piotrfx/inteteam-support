# Two-Factor Authentication (2FA)

Add an extra layer of security to your account. When enabled, you'll need both your password and a code from your phone to log in.

**Settings -> Security tab -> Two-Factor Authentication**, or navigate to `/admin/settings/two-factor`.

---

## Setting Up 2FA

1. Go to **Settings -> Security -> Two-Factor Authentication**
2. Click **Enable Two-Factor Authentication**
3. You'll see a **QR code** on screen
4. Open your authenticator app on your phone:
   - [Google Authenticator](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2) (Android / iOS)
   - [Authy](https://authy.com/) (Android / iOS / Desktop)
   - [Microsoft Authenticator](https://www.microsoft.com/en-us/security/mobile-authenticator-app) (Android / iOS)
5. Scan the QR code with your authenticator app
6. Enter the **6-digit code** shown in the app to confirm
7. You'll receive **recovery codes** — save these somewhere safe

---

## Recovery Codes

After enabling 2FA, you're given a set of one-time recovery codes. These are your backup if you lose access to your authenticator app.

**Important:**
- Each code can only be used **once**
- Store them somewhere safe (password manager, printed in a secure location)
- If you use most of your codes, generate new ones from the settings page

To view or regenerate codes: **Settings -> Security -> Two-Factor Authentication -> Recovery Codes**

---

## Logging In with 2FA

1. Enter your email and password as normal
2. You'll see the **Two-Factor Authentication** screen
3. Enter the 6-digit code from your authenticator app
4. Optionally tick **Remember this device for 30 days**
5. Click **Verify**

If you don't have your phone:
1. Click **Use a recovery code**
2. Enter one of your saved recovery codes
3. Click **Verify**

---

## Disabling 2FA

1. Go to **Settings -> Security -> Two-Factor Authentication**
2. Click **Disable Two-Factor Authentication**
3. Confirm

You'll no longer be asked for a code when logging in.
