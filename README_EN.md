# The Benne Board

> A self-hosted, privacy-first personal workspace — notes, tasks, finances, habits, goals and a lunar calendar. All in one elegant PWA.

![GUI](screenshots/gui.png)

---

> 🌐 **Language note:** This app is currently available in German only. An English version is planned for a future update. In the meantime, you can use your browser's built-in translation feature (Google Chrome: right-click → Translate to English) to navigate the interface.

---

## What is this?

The Benne Board is a personal all-in-one workspace that you host on your own server — for yourself, your family and friends. No cloud, no subscriptions, no third-party servers. Your data belongs to you.

The app runs as a Progressive Web App (PWA), meaning it works in any browser and can be installed on your smartphone like a native app. The backend runs on [PocketBase](https://pocketbase.io/), a lightweight self-hosted database with built-in user management.

---

## Features

### 📝 Notes
- Create, edit and archive notes
- Markdown with live preview
- Tags and full-text search

### ✅ Tasks
- Kanban board (Open / In Progress / Done)
- Subtasks and checklists within each task
- Recurring tasks (daily / weekly / monthly)
- Priorities and due dates

### 💶 Ledger
- Track income and expenses
- Categories and monthly overview
- Recurring entries (rent, subscriptions)
- CSV export

### 🔥 Habits
- Create and check off daily habits
- Streak display
- Monthly heatmap in GitHub style

### 🎯 Goals
- Goals with progress bar
- Target value, current value, unit and deadline
- Status: Active / Achieved / Abandoned

### 📅 Calendar
- Gregorian calendar with task integration
- **Harmonic Earth Calendar** – 13 months of 28 days, year starts in March
- Live moon phase calculation based on Jean Meeus algorithm
- Garden influence by moon phase (clickable cards)
- Info section about lunar and solar calendars

### ⚙️ Technical
- Single-file PWA – everything in one `index.html`
- Offline-first with background sync
- Multi-user with separated data
- Dark mode (automatic + manual)
- Keyboard shortcuts
- Import / Export as JSON

---

## Screenshots

| Notes | Tasks | Ledger | Calendar |
|-------|-------|--------|----------|
| ![Notes](screenshots/notes.png) | ![Tasks](screenshots/tasks.png) | ![Ledger](screenshots/ledger.png) | ![Calendar](screenshots/kalender.png) |

---

## Installation

### Requirements
- A server (e.g. home server, Raspberry Pi, VPS)
- Docker
- Optional: [Tailscale](https://tailscale.com/) for secure access from anywhere

---

### Step 1 – Start PocketBase

```bash
docker run -d \
  --name pocketbase \
  --restart unless-stopped \
  -p 8090:8090 \
  -v /opt/pocketbase/pb_data:/pb/pb_data \
  ghcr.io/pocketbase/pocketbase \
  /pb/pocketbase serve --http=0.0.0.0:8090
```

Then open the admin panel and create your superuser account:
```
http://YOUR-IP:8090/_/
```

### Correct example

![Example](screenshots/beispiel.png)

---

### Step 2 – Create Collections

In the PocketBase admin panel under **Collections**, create the following collections with these fields:

**notes**
| Field | Type |
|-------|------|
| title | Text |
| content | Text |
| tags | Text |
| archived | Bool |
| user | Relation → users |

**tasks**
| Field | Type |
|-------|------|
| title | Text |
| description | Text |
| due_date | Date |
| priority | Text |
| status | Text |
| subtasks | JSON |
| recurring | Text |
| user | Relation → users |

**ledger_entries**
| Field | Type |
|-------|------|
| type | Text |
| amount | Number |
| description | Text |
| category | Text |
| date | Date |
| recurring | Text |
| user | Relation → users |

**habits**
| Field | Type |
|-------|------|
| name | Text |
| icon | Text |
| frequency | Text |
| target_per_week | Number |
| user | Relation → users |

**habit_logs**
| Field | Type |
|-------|------|
| habit | Relation → habits |
| date | Date |
| done | Bool |
| user | Relation → users |

**goals**
| Field | Type |
|-------|------|
| title | Text |
| description | Text |
| current_value | Number |
| target_value | Number |
| unit | Text |
| deadline | Date |
| status | Text |
| user | Relation → users |

---

### Step 3 – Set API Rules

For each collection, go to the **API Rules** tab and enter the following for all 5 rules:
```
@request.auth.id != ""
```

---

### Step 4 – Host the App

```bash
docker run -d \
  --name benne-board \
  --restart unless-stopped \
  -p 8085:80 \
  -v /path/to/index.html:/usr/share/nginx/html/index.html:ro \
  nginx:alpine
```

Open the app:
```
http://YOUR-IP:8085
```

---

### Step 5 – Configure the App

On first launch, the app will ask for your PocketBase URL:
```
http://YOUR-IP:8090
```

Then log in with your PocketBase account.

---

### Access from anywhere with Tailscale (recommended)

1. Install [Tailscale](https://tailscale.com/) on your server and your devices
2. Use the Tailscale IP instead of your local IP – no port forwarding, no reverse proxy needed
3. Install the PWA on your smartphone via "Add to Home Screen"

---

## Creating Users

Additional accounts can be created in the PocketBase admin panel under **Users**. Each user can only see their own data.

---

## License

GPL-3.0 © [Bennedict](https://github.com/Bennedict)

---

## Author

Built by **Bennedict** & **Claude**

