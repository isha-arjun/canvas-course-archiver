# Canvas Course Archiver

A Chrome extension (Manifest V3) that archives your accessible Canvas LMS courses—including past enrollments when your school allows access—so you can keep pages, assignments, announcements, and files offline.

**It uses your existing signed-in Canvas browser session. It never asks for, collects, or stores your password or API tokens.**

## Requirements

- Google Chrome on macOS 12.7.6 (or later Chrome on other platforms)
- Access to your school’s Canvas site in a browser tab
- Ability to install an **unpacked** extension (Developer mode)

No Terminal, Python, Node.js, or code editing is required to use the extension.

## Installation (macOS / Chrome)

1. Download and unzip this project folder so you have a directory named `canvas-course-archiver` containing `manifest.json`, `popup.html`, `js/`, `css/`, `icons/`, and `lib/`.
2. Open Chrome and go to: `chrome://extensions`
3. Turn **on** “Developer mode” (toggle in the top-right).
4. Click **Load unpacked**.
5. Select the `canvas-course-archiver` folder (the one that contains `manifest.json`).
6. The extension icon (blue “C”) should appear in the toolbar. Pin it if you like.

## How to use

1. **Open Canvas** in a normal Chrome tab and **sign in** to your school account. Stay signed in.
2. Click the **Canvas Course Archiver** extension icon.
3. Click **1. Connect to Canvas**.  
   - Chrome will ask for permission to access your Canvas domain—allow it.  
   - The extension reads the domain from the active (or a Canvas-looking) tab.
4. Click **2. Discover Courses**.  
   - Lists active and completed (past enrollment) courses the API returns for you.
5. Select a course in the list.
6. Click **3. Scan Selected Course** to inventory modules, pages, assignments, files, announcements, and quizzes (metadata).
7. Click **4. Archive Selected Course** to download content into the extension’s local storage (IndexedDB).
8. Optionally click **Archive All Accessible Courses** (long-running).
9. Use **Pause / Resume / Cancel** during scan or archive if needed. Progress is saved so you can resume later.
10. Click **5. Export Archive (ZIP)**.  
    - Choose where to save the ZIP.  
    - Extract it and open `index.html` in any browser for an offline course browser.

## What is archived

| Content | Notes |
|--------|--------|
| Course list | Active + completed when permitted |
| Modules & module items | Structure and metadata |
| Wiki pages | Title + body (HTML sanitized) |
| Assignments | Name, due date, points, description |
| Announcements | Title + message |
| Files | Downloaded when the file URL is accessible |
| Quizzes | Metadata only (questions often restricted) |
| Syllabus | If present on the course |

External URLs, LTI tools, embedded videos, and many third-party tools are **recorded with their original links** and flagged as not available offline.

## Permissions explained

- **storage / unlimitedStorage** — Save course inventory and archived content on your device.
- **downloads** — Save the export ZIP where you choose.
- **tabs / scripting** — Detect the Canvas tab you are using.
- **optional host permission** for your Canvas domain — Requested only when you click Connect, so the extension can call the Canvas API using your existing session cookies.

Nothing is sent to third-party servers. There is no analytics, advertising, or remote upload.

## Troubleshooting

| Problem | What to try |
|--------|-------------|
| “No Canvas tab found” | Open your Canvas site, sign in, leave that tab open, click Connect again. |
| “Session expired” / 401 | Sign in again in the Canvas tab, then Connect. |
| “Permission denied” | Allow the host permission when Chrome prompts. Some schools block API access for certain roles. |
| Empty past enrollments | Your school may restrict past course access; the extension only sees what Canvas returns. |
| Export fails | Try again; ensure free disk space. Large archives need time and memory. |
| Extension won’t load | Confirm you selected the folder that contains `manifest.json`. Check the Errors button on `chrome://extensions`. |

## Known limitations

- Only content **you are allowed to access** is archived. The extension does not bypass school restrictions.
- Quiz questions, some discussion threads, and certain files may be blocked by permissions.
- Streamed videos and external embeds will not work offline; original links are preserved in the archive.
- Very large courses or many file downloads may hit browser memory limits; use Pause and archive course-by-course if needed.
- This extension was built against **documented Canvas LMS REST API** endpoints (`/api/v1/courses`, modules, pages, assignments, discussion_topics, files, quizzes). Behavior can vary by Canvas version and institutional settings.
- **No live testing against a real school Canvas instance was performed in this environment.** Pagination, rate limits, and permission errors are handled according to public Canvas API documentation.

## Project structure

```
canvas-course-archiver/
├── manifest.json
├── popup.html
├── README.md
├── css/
│   └── popup.css
├── icons/
│   ├── icon16.png
│   ├── icon48.png
│   └── icon128.png
├── lib/
│   └── jszip.min.js          (bundled JSZip 3.10.1)
└── js/
    ├── background.js         (service worker)
    ├── popup.js
    ├── api.js                (Canvas REST client)
    ├── archiver.js           (scan / archive / pause)
    ├── export.js             (ZIP + offline index)
    └── storage.js            (IndexedDB + chrome.storage)
```

## Security notes

- Archived HTML is sanitized (scripts and inline event handlers removed) before storage and before display in the offline viewer.
- Do not open untrusted HTML from other sources inside the extension.
- Clear local data from the popup when you no longer need the in-browser cache.

## License

This project is provided as-is for personal archival of content you are authorized to access. Respect your institution’s terms of use and copyright.
