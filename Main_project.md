# PinFlow — Automated Pinterest Uploader Landing Page

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
 ├── index.html       # Main landing page
 ├── README.md        # This file
 └── uploader.py      # Pinterest automation script
```

---

## 🛠 Deployment Instructions (Landing Page)

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

## 📌 Pinterest Uploader Script
This repository now includes a **Python script** (`uploader.py`) that can automatically upload images to Pinterest, rotate through titles/descriptions from a CSV file, archive used items, and send updates to Discord.

### Configuration
Edit the constants at the top of `uploader.py`:
```python
PINTEREST_ACCESS_TOKEN = "YOUR_PINTEREST_ACCESS_TOKEN"
BOARD_ID = "YOUR_BOARD_ID"
DISCORD_WEBHOOK_URL = "YOUR_DISCORD_WEBHOOK"

IMAGE_FOLDER = "/path/to/images"
USED_FOLDER = "/path/to/used"
CSV_FILE = "/path/to/texts.csv"
USED_CSV_FILE = "/path/to/used_texts.csv"
POSTS_PER_DAY = 3
```

### Usage
Run manually:
```bash
python3 uploader.py
```

You can also schedule it via **cron** or a task scheduler to automate daily uploads.

### Script Logic
- Randomly select a title/description from `texts.csv` (and archive it).
- Take the next available image from the image folder.
- Upload to Pinterest via API.
- Move the used image to the `used` folder.
- Send a summary message to Discord (remaining posts, estimated last day).

### File: `uploader.py`
```python
import os
import csv
import random
import requests
from datetime import datetime, timedelta

# --- CONFIG ---
PINTEREST_ACCESS_TOKEN = "DEIN_PINTEREST_ACCESS_TOKEN"
BOARD_ID = "DEIN_BOARD_ID"
DISCORD_WEBHOOK_URL = "DEIN_DISCORD_WEBHOOK"

IMAGE_FOLDER = "/home/daniel/Pinterest_bot/images"
USED_FOLDER = "/home/daniel/Pinterest_bot/used"
CSV_FILE = "/home/daniel/Pinterest_bot/texts.csv"
USED_CSV_FILE = "/home/daniel/Pinterest_bot/used_texts.csv"
POSTS_PER_DAY = 3  # important for reach calculation

# --- Helper functions ---
def load_texts():
    with open(CSV_FILE, "r", encoding="utf-8") as f:
        reader = list(csv.DictReader(f))
    return reader

def save_texts(texts):
    if not texts:
        return
    with open(CSV_FILE, "w", encoding="utf-8", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=["title", "description"])
        writer.writeheader()
        writer.writerows(texts)

def archive_text(entry):
    write_header = not os.path.exists(USED_CSV_FILE)
    with open(USED_CSV_FILE, "a", encoding="utf-8", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=["title", "description"])
        if write_header:
            writer.writeheader()
        writer.writerow(entry)

# --- 1. Choose random title & description ---
def get_random_text():
    texts = load_texts()
    if not texts:
        return None, None
    entry = random.choice(texts)
    texts.remove(entry)
    save_texts(texts)
    archive_text(entry)
    return entry["title"], entry["description"]

# --- 2. Get next image ---
def get_next_image():
    files = [f for f in os.listdir(IMAGE_FOLDER) if f.lower().endswith((".png", ".jpg", ".jpeg"))]
    if not files:
        return None
    return os.path.join(IMAGE_FOLDER, sorted(files)[0])

# --- 3. Upload to Pinterest ---
def upload_to_pinterest(image_path, title, description):
    url = "https://api.pinterest.com/v5/pins"
    headers = {"Authorization": f"Bearer {PINTEREST_ACCESS_TOKEN}"}
    with open(image_path, "rb") as img:
        files = {"image": (os.path.basename(image_path), img, "image/png")}
        data = {"board_id": BOARD_ID, "title": title, "description": description}
        r = requests.post(url, headers=headers, data=data, files=files)
    return r.status_code, r.text

# --- 4. Move used image ---
def move_used_image(image_path):
    if not os.path.exists(USED_FOLDER):
        os.makedirs(USED_FOLDER)
    os.rename(image_path, os.path.join(USED_FOLDER, os.path.basename(image_path)))

# --- 5. Discord update ---
def send_discord_update():
    files = [f for f in os.listdir(IMAGE_FOLDER) if f.lower().endswith((".png", ".jpg", ".jpeg"))]
    texts_left = len(load_texts())

    remaining = min(len(files), texts_left)

    if remaining == 0:
        msg = "❌ No resources left: Either images or texts are missing!"
    else:
        days_left = remaining // POSTS_PER_DAY
        last_day = datetime.today() + timedelta(days=days_left)
        msg = f"📌 {remaining} posts remaining. Will last until approx. {last_day.strftime('%d.%m.%Y')} with {POSTS_PER_DAY} posts/day."

    payload = {"content": msg}
    requests.post(DISCORD_WEBHOOK_URL, json=payload)

# --- MAIN ---
if __name__ == "__main__":
    image_path = get_next_image()
    if not image_path:
        send_discord_update()
        exit()

    title, description = get_random_text()
    if not title or not description:
        send_discord_update()
        exit()

    status, resp = upload_to_pinterest(image_path, title, description)

    if status == 201:
        move_used_image(image_path)
    else:
        print("Error uploading:", resp)

    send_discord_update()
```

---

## ✅ Checklist Before Submitting to Pinterest
- [ ] Replace all placeholders in `index.html`.
- [ ] Configure `uploader.py` with your real Pinterest API token, board ID, and Discord webhook.
- [ ] Test uploads locally.
- [ ] Ensure privacy policy & Impressum are visible at your GitHub Pages URL.

---

## 📧 Contact
For issues or customization help, reach out at:
```
you@yourdomain.com
```
