# Vue 3 SPA Setup Guide with Frappe UI

This document explains three different methods to set up a Vue 3 Single Page Application (SPA) inside a custom Frappe app.

This guide is created to avoid confusion and clearly explain when and why to use each method.

---

# Why Use Doppio?

Doppio is a Frappe app that simplifies SPA integration with Frappe.

It provides:

- Automatic Vue/React scaffolding
- Built-in Vite configuration
- Automatic proxy setup
- Automatic `website_route_rules` configuration
- Proper login session handling
- Production-safe routing

Without Doppio, developers often face:

- Login/session issues
- CSRF errors
- Proxy misconfiguration
- Incorrect production routing

Repository:
https://github.com/NagariaHussain/doppio  
Licensed under MIT License

---

# METHOD 1: Using Doppio (bench add-spa) — Recommended

Best for full-featured SPA with automatic routing and session handling.

## Step 1: Install Doppio

Inside your bench directory:

```bash
bench get-app https://github.com/NagariaHussain/doppio
bench --site <site-name> install-app doppio
```

---

## Step 2: Create Vue SPA

```bash
bench add-spa --app <your-app-name>
yarn add frappe-ui
yarn add -D tailwindcss postcss autoprefixer
```

Prompts:

- Enter SPA name (example: dashboard)
- Choose framework → Vue
- Choose JavaScript or TypeScript
- Enable Tailwind (optional)

This automatically:

- Creates Vue 3 project (Vite based)
- Configures Vue Router
- Sets proxy configuration
- Updates `hooks.py`
- Ensures session-safe SPA routing

---

## Step 3: Install & Run

```bash
cd <spa-folder>
yarn install
yarn dev
```

Access in browser:

```
http://<site>:8080
```

Use this method when:

- Building complete SPA
- Want automatic routing
- Need stable authentication
- Want production-ready structure

---

# METHOD 2: Using Doppio (bench add-frappe-ui)

This method scaffolds a Frappe UI starter template.

⚠ Do NOT use this together with `bench add-spa`.  
Choose only one Doppio method.

## Step 1: Install Doppio

```bash
bench get-app https://github.com/NagariaHussain/doppio
bench --site <site-name> install-app doppio
```

---

## Step 2: Add Frappe UI Starter

```bash
bench add-frappe-ui
```

You will be prompted for:

- App name
- Folder location

---

## Step 3: Install & Run

```bash
cd <generated-folder>
yarn install
yarn dev
```



Use this method when:

- You only want Frappe UI starter
- Lightweight setup is enough
- Testing UI quickly
- Not building complex SPA routing

---

---

# ⚠ Common Issue (Frappe UI + Vite CSS Import Error)

While using the **bench add-frappe-ui** method, you may encounter the following error:

```
[plugin:vite:css] [postcss] Missing "./src/style.css" specifier in "frappe-ui" package
```

### ✅ Cause

This occurs because recent versions of **frappe-ui** changed internal file paths, but some starter templates still reference older paths.

---

### ✅ Solution

Update the incorrect import paths using the following commands:

```bash
sed -i 's@frappe-ui/src/style.css@frappe-ui/style.css@g' frontend/src/index.css
sed -i 's@frappe-ui/src/tailwind/preset@frappe-ui/tailwind@g' frontend/tailwind.config.js
```

---

### 🔄 After Fix

Restart the development server:

```bash
yarn dev
```

The application should now run without CSS resolution errors.

---

### 💡 Recommendation

Always verify frappe-ui internal paths when upgrading versions, as starter templates may lag behind package updates.


# METHOD 3: Manual Setup (Without Doppio)

This method uses official Frappe UI starter.

Reference:
https://ui.frappe.io/docs/getting-started

---

## Step 1: Create Frontend Folder

Inside your custom app:

```bash
cd apps/<your-app-name>
npx degit netchampfaris/frappe-ui-starter frontend
cd frontend
yarn install
```

---

## Step 2: Configure Proxy

Edit `vite.config.js`:

```js
export default {
  server: {
    proxy: {
      "/api": {
        target: "http://localhost:8000",
        changeOrigin: true
      }
    }
  }
}
```

---

## Step 3: Update hooks.py

In your Frappe app:

```python
website_route_rules = [
    {"from_route": "/dashboard", "to_route": "dashboard"}
]
```

Restart bench:

```bash
bench restart
```

---

## Step 4: Run Development Server

```bash
npm run dev
```

Use this method when:

- You need full control
- Custom frontend architecture
- Advanced routing customization
- Deep understanding of Frappe routing

---

# Comparison

| Feature | add-spa | add-frappe-ui | Manual |
|----------|----------|---------------|---------|
| Vue Scaffold | Yes | Yes | Yes |
| Router Setup | Auto | Basic | Manual |
| Proxy Config | Auto | Partial | Manual |
| hooks.py Update | Auto | Limited | Manual |
| Session Handling | Stable | Stable | Must Configure |
| Production Build | Simplified | Simplified | Manual Setup |
| Complexity | Low | Low | Medium |

---

# Important Notes

- Do NOT run both `bench add-spa` and `bench add-frappe-ui`
- Restart bench after installation
- Ensure proxy port matches Frappe (usually 8000)
- Always run `npm install` before `npm run dev`

---

# Final Recommendation

For most team projects:

Use **Method 1 (bench add-spa with Vue)**.

It provides:

- Clean structure
- Automatic routing
- Stable authentication
- Production-safe deployment
