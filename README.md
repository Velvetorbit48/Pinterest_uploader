# PinFlow — Automated Pinterest Uploader Landing Page

This repository hosts a **professional landing page** for a Pinterest Uploader project (PinFlow). The site is designed to run entirely on **GitHub Pages** and can be used to satisfy Pinterest Developer requirements (website + privacy policy + legal notice).

---

## 🚀 Features of the Landing Page
- Professional **HTML landing page** (`index.html`)
- Sections for **Hero, Features, How it works, Privacy, Impressum, FAQ, Get Started**
- Includes **sample Privacy Policy** and **Legal Notice** (placeholders to replace)
- Responsive, minimal CSS (no external dependencies)
- Ready for GitHub Pages deployment

---

## 📦 Repository Structure
```
/ (root)
 ├── index.html   # Main landing page
 └── README.md    # This file
```

---

## 🛠 Deployment Instructions

### 1. Fork or Clone the Repository
You can either fork this repository or clone it directly:
```bash
git clone https://github.com/<your-username>/pinterest-uploader-site.git
cd pinterest-uploader-site
```

### 2. Customize the Page
Open `index.html` and update:
- **[YOUR NAME / COMPANY]** → Replace with your real name or business name.
- **[ADDRESS]** → Your postal address (required for Impressum/legal notice).
- **[CONTACT EMAIL]** → Your real contact email.
- **Effective date** in Privacy Policy → e.g., `2025-01-01`.

### 3. Commit & Push Changes
```bash
git add index.html
git commit -m "Customize landing page with my details"
git push origin main
```

### 4. Enable GitHub Pages
1. Go to your repository on GitHub.
2. Navigate to **Settings → Pages**.
3. Under **Source**, select **Branch: main** and **Folder: /(root)**.
4. Click **Save**.

After a few minutes, your site will be live at:
```
https://<your-username>.github.io/pinterest-uploader-site/
```

### 5. Register with Pinterest Developer
1. Create a **Pinterest Business Account**.
2. Register a new app in the [Pinterest Developer Portal](https://developers.pinterest.com/).
3. Use your GitHub Pages URL as:
   - **Website URL**
   - **Redirect URI** (e.g., `https://<your-username>.github.io/pinterest-uploader-site/auth/callback`)
4. Submit for review.

---

## ✅ Checklist Before Submitting to Pinterest
- [ ] Replace all placeholders (`[YOUR NAME]`, `[CONTACT EMAIL]`, etc.).
- [ ] Double-check that Impressum and Privacy Policy are accessible.
- [ ] Ensure the site loads correctly at your GitHub Pages URL.
- [ ] Test OAuth redirect flow (if implemented).

---

## 📌 Notes
- This project is **independent** and **not affiliated with Pinterest, Inc.**
- Using the Pinterest API is subject to Pinterest’s **Developer Agreement** and **Acceptable Use Policy**.
- For legal compliance, adapt the Privacy Policy with a lawyer or official generator.

---

## 📧 Contact
For issues or customization help, reach out at:
```

