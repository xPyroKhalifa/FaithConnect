# FaithConnect – Email Confirmation Page (Simple HTML)

A minimal, production-ready HTML page that verifies Supabase email confirmation links and shows a success message. Intended for hosting on **Vercel** and used by your **React Native** app’s signup flow.

---

## Features
- Plain HTML + vanilla JS (no frameworks)
- Uses `@supabase/supabase-js` v2 CDN
- Exchanges `token_hash` (or `token`) with `verifyOtp({ type: 'email' })`
- Shows a simple success/error message and auto-redirects to `/dashboard`

---

## File Structure
```
.
├── index.html               # or confirm.html – the page you’ll deploy
├── README.md                # this guide
```

> If you prefer a different filename (e.g., `confirm.html`), update your **emailRedirectTo** to point to that path.

---

## 1) Configure Supabase Auth URLs
In the Supabase Dashboard:
1. Navigate to **Auth → URL Configuration**.
2. Set **Site URL** to your Vercel domain, e.g., `https://your-vercel-domain.vercel.app`.
3. Add your domain to **Redirect URLs**, e.g., `https://your-vercel-domain.vercel.app` (and any preview URLs you need).

> These settings ensure the email link redirects to your hosted page and is allowed by Supabase.

---

## 2) Use the Confirmation URL in your email template
Keep the built-in variable in Supabase email templates:
```html
<p><a href="{{ .ConfirmationURL }}">Confirm Email</a></p>
```
Supabase will build a link like:
```
https://PROJECT_REF.supabase.co/auth/v1/verify?token_hash=...&type=email&redirect_to=https://your-vercel-domain.vercel.app
```

---

## 3) Pass `emailRedirectTo` in your signup call
From your app (React Native or web):
```ts
await supabase.auth.signUp({
  email,
  password,
  options: {
    emailRedirectTo: 'https://your-vercel-domain.vercel.app' // or /confirm
  }
});
```

---

## 4) Add your Supabase project values to the page
Open `index.html` and set:
```js
const SUPABASE_URL = 'https://YOUR_PROJECT_REF.supabase.co';
const SUPABASE_ANON_KEY = 'YOUR_ANON_KEY';
```
> Use your **project ref** and **anon key** from the Supabase Dashboard.

---

## 5) Deploy to Vercel
### Option A: One‑file project
1. Create a new project in Vercel.
2. Upload `index.html` (or `confirm.html`).
3. Deploy. Your page will be available at `https://your-vercel-domain.vercel.app`.

### Option B: Git repo
1. Create a repo containing this HTML and README.
2. Import the repo into Vercel.
3. Deploy (Vercel will serve `index.html` by default).

---

## 6) How the page works
- Reads `token_hash` (or `token`) and optional `type` from the URL query.
- Calls:
```js
await supabase.auth.verifyOtp({ token_hash, type: 'email' });
```
- On success: shows **Email verified! You are now signed in.** and auto-redirects to `/dashboard`.
- On error: shows a friendly failure message.

You can change the redirect target by editing:
```js
setTimeout(() => { window.location.href = '/dashboard'; }, 1500);
```

---

## 7) Testing
1. In a test environment, sign up with a real email.
2. Click the **Confirm Email** link sent by Supabase.
3. Ensure the page displays **verified** and redirects.
4. Check your Supabase **Auth → Users** to confirm the email is marked as verified.

---

## 8) Troubleshooting
- **Redirects to wrong domain**: Update **Site URL** and **Redirect URLs** in Supabase to your Vercel domain.
- **Invalid or missing token**: Make sure you used `{{ .ConfirmationURL }}` in the template and passed `emailRedirectTo` in your signup call.
- **Link expired**: Ask the user to request a new link (magic links expire after a period).
- **CORS/storage issues**: Ensure you’re using the correct **Anon Key** and project URL; the client must be allowed to set the session.

---

## 9) Customization
- Colors: edit the CSS variables or inline styles to match FaithConnect’s palette.
- Copy: adjust text in the `.container` for your tone.
- Buttons: change `/dashboard` to your preferred landing path.

---

## 10) Security Notes
- Keep your **service role key** private. Only the **anon key** is used here.
- Consider adding a **Content Security Policy (CSP)** if you later expand the page.

---

## License
You may use, modify, and distribute this page within the FaithConnect project.
