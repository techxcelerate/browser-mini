<p align="center">
  <img src="images/logo.png" alt="NTXM Browser Mini" width="120" />
</p>

<h1 align="center">NTXM Browser Mini</h1>

<p align="center">
  <strong>A programmable, C++ desktop browser built with Qt 6 and Chromium Embedded Framework (CEF), featuring an embedded REST Automation Server.</strong>
</p>

<p align="center">
  <a href="https://github.com/techxcelerate/browser-mini/releases/"><img src="https://img.shields.io/badge/Releases-Download-blue?style=flat-square" alt="Releases" /></a>
  <img src="https://img.shields.io/badge/License-Proprietary-red?style=flat-square" alt="License" />
</p>

---

## 🚀 Overview

NTXM Browser Mini is a lightweight, privacy-first desktop web browser designed from the ground up for developers, QA testers, and AI agents. Unlike standard consumer browsers that require bulky drivers and heavy headful processes to orchestrate, NTXM Browser Mini runs a native, multi-threaded **REST Automation Server** (`http://127.0.0.1:9090`) out-of-the-box. 

This enables you to control tabs, trigger navigation, and scrape fully rendered DOM contents programmatically using simple, lightweight HTTP requests from any language.

<p align="center">
  <img src="images/home-page.png" alt="NTXM Browser Mini Workspace" width="700" style="border-radius: 8px;" />
</p>

---

## ✨ Key Features

*   **Chromium Engine (CEF)**: Delivers rendering compliance, security, and performance utilizing Chromium Embedded Framework.
*   **Qt 6 Interface Shell**: Built with native C++ Qt 6 for responsive, low-overhead window and tab management.
*   **Embedded REST Server**: Background thread listening on port `9090` (powered by `cpp-httplib`), bypassing heavy drivers like WebDrivers.
*   **Multi-Tab Architecture**: Open, close, switch, and inspect multiple tabs programmatically.
*   **Wait-for-Load Rendering**: Synchronous navigation endpoint (`/navigate_content`) that blocks responses until client-side JS finishes rendering, resolving SPA scraping issues.
*   **Telemetry-Free & Local**: Zero remote analytics, zero background cloud sync, and fully local offline execution.

---

## 📦 Releases

You can download the latest precompiled binaries and installers for NTXM Browser Mini directly from the [GitHub Releases](https://github.com/techxcelerate/browser-mini/releases/) page.

---

## 📸 Interface Preview

<p align="center">
  <img src="images/image-01.png" alt="Multi-tab automation" width="48%" style="border-radius: 6px; margin-right: 2%;" />
  <img src="images/image-02.png" alt="Lightweight UI shell" width="48%" style="border-radius: 6px;" />
</p>

---

## 📡 REST Automation API Quick Reference

The local server exposes endpoints on `http://127.0.0.1:9090`. All endpoints targeting page/tab contents support optional query parameters `id` (unique lifetime ID) or `index` (0-based order).

### Tab Management

#### List Open Tabs
*   `GET /tabs` — Returns list of all active tabs in JSON.
*   *Example*:
    ```bash
    curl http://127.0.0.1:9090/tabs
    ```

#### Open New Tab
*   `POST /new_tab` — Opens a new tab, optionally navigating to a URL.
*   *Example*:
    ```bash
    curl -X POST http://127.0.0.1:9090/new_tab -d "https://github.com"
    ```

#### Close Tab
*   `POST /close_tab` — Closes target tab by index or ID.
*   *Example*:
    ```bash
    curl -X POST http://127.0.0.1:9090/close_tab?index=1
    ```

#### Switch Tab
*   `POST /activate_tab` — Switches focus to targeted tab.
*   *Example*:
    ```bash
    curl -X POST http://127.0.0.1:9090/activate_tab?id=2
    ```

### Navigation & Content Scraper

#### Asynchronous Navigation
*   `POST /navigate` — Commands tab to load a URL asynchronously.
*   *Example*:
    ```bash
    curl -X POST "http://127.0.0.1:9090/navigate?id=1" -d "https://wikipedia.org"
    ```

#### Get Current URL
*   `GET /url` — Returns active tab URL as plain text.

#### Get DOM Source HTML
*   `GET /content` — Returns raw HTML of the current DOM.
*   *Example*:
    ```bash
    curl http://127.0.0.1:9090/content?id=1
    ```

#### Synchronous Navigation (Scraper Mode)
*   `POST /navigate_content` — Loads a URL, waits for CEF load completion (or custom `delay` in ms), and returns the fully compiled HTML.
*   *Example*:
    ```bash
    curl -X POST "http://127.0.0.1:9090/navigate_content?delay=1500" -d "https://news.ycombinator.com"
    ```

---

## 🐍 Automation Blueprint (Python)

Orchestrating browser workflows requires zero complex configurations. Below is a complete script using only Python's standard `requests` module:

```python
import requests
import time

BASE_URL = "http://127.0.0.1:9090"

# 1. Open a new tab pointing to Hacker News
new_tab = requests.post(f"{BASE_URL}/new_tab", data="https://news.ycombinator.com").json()
tab_id = new_tab.get("id")
print(f"Tab created successfully with ID: {tab_id}")

# 2. Wait for rendering and extract content
time.sleep(2)
html = requests.get(f"{BASE_URL}/content?id={tab_id}").text

# 3. Print parsed headlines
for line in html.split("\n"):
    if 'class="titleline"' in line:
        headline = line.split('">')[2].split('</a>')[0]
        print(f" Headline: {headline}")

# 4. Close the automation tab
requests.post(f"{BASE_URL}/close_tab?id={tab_id}")
print("Automation completed and tab closed.")
```

---

## 📄 License & Terms of Use

NTXM Browser Mini is proprietary software. All rights reserved. 

*   **No Source Code**: This repository does not distribute source code. It is used solely for hosting compiled application releases, installer distribution, and issue tracking.
*   **Restrictions**: You are strictly prohibited from decompiling, reverse engineering, disassembling, or attempting to derive the source code of the binary files.
*   **Usage**: The software is provided as-is for personal and professional automation, testing, and browsing workflows. Modifying, repackaging, or redistributing the binary executables without explicit written authorization is strictly prohibited.
