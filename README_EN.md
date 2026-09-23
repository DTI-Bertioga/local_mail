# Local Mail Plugin for Moodle (Customized / Modified Version)

[Versão em Português](README.md)

This is a **modified and enhanced version** of the official [Local Mail for Moodle](https://moodle.org/plugins/local_mail) plugin.

The original plugin provides a modern webmail-like interface for communication between users within the same course. This version extends the original project by introducing **site-wide (system-level) global messaging** for all authenticated users, along with critical database fixes, refined user search filters, and improved frontend attachment filemanager resilience.

---

## 🌟 Key Differences and Enhancements Compared to Original Project

### 1. 📢 System-Level Site-Wide Messaging (`SITEID`)
* **Original Project:** Messaging was strictly limited to courses where users were enrolled (`courseid > 1`).
* **Modified Version:** Integrates Moodle System Context (`SITEID = 1`). All authenticated Moodle users can now send and receive site-wide messages without needing to be enrolled in a specific course, while retaining the ability to filter messages by course when desired.

### 2. 🔍 Refined Global User Search
* **Original Project:** User search performed direct joins with course enrolment tables (`user_enrolments`), making site-wide user search impossible.
* **Modified Version:** For global searches (`SITEID`), enrolment joins are bypassed. The search filter automatically excludes:
  * The current sender user.
  * Suspended, deleted, or unconfirmed users.
  * The guest user (`$CFG->siteguest`).

### 3. 🛡️ Safe Course Data Deletion (`delete_course_data`)
* **Original Project:** The `delete_course_data()` function contained a generic subquery that deleted message references globally, accidentally affecting message threads in other courses.
* **Modified Version:** Restored strict `WHERE courseid = :courseid` filtering in `classes/message.php`, ensuring course data deletion does not affect global or cross-course message threads.

### 4. 🧹 Duplicate Course Entries Cleanup
* **Original Project:** User preferences and course dropdown menus could display duplicate System context (`SITEID`) entries.
* **Modified Version:** `get_preferences_raw()` in `classes/external.php` cleans up the course list, ensuring the System course (`coresystem`) appears uniquely and cleanly in the UI.

### 5. 📎 Attachment Manager Resilience (SPA Filemanager)
* **Original Project:** AJAX form initialization in a single-page application (Svelte SPA) could freeze the attachment container in an infinite loading state (`fm-loading`), due to `Y.on('domready')` listeners not firing post-initial page load or unhandled exceptions from 3rd-party rich text editors (e.g. `tiny_htmlbootstrapeditor` / `M.recit`).
* **Modified Version:** 
  * Injected safe JavaScript fallback stubs for `window.M.recit`, preventing asynchronous scripts from breaking form initialization.
  * Implemented dynamic document execution state checks `(document.readyState === "complete" || document.readyState === "interactive")`, allowing `M.form_filemanager.init` to execute instantly on SPA AJAX requests.

---

## 🚀 Installation Methods

### Option 1: Installation via Moodle Web Interface (GUI / .ZIP Package)
1. Run `git clone https://github.com/DTI-Bertioga/local_mail.git mail` and compress the created directory into a ZIP file (e.g., `mail.zip`).
2. Log in to your Moodle as an Administrator.
3. Navigate to: **Site administration > Plugins > Install plugins** (`/admin/tool/installplugin/index.php`).
4. Drag and drop the **`.zip`** file into the **Zip package** field.
5. If prompted for plugin type, select **Local plugin (`local`)**.
6. Click **Install plugin from the ZIP file**.
7. Confirm the prompt and click **Upgrade Moodle database now**.

---

### Option 2: Manual Installation on Server
1. Download or clone this repository directly into your Moodle's local plugins directory:
   ```bash
   git clone https://github.com/DTI-Bertioga/local_mail.git /path/to/moodle/local/mail
   ```
2. Navigate to your Moodle Site Administration (**Site administration > Plugins > Install Plugins**) to complete the extension installation.

---

## 🛠️ Requirements
* **Moodle:** 4.x or 5.x
* **PHP:** Compatible with your Moodle version (PHP 8.1+)

---

## 📄 License and Copyright

This plugin is distributed under the terms of the **GNU General Public License v3.0 or later**.

### Original Project Credits:
* © 2012-2014 Institut Obert de Catalunya
* © 2014-2023 Marc Català <reskit@gmail.com>
* © 2016-2025 Albert Gasset <albertgasset@fsfe.org>
* © 2023-2024 Proyecto UNIMOODLE
