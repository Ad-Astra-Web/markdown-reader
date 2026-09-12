# Comprehensive Codebase Audit Report

**Target Workspace:** `/Users/lei/Downloads/adastra/manualtest`  
**Date of Audit:** September 10, 2026 (Updated to incorporate newly added `adastra2023`, `adastra2024`, `adastra2025`, and `admin` portals)  
**Scope:**
1. **Complete Audit of Unused / Never Called Files** (including dead pages, draft versions, abandoned test suites, uncalled GPP endpoints, 0-byte scripts, leftover archive zips, server config artifacts, and dormant backdoors).
2. **Complete Audit of Database Calling Files** (separating files calling via `connect.php` from those with hardcoded database connections, connection definition files, and custom wrapper layers).

> [!NOTE]
> **Studio File Manager Note:** As noted by the system administrator, `studio-file-manager` iterations for 2026 and earlier (`studio-file-manager`, `studio-file-manager-2023` through `2026`) were intentionally excluded from download due to their massive storage size (~100+ GB of student photoshoot media). `studio-file-manager-2027` is present and audited.

---

## Table of Contents
1. [Executive Summary](#executive-summary)
2. [Section 1: All Files That Are Never Used or Called](#section-1-all-files-that-are-never-used-or-called)
   - [1.1 Critical Security Alert: Obfuscated Backdoor Web Shell](#11-critical-security-alert-obfuscated-backdoor-web-shell)
   - [1.2 Legacy & Orphaned Root Scripts](#12-legacy--orphaned-root-scripts)
   - [1.3 Obsolete Version Iterations, Drafts & 0-Byte Files](#13-obsolete-version-iterations-drafts--0-byte-files)
   - [1.4 Uncalled Test, Scratch, and Diagnostic Scripts](#14-uncalled-test-scratch-and-diagnostic-scripts)
   - [1.5 Abandoned Cloned Test Suites & Folders](#15-abandoned-cloned-test-suites--folders)
   - [1.6 Dead Cloned Backend Endpoints in GPP Modules (510 Files)](#16-dead-cloned-backend-endpoints-in-gpp-modules-510-files)
   - [1.7 Leftover Compressed Archive Packages (15 ZIP Files, ~467 MB)](#17-leftover-compressed-archive-packages-15-zip-files-467-mb)
   - [1.8 Backup, Upgrade, and Server Metadata Artifacts](#18-backup-upgrade-and-server-metadata-artifacts)
   - [1.9 Defunct / Empty Directories](#19-defunct--empty-directories)
   - [1.10 Comprehensive Cleanup Action Plan](#110-comprehensive-cleanup-action-plan)
3. [Section 2: Complete Database Connection and Call Audit](#section-2-complete-database-connection-and-call-audit)
   - [2.1 Architectural Overview of Database Connectivity](#21-architectural-overview-of-database-connectivity)
   - [2.2 Files with Hardcoded Database Connections (46 Files)](#22-files-with-hardcoded-database-connections-46-files)
   - [2.3 Database Connection Definition Files (connect.php - 45 Files)](#23-database-connection-definition-files-connectphp---45-files)
   - [2.4 Custom Database Wrappers and Their Callers (25 Files)](#24-custom-database-wrappers-and-their-callers-25-files)
   - [2.5 Files Calling the Database via connect.php (1,354 Files)](#25-files-calling-the-database-via-connectphp-1354-files)
   - [2.6 Master Database and Credential Inventory Matrix](#26-master-database-and-credential-inventory-matrix)

---

## Executive Summary

A comprehensive repository-wide static and dependency-graph analysis was conducted across `/Users/lei/Downloads/adastra/manualtest`. The codebase spans over **34,000 total files** across 24 distinct yearbook campaigns (`adastra2023`, `adastra2024`, `adastra2025`, `adastra2026`, `adastra2027`, `fragment202122`, `obra2019`), graduation photo packages (`gpp_2023` through `gpp_2026`, `gpp_claiming`, `gpp_fragment`), ticketing portals (`2026ticket`, `2027ticket`), promotional sites (`EMBARK7` through `embark11`), and the central administrative console (`admin`).

### High-Level Summary Statistics (Updated with adastra2023..2025 and admin)

| Category | Count | Key Description |
| :--- | :---: | :--- |
| **Total PHP Files in Repository** | **3,423 files** | Total PHP codebase analyzed across all active and legacy modules. |
| **Total Files Interacting with Database** | **1,470 files** | Analyzed across direct, wrapper, and include patterns. |
| ↳ Files calling DB via `connect.php` | **1,354 files** | Include `connect.php` and query global connection variables (`$conn`, `$conn2026`, etc.). |
| ↳ Files with Hardcoded Direct Connections | **46 files** | Directly execute `mysqli_connect` / `new mysqli` without using `connect.php`. |
| ↳ Database Connection Definitions (`connect.php`) | **45 files** | 8 form POST action handlers + 37 central config includes. |
| ↳ Custom Database Wrappers & Callers | **25 files** | 4 wrapper classes (`database_master.php`, `databaseWrapper.php`, `connection.php`) + 21 callers. |
| **Total Unused / Never Called Files** | **1,000+ files** | Identified through dependency graph and reference tracking. |
| ↳ Unused Backend Endpoints Cloned into GPP Modules | **510 files** | 85 unreferenced PHP scripts copied into each of the 6 GPP folders. |
| ↳ Abandoned Test Environments | **278 files** | `fragmenttest/` (187 files) and `recruitmentTest/` (91 files). |
| ↳ Obsolete Page Versions, Drafts & 0-Byte Files | **100+ files** | Superseded revisions (`login2..3`, `passreqdupe`, `deleteAppointment.php` 0b, etc.). |
| ↳ Diagnostic & Scratch Scripts | **60+ files** | Ad-hoc connection and debug tests (`testCheckIn.php`, `testblast.php`, `testt.php`). |
| ↳ Leftover Compressed Archive Bundles (ZIPs) | **15 files (~467 MB)** | Leftover zip archives including 206MB proofUploads.zip and 98MB dashboard.zip. |
| ↳ Backup, Upgrade & Server Config Artifacts | **37 files (+311MB logs)** | `.bak`, `.phpupgrader.*`, `php.ini.ea4bak`, `.ftpquota`, `error_log` (303MB root + 20 admin logs). |

---

## Section 1: All Files That Are Never Used or Called

This section details every file identified across the repository that is never called, never referenced in navigation, never invoked by forms or AJAX, or represents obsolete dead weight.

### 1.1 Critical Security Alert: Obfuscated Backdoor Web Shell

> [!CAUTION]
> **CRITICAL SECURITY RISK: MALICIOUS FILE DETECTED**  
> The following file is an obfuscated PHP web shell / backdoor Trojan uploaded to the server, disguised as a WordPress login page and hidden inside a favicon asset directory. It is not called by any application flow and must be quarantined or deleted immediately.

- **Path:** [`fragment202122/favicon_io/wp-loginx3.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/favicon_io/wp-loginx3.php)
  - **File Type:** Obfuscated PHP script
  - **Capabilities Detected:** ASCII-obfuscated execution of `move_uploaded_file`, `file_get_contents`, `unlink`, `chmod`, `session_start`, arbitrary file upload, file browsing, and shell evaluation.
  - **Incoming References:** **0** (completely unlinked by legitimate code).
  - **Recommendation:** **IMMEDIATE DELETION**. Inspect server access logs for any requests targeting this URI.

### 1.2 Legacy & Orphaned Root Scripts

The root web directory contains scripts and error pages from 2016-2018 that are no longer part of active routing or navigation.

| File Path | Type | Reason Unused |
| :--- | :--- | :--- |
| [`photo_submission2016.php`](file:///Users/lei/Downloads/adastra/manualtest/photo_submission2016.php) | PHP script | Orphaned from 2016. Attempts to include non-existent `../includes/database_master.inc.php`. Syntax errors. Never called. |
| [`_index.html`](file:///Users/lei/Downloads/adastra/manualtest/_index.html) | HTML page | Abandoned 2016/2017 splash page. References defunct external links and missing CSS (`css/foundation.css`, `css/animate.css`). |
| [`landing-page.gif`](file:///Users/lei/Downloads/adastra/manualtest/landing-page.gif) | Image asset | Only referenced by `_index.html`. Unused by any active portal. |
| [`500.php`](file:///Users/lei/Downloads/adastra/manualtest/500.php) | PHP script | Standalone 500 error display script. Not registered in `.htaccess` `ErrorDocument`. |
| [`400.shtml`](file:///Users/lei/Downloads/adastra/manualtest/400.shtml) | SHTML page | Default cPanel error template. Not mapped by Apache configuration. |
| [`401.shtml`](file:///Users/lei/Downloads/adastra/manualtest/401.shtml) | SHTML page | Default cPanel error template. Not mapped by Apache configuration. |
| [`403.shtml`](file:///Users/lei/Downloads/adastra/manualtest/403.shtml) | SHTML page | Default cPanel error template. Not mapped by Apache configuration. |
| [`404.shtml`](file:///Users/lei/Downloads/adastra/manualtest/404.shtml) | SHTML page | Default cPanel error template. Not mapped by Apache configuration. |
| [`500.shtml`](file:///Users/lei/Downloads/adastra/manualtest/500.shtml) | SHTML page | Default cPanel error template. Not mapped by Apache configuration. |

### 1.3 Obsolete Version Iterations, Drafts & 0-Byte Files

Across the yearbook portals, ticketing sites, and admin console, developers frequently created versioned copies (`_v2`, `_v3`, `copy`, `orig`, `Open`, `dupe`) and left 0-byte dummy scripts directly in production directories.

#### A. Central Administrative Console (`admin`)

| File Path | Type / Size | Status & Finding |
| :--- | :---: | :--- |
| [`admin/deleteAppointment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/deleteAppointment.php) | **0 bytes** | Completely empty 0-byte file left in the root of admin. |
| [`admin/gpp_scheduling_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/gpp_scheduling_v1.php) | **0 bytes** | Completely empty 0-byte file left in the root of admin. |
| [`admin/passreqdupe.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/passreqdupe.php) | PHP script | Exact duplicate of `admin/passreq.php`. |
| [`admin/updatepassdupe.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/updatepassdupe.php) | PHP script | Exact duplicate of `admin/updatepass.php`. |
| [`admin/loginn.html`](file:///Users/lei/Downloads/adastra/manualtest/admin/loginn.html) | HTML page | Redundant draft copy of `admin/login.html`. |
| [`admin/forgot-password.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/forgot-password.php) | PHP script | Duplicate forgot password handler; forms use `forgot.html` and `passreq.php`. |
| [`admin/evaluate_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/evaluate_v1.php) | PHP script | Version 1 evaluation page; superseded by active `evaluate.php`. |
| [`admin/schedule-attendance-v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-attendance-v2.php) | PHP script | Draft attendance tool; production links to `schedule-attendance.php`. |
| [`admin/schedule-manager-fix.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-fix.php) | PHP script | One-off patch file; unreferenced in active admin navigation. |
| [`admin/schedule-manager-php-fix`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php-fix) | Artifact | Extensionless PHP/text script artifact left in admin root. |
| [`admin/sm-fix.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/sm-fix.php) | PHP script | One-off patch file for schedule management. |
| [`admin/sm.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/sm.php) | PHP script | Obsolete abbreviation script; superseded by `schedule-manager.php`. |
| [`admin/pictorial-copy.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/pictorial-copy.php) | PHP script | Duplicate copy of pictorial scheduling tool. |
| [`admin/pictorial_scheduling_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/pictorial_scheduling_v2.php) | PHP script | Draft version 2 of pictorial scheduling. |
| [`admin/pictorial_scheduling_v3.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/pictorial_scheduling_v3.php) | PHP script | Draft version 3 of pictorial scheduling. |
| [`admin/sidebar-og.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/sidebar-og.php) | PHP script | Original backup copy of sidebar navigation; production includes `sidebar.php`. |
| [`admin/downloadPhotos.html`](file:///Users/lei/Downloads/adastra/manualtest/admin/downloadPhotos.html) | HTML page | Standalone unlinked download helper page. |
| [`admin/index2024.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/index2024.php) | PHP script | Year-specific index prototype; production uses `index.php`. |
| [`admin/index2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/index2025.php) | PHP script | Year-specific index prototype; production uses `index.php`. |
| [`admin/index_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/index_v2.php) | PHP script | Alternative index page prototype. |
| [`admin/index copy.html`](file:///Users/lei/Downloads/adastra/manualtest/admin/index copy.html) | HTML page | Duplicate copy of admin index. |
| [`admin/index2.html`](file:///Users/lei/Downloads/adastra/manualtest/admin/index2.html) | HTML page | Legacy admin panel prototype. |
| [`admin/index3.html`](file:///Users/lei/Downloads/adastra/manualtest/admin/index3.html) | HTML page | Legacy admin panel prototype. |
| [`admin/starter.html`](file:///Users/lei/Downloads/adastra/manualtest/admin/starter.html) | HTML page | Unmodified AdminLTE starter HTML template. |
| [`admin/browse-2025-v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/browse-2025-v2.php) | PHP script | Draft version 2 of 2025 package browser. |
| [`admin/browse-writeups-2024-v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/browse-writeups-2024-v2.php) | PHP script | Draft version 2 of 2024 write-up browser. |
| [`admin/browse-pophistory-2026_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/browse-pophistory-2026_v1.php) | PHP script | Version 1 proof of payment history browser. |
| [`admin/browse-pophistory-2026_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/browse-pophistory-2026_TEST.php) | PHP script | Test harness for 2026 POP history. |
| [`admin/php/deleteSchedOrig.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/deleteSchedOrig.php) | PHP script | Original backup copy of schedule deletion script. |
| [`admin/php/old-pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/old-pullCalendar.php) | PHP script | Obsolete calendar data fetcher. |
| [`admin/php/pullTimeSlot_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTimeSlot_v1.php) | PHP script | Version 1 time slot fetcher. |
| [`admin/php/updateSchedule_GPP_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateSchedule_GPP_v1.php) | PHP script | Version 1 GPP schedule updater. |
| [`admin/php/survey_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/survey_v2.php) | PHP script | Survey version 2 endpoint. |

#### B. Campaign Portals (`adastra2023`, `adastra2024`, `adastra2025`)

| File Path | Status & Finding |
| :--- | :--- |
| [`adastra2023/closed.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/closed.html) | Standalone closed notice page; not actively routed. |
| [`adastra2023/subscribe.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/subscribe.html) | Early iteration of subscription form; production uses `registration.html`. |
| [`adastra2023/adastra/index copy.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/index copy.html) | Duplicate copy of admin index page. |
| [`adastra2024/indexOPEN.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/indexOPEN.html) | Draft open-state index page; superseded by active `index.html`. |
| [`adastra2024/forgotpass.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/forgotpass.html) | Duplicate of `forgot.html`. Production links to `forgot.html`. |
| [`adastra2024/passreq2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/passreq2.php) | Duplicate iteration of `passreq.php`. Production forms post to `passreq.php`. |
| [`adastra2024/updatepass2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/updatepass2.php) | Duplicate iteration of `updatepass.php`. |
| [`adastra2024/adastra/checkIn2024_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/checkIn2024_v2.php) | Version 2 check-in script; superseded by production check-in tools. |
| [`adastra2024/adastra/index copy.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/index copy.html) | Duplicate copy of admin index page. |
| [`adastra2024/adastra/dashboard/photoshoot-schedule/recruitmentmailblast_copy.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/photoshoot-schedule/recruitmentmailblast_copy.php) | Duplicate mail blast script. |
| [`adastra2025/login2.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/login2.html) | Draft login iteration. |
| [`adastra2025/login3.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/login3.html) | Draft login iteration. |
| [`adastra2025/login_orig.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/login_orig.html) | Original backup copy of `login.html`. |
| [`adastra2025/forgot_v1.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/forgot_v1.html) | Early draft iteration of forgot password form. |
| [`adastra2025/forgot_v2.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/forgot_v2.html) | Intermediate draft iteration of forgot password form. |
| [`adastra2025/forgot_v3.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/forgot_v3.html) | Intermediate draft iteration of forgot password form. |
| [`adastra2025/forgot_v4.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/forgot_v4.html) | Intermediate draft iteration of forgot password form. |
| [`adastra2025/indexOpen.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/indexOpen.html) | Draft open-state landing page; superseded by `index.html`. |
| [`adastra2025/subformOpen.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/subformOpen.html) | Alternative subform open version. |
| [`adastra2025/subformUpdate2.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/subformUpdate2.html) | Alternative update subform iteration. |
| [`adastra2025/newSubForm.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/newSubForm.html) | Prototype replacement subform; production form is `subform.html`. |
| [`adastra2025/ExclusiveSubform.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/ExclusiveSubform.html) | Standalone promo subform with hardcoded values; unlinked. |
| [`adastra2025/checkin-og.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/checkin-og.html) | Original check-in scanner page. |
| [`adastra2025/checkinv2.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/checkinv2.html) | Version 2 check-in scanner page. |
| [`adastra2025/checkin.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/checkin.html) | Standalone check-in tool; unlinked. |
| [`adastra2025/checkout.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/checkout.html) | Standalone checkout tool; unlinked. |
| [`adastra2025/close.css`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/close.css) | Unreferenced stylesheet for closed state. |
| [`adastra2025/confirm.css`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/confirm.css) | Unreferenced stylesheet for confirmation page. |
| [`adastra2025/accord.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/accord.html) | Standalone accordion demo page. |
| [`adastra2025/set.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/set.html) | Standalone draft page. |
| [`adastra2025/lookbook.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/lookbook.html) | Standalone draft page. |
| [`adastra2025/passreq_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/passreq_v2.php) | Alternative password request script; production uses `passreq.php`. |
| [`adastra2025/sendsubinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/sendsubinfo.php) | Standalone mail script; unreferenced by production forms. |

#### C. Other Portals (`adastra2026`, `adastra2027`, `embark8`, `embark10`, `fragment202122`, `obra2019`, `ticket`)

| File Path | Status & Finding |
| :--- | :--- |
| [`2026ticket/index_v2.html`](file:///Users/lei/Downloads/adastra/manualtest/2026ticket/index_v2.html) | **Identical duplicate** of `2026ticket/index.html` (identical MD5 hash). Never referenced. |
| [`2026ticket/indexClosed.html`](file:///Users/lei/Downloads/adastra/manualtest/2026ticket/indexClosed.html) | Preserved registration form from when the ticket portal was open; superseded by static teaser. |
| [`2027ticket/index_v2.html`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/index_v2.html) | Early draft snapshot of 2027 ticket page (12.1 KB vs active 31.7 KB). Unlinked. |
| [`2027ticket/index_v3.html`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/index_v3.html) | Intermediate draft snapshot (20.9 KB). Unlinked. |
| [`2027ticket/index_v4.html`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/index_v4.html) | Intermediate draft snapshot (24.2 KB). Unlinked. |
| [`2027ticket/index_v5.html`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/index_v5.html) | Pre-release draft snapshot (24.8 KB). Unlinked. |
| [`2027ticket/indexClosed.html`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/indexClosed.html) | Copied directly from 2026ticket; contains 2026 campaign strings. Never linked. |
| [`adastra2026/subformV1.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/subformV1.html) | First revision of subscription form. Superseded by `subform.html`. |
| [`adastra2026/subformV2.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/subformV2.html) | Second revision of subscription form. Superseded by `subform.html`. |
| [`adastra2026/subformV3.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/subformV3.html) | Third revision of subscription form. Superseded by `subform.html`. |
| [`adastra2026/subformClose.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/subformClose.html) | Static closed form notice; unrouted. |
| [`adastra2026/subformV2.css`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/subformV2.css) | Stylesheet created specifically for uncalled `subformV2.html`. |
| [`adastra2026/close.css`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/close.css) | Unreferenced styling file for closed state. |
| [`adastra2026/confirm.css`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/confirm.css) | Duplicate styling file; `confirmation.html` links directly to `css/styles.css`. |
| [`adastra2026/indexOpenv3.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/indexOpenv3.html) | Draft open-state index page; superseded by production `index.html`. |
| [`adastra2026/Closed.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/Closed.html) | Standalone closed announcement page; no incoming links. |
| [`adastra2026/ExclusiveSubform.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/ExclusiveSubform.html) | Standalone subform with hardcoded promo values; unlinked. |
| [`adastra2026/passreq_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/passreq_v2.php) | Alternative password request handler; production forms post to `passreq.php`. |
| [`adastra2026/checkin.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/checkin.html) | Standalone QR/event check-in tool, unlinked. |
| [`adastra2026/checkout.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/checkout.html) | Standalone event check-out tool, unlinked. |
| [`adastra2027/index_testing.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/index_testing.html) | Testing version of 2027 landing page. Superseded by `index.html`. |
| [`adastra2027/subformV1.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/subformV1.html) | Old form iteration carried over from 2026. Superseded by `subform.html`. |
| [`adastra2027/subformClose.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/subformClose.html) | Unreferenced closed notice page. |
| [`adastra2027/Closed.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/Closed.html) | Unreferenced closed notice page. |
| [`adastra2027/loginv2.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/loginv2.html) | Alternative login page prototype; production links go to `login.html`. |
| [`adastra2027/checkin.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/checkin.html) | Copied check-in tool, unlinked. |
| [`adastra2027/checkout.html`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/checkout.html) | Copied check-out tool, unlinked. |
| [`embark8/embark8.html`](file:///Users/lei/Downloads/adastra/manualtest/embark8/embark8.html) | Duplicate of `embark8/index.html` containing 114 extra lines of footer/staff markup. Never linked. |
| [`embark10/teaser.html`](file:///Users/lei/Downloads/adastra/manualtest/embark10/teaser.html) | Pre-launch teaser page; live site serves `embark10/index.html`. |
| [`fragment202122/registrationForm.html`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/registrationForm.html) | Deprecated early registration form; superseded by `subscriptionForm.html`. |
| [`fragment202122/safeForm`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/safeForm) | Unknown extensionless text file leftover in root of fragment202122. |
| [`fragment202122/adastra/index copy.html`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/index copy.html) | Accidental duplicate of admin index page. |
| [`fragment202122/adastra/index2.html`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/index2.html) | Development iteration of admin panel. |
| [`fragment202122/adastra/index3.html`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/index3.html) | Development iteration of admin panel. |
| [`fragment202122/adastra/starter.html`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/starter.html) | Unmodified AdminLTE starter template file. |
| [`obra2019/index-teaser.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/index-teaser.php) | Standalone teaser script; main site serves `obra2019/index.php`. |
| [`obra2019/includes/landingpage-aj.inc.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/landingpage-aj.inc.php) | Dead alternate version of landing page include. Never required. |
| [`obra2019/includes/landingpage-open.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/landingpage-open.php) | Dead alternate version of landing page include. Never required. |

### 1.4 Uncalled Test, Scratch, and Diagnostic Scripts

Numerous test and debug scripts were left deployed across production directories:

| File Path | Description & Purpose |
| :--- | :--- |
| [`admin/test.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/test.php) | Scratch PHP test script in admin root. |
| [`admin/testt.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/testt.php) | Scratch PHP test script in admin root. |
| [`admin/testscandir.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/testscandir.php) | Test script for directory scanning. |
| [`admin/mailviewer.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/mailviewer.php) | HTML email template previewer script. |
| [`admin/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/testCheckIn.php) | Diagnostic check-in test script. |
| [`admin/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/testUsername.php) | Username testing endpoint. |
| [`admin/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/testWriteUpCount.php) | Write-up count testing endpoint. |
| [`admin/php/masterInjector.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/masterInjector.php) | Legacy test data injector script. |
| [`adastra2024/luxeoadastrablasttest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/luxeoadastrablasttest.php) | Ad-hoc test script for Luxeo email blast. |
| [`adastra2024/testblast.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/testblast.php) | Ad-hoc email blast test script. |
| [`adastra2024/mailviewer.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/mailviewer.php) | Ad-hoc HTML email template viewer / tester script. |
| [`adastra2024/mailvieweremailblast.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/mailvieweremailblast.php) | Ad-hoc blast email previewer script. |
| [`adastra2023/mailviewer.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailviewer.php) | Ad-hoc HTML email template viewer / tester script. |
| [`adastra2025/mailviewer.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/mailviewer.php) | Ad-hoc HTML email template viewer / tester script. |
| [`adastra2025/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/testCheckIn.php) | Diagnostic check-in test script. |
| [`adastra2025/php/testCheckOut.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/testCheckOut.php) | Diagnostic check-out test script. |
| [`adastra2025/adastra/php/pullinfo_v2_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullinfo_v2_TEST.php) | Test harness for subscriber info fetching. |
| [`adastra2025/adastra/dashboard/my-photos/uploadPhoto-OLD.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/uploadPhoto-OLD.php) | Obsolete photo upload handler. |
| [`adastra2025/adastra/dashboard/my-photos/uploadPhoto-BACKUP.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/uploadPhoto-BACKUP.php) | Backup copy of photo upload handler. |
| [`adastra2023/emailblast.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/emailblast.php) | Legacy email blast script. |
| [`adastra2023/emailGeneral.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/emailGeneral.php) | Legacy general email notification script. |
| [`adastra2023/emailTransfer.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/emailTransfer.php) | Legacy transfer email notification script. |
| [`adastra2023/mailFirstBatch.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailFirstBatch.php) | One-off blast to batch 1 subscribers. |
| [`adastra2023/mailSecondBatch.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailSecondBatch.php) | One-off blast to batch 2 subscribers. |
| [`adastra2023/mailNoAppointment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailNoAppointment.php) | One-off blast to subscribers without appointments. |
| [`adastra2023/mailNoGPP.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailNoGPP.php) | One-off blast to subscribers without GPP packages. |
| [`adastra2023/mailNoPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailNoPayment.php) | One-off blast to unpaid subscribers. |
| [`adastra2023/mailNoWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailNoWriteUp.php) | One-off blast for missing write-ups. |
| [`adastra2023/mailObraClaim.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailObraClaim.php) | One-off Obra claiming email script. |
| [`adastra2023/mailPassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailPassword.php) | One-off password notification script. |
| [`adastra2023/mailTransfer.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailTransfer.php) | One-off transfer notification script. |
| [`adastra2023/mailWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailWriteUp.php) | One-off write-up notification script. |
| [`2026ticket/store.php`](file:///Users/lei/Downloads/adastra/manualtest/2026ticket/store.php) | 5-line script testing mysqli connection to `theadast_adastra_byo2026`. Unused. |
| [`2027ticket/store.php`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/store.php) | Copy of 2026 store.php; erroneously connects to `theadast_adastra_byo2026`. Unused. |
| [`adastra2026/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/testCheckIn.php) | Test endpoint with hardcoded DB query for check-in debugging. |
| [`adastra2026/php/testCheckOut.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/testCheckOut.php) | Test endpoint with hardcoded DB query for check-out debugging. |
| [`adastra2027/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/testCheckIn.php) | Test endpoint with hardcoded DB query for check-in debugging. |
| [`adastra2027/php/testCheckOut.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/testCheckOut.php) | Test endpoint with hardcoded DB query for check-out debugging. |
| [`fragment202122/testSubscribe.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/testSubscribe.php) | HTML form saved as `.php` that submits test registrations to `saveTestRegistration.php`. |
| [`fragment202122/saveTestRegistration.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/saveTestRegistration.php) | Test backend registration processor. |
| [`fragment202122/testStudentNumber.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/testStudentNumber.php) | Validation endpoint only invoked by the test form `testSubscribe.php`. |
| [`fragment202122/testmail.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/testmail.php) | PHPMailer SMTP test sender script. |

### 1.5 Abandoned Cloned Test Suites & Folders

Two entire standalone folders under `fragment202122` are obsolete test duplicates that are completely disconnected from production:

1. **`fragment202122/fragmenttest/` (187 files):**
   - An entire clone of the `fragment202122` application created for testing.
   - Contains its own duplicate copies of `registrationForm.html`, `subscriptionForm.html`, `processRegistration.php`, `databaseWrapper.php`, `approveUser.php`, `success.php`, `index_css/`, and `cgi-bin/`.
2. **`fragment202122/recruitmentTest/` (91 files):**
   - A test recruitment application containing form handling, file upload directories (`uploads/`), font libraries, PNG assets, and `connection.php`.
3. **`adastra2023/bower_components/` & `adastra2023/snbutton-master/`:**
   - Unbundled frontend dependency folders left inside `adastra2023`, containing hundreds of unused source files.

### 1.6 Dead Cloned Backend Endpoints in GPP Modules (510 Files)

> [!NOTE]
> **Massive Architectural Dead Weight in GPP Modules**  
> Each of the Graduation Photo Package folders (`gpp_2023`, `gpp_2024`, `gpp_2025`, `gpp_2026`, `gpp_claiming`, `gpp_fragment`) is designed simply as a one-page claiming form (`index.php`) submitting to `connect.php`.  
> However, an entire administrative API folder (`php/` containing 87 PHP scripts) was copy-pasted into **each** of these 6 folders from an older yearbook administration system. Because these GPP portals have no administrative dashboard UI, **85 scripts in each folder are completely uncalled and dead** (totaling **510 orphaned backend scripts**).

### 1.7 Leftover Compressed Archive Packages (15 ZIP Files, ~467 MB)

> [!WARNING]
> **Huge Server Disk Bloat: ~467 MB of Compressed Archives**  
> Numerous large `.zip` archives containing database dumps, file uploads, backups, and library packages are sitting uncompressed in production directories.

| Archive Path | File Size | Description & Status |
| :--- | :---: | :--- |
| [`adastra2024/adastra/proofUploads.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/proofUploads.zip) | **206.4 MB** | Complete zip backup of proof of payment uploads. Safe to archive offsite. |
| [`adastra2024/adastra/dashboard.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard.zip) | **98.5 MB** | Complete zip backup of 2024 admin dashboard tree. |
| [`adastra2023/test.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/test.zip) | **83.1 MB** | Miscellaneous test assets and media zip left in `adastra2023` root. |
| [`adastra2025/elements.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/elements.zip) | **20.1 MB** | Zip bundle of graphic assets in `adastra2025`. |
| [`fragment202122/assets.zip`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/assets.zip) | **7.3 MB** | Leftover zip archive of the `assets/` directory. |
| [`adastra2023/adastra/PHPExcel-1.8.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/PHPExcel-1.8.zip) | **5.2 MB** | Compressed source zip of PHPExcel 1.8. (Extracted folder already exists). |
| [`adastra2024/adastra/PHPExcel-1.8.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/PHPExcel-1.8.zip) | **5.2 MB** | Compressed source zip of PHPExcel 1.8. |
| [`fragment202122/adastra/PHPExcel-1.8.zip`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/PHPExcel-1.8.zip) | **4.9 MB** | Compressed source zip of PHPExcel 1.8. |
| [`EMBARK7/web.zip`](file:///Users/lei/Downloads/adastra/manualtest/EMBARK7/web.zip) | **3.7 MB** | Backup zip of Flutter web build output. |
| [`adastra2023/adastra/File Manager.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/File Manager.zip) | **2.8 MB** | Compressed source zip of file manager utility. |
| [`adastra2024/adastra/File Manager.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/File Manager.zip) | **2.8 MB** | Compressed source zip of file manager utility. |
| [`fragment202122/adastra/File Manager.zip`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/File Manager.zip) | **2.4 MB** | Compressed source zip of file manager utility. |
| [`adastra2023/adastra/file-manager/File.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/file-manager/File.zip) | **1.9 MB** | Nested file manager archive. |
| [`adastra2024/adastra/file-manager/File.zip`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/file-manager/File.zip) | **1.9 MB** | Nested file manager archive. |
| [`obra2019/obra-teaser.zip`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/obra-teaser.zip) | **1.3 MB** | Leftover zip of teaser graphics and assets. |
| **Total Compressed Archive Bloat** | **~467.5 MB** | |

### 1.8 Backup, Upgrade, and Server Metadata Artifacts

| File Path | Category | Details & Size |
| :--- | :--- | :--- |
| [`error_log`](file:///Users/lei/Downloads/adastra/manualtest/error_log) | Unrotated Log | **303.7 MB** server error log in workspace root. |
| [`adastra2023/error_log`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/error_log) | Unrotated Log | **6.2 MB** error log in `adastra2023`. |
| [`admin/error_log`](file:///Users/lei/Downloads/adastra/manualtest/admin/error_log) | Unrotated Log | **590.9 KB** error log in `admin` root (+ 19 subfolder error logs). |
| [`adastra2025/error_log`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/error_log) | Unrotated Log | **503.1 KB** error log in `adastra2025`. |
| [`adastra2024/error_log`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/error_log) | Unrotated Log | **341.6 KB** error log in `adastra2024`. |
| [`.htaccess.bak.1566974363`](file:///Users/lei/Downloads/adastra/manualtest/.htaccess.bak.1566974363) | Backup | Timestamped Apache `.htaccess` backup from 2019. |
| [`.htaccess.bak.1566974456`](file:///Users/lei/Downloads/adastra/manualtest/.htaccess.bak.1566974456) | Backup | Timestamped Apache `.htaccess` backup from 2019. |
| [`.htaccess.bak.1566975960`](file:///Users/lei/Downloads/adastra/manualtest/.htaccess.bak.1566975960) | Backup | Timestamped Apache `.htaccess` backup from 2019. |
| [`.htaccess.phpupgrader.initial`](file:///Users/lei/Downloads/adastra/manualtest/.htaccess.phpupgrader.initial) | Upgrader Artifact | Leftover cPanel PHP version upgrade backup file. |
| [`.htaccess.phpupgrader.fa3958b8`](file:///Users/lei/Downloads/adastra/manualtest/.htaccess.phpupgrader.fa3958b8) | Upgrader Artifact | Leftover cPanel PHP version upgrade backup file. |
| [`.htaccess.phpupgrader.d5bf0bb6`](file:///Users/lei/Downloads/adastra/manualtest/.htaccess.phpupgrader.d5bf0bb6) | Upgrader Artifact | Leftover cPanel PHP version upgrade backup file. |
| [`.htaccess.phpupgrader.4812b21c`](file:///Users/lei/Downloads/adastra/manualtest/.htaccess.phpupgrader.4812b21c) | Upgrader Artifact | Leftover cPanel PHP version upgrade backup file. |
| [`php.ini.ea4bak`](file:///Users/lei/Downloads/adastra/manualtest/php.ini.ea4bak) | Upgrader Artifact | EasyApache 4 backup of `php.ini` (67.0 KB). |
| [`.ftpquota`](file:///Users/lei/Downloads/adastra/manualtest/.ftpquota) | cPanel Artifact | Server FTP quota metadata file. |
| [`2026ticket/desktop.ini`](file:///Users/lei/Downloads/adastra/manualtest/2026ticket/desktop.ini) | OS Metadata | Windows shell folder icon configuration file. |
| [`2027ticket/desktop.ini`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/desktop.ini) | OS Metadata | Windows shell folder icon configuration file. |
| `.DS_Store` (multiple) | OS Metadata | macOS Finder visual folder metadata files. |

### 1.9 Defunct / Empty Directories

- [`embark/`](file:///Users/lei/Downloads/adastra/manualtest/embark/) - Empty directory containing only an empty `.well-known` folder. Leftover placeholder.
- [`cgi-bin/`](file:///Users/lei/Downloads/adastra/manualtest/cgi-bin/) - Empty server CGI directory in root.
- [`admin/late-folder/`](file:///Users/lei/Downloads/adastra/manualtest/admin/late-folder/) - Empty folder created for late yearbook submissions.

### 1.10 Comprehensive Cleanup Action Plan

1. **Urgent Security Fix:** Delete `fragment202122/favicon_io/wp-loginx3.php` immediately and check server access logs.
2. **Reclaim Massive Disk Space (~780 MB):**
   - Delete or move offsite the 15 `.zip` archive packages (~467 MB), especially `proofUploads.zip` (206 MB), `dashboard.zip` (98 MB), and `test.zip` (83 MB).
   - Truncate/rotate the huge log files (`error_log` 303 MB in root, 6.2 MB in `adastra2023`, and 20 error logs in `admin`).
3. **Remove Dead Test Environments:** Delete `fragment202122/fragmenttest/` (187 files) and `fragment202122/recruitmentTest/` (91 files).
4. **Prune Orphaned GPP API Endpoints:** Remove the 510 uncalled `php/pull*.php` and `php/approve*.php` scripts from the 6 `gpp_*` folders.
5. **Clean Version Drafts & Diagnostic Scripts:** Remove `_v2`, `_v3`, `_v4`, `_v5`, `login2..3`, `passreqdupe.php`, `deleteAppointment.php` (0b), `gpp_scheduling_v1.php` (0b), `subformV1..V3`, `subformOpen`, and ad-hoc connection test files.

---

## Section 2: Complete Database Connection and Call Audit

The codebase interacts with MySQL/MariaDB across various yearbook campaign editions (`theadast_byo2016` through `theadast_byo2027`, `theadast_claiming`, `theadast_cspanel`, `theadast_recruitment`, etc.).

This audit divides database interactions into distinct architectural tiers:
1. **Files with Hardcoded Database Connections (46 Files)** (direct `mysqli_connect` or `new mysqli` inside the script, bypassing `connect.php`).
2. **Database Connection Definition Files (`connect.php` - 45 Files)** (the 45 files named `connect.php` that define and initialize connections).
3. **Custom Database Abstraction Wrappers and Callers (25 Files)** (`database_master.php`, `databaseWrapper.php`, `connection.php`).
4. **Files Calling the Database via `connect.php` (1,354 Files)** (the 1,354 scripts that include `connect.php` to access `$conn`, `$conn2026`, etc.).
5. **Master Database & Credentials Matrix**.

### 2.1 Architectural Overview of Database Connectivity

```
                 +-------------------------------------------------------------+
                 |                     Client Requests                         |
                 +-------------------------------------------------------------+
                        |                                             |         
                        v                                             v         
       [Pattern A: Standard Architecture]             [Pattern B: Hardcoded Pattern]    
  +-------------------------------------------+   +------------------------------------+
  | 1,354 Backend Scripts / AJAX Endpoints    |   | 46 Standalone Scripts & Endpoints  |
  | (e.g. pullSchedules.php, saveReg.php)     |   | (e.g. passreq.php, editCabinet.php)|
  +-------------------------------------------+   +------------------------------------+
                        |                                             |         
                        | include "connect.php"                       | direct mysqli_* 
                        v                                             v         
  +-------------------------------------------+                               |         
  | Central Definition Files (connect.php)    |                               |         
  | - 37 Shared connection config files       |                               |         
  | - 8 Form POST action handlers             |                               |         
  +-------------------------------------------+                               |         
                        |                                                     |         
                        +-----------------------+-----------------------------+         
                                                |                                       
                                                v                                       
                     +----------------------------------------------------+             
                     | MySQL Databases (byo2016..2027, claiming, cspanel) |             
                     +----------------------------------------------------+             
```

### 2.2 Files with Hardcoded Database Connections (46 Files)

> [!WARNING]
> **Security and Maintenance Risk**  
> These 46 files do **not** use `connect.php`. They establish a direct connection by embedding database credentials and connection parameters directly within the script. If credentials or database names change, these files will fail silently unless manually updated.

| # | File Path | Connection Method | Target Database | Hardcoded Host / User | Script Purpose |
| :- | :--- | :--- | :--- | :--- | :--- |
| 1 | [`admin/add-schedule.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/add-schedule.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2024")` | `theadast_byo2024` | `localhost / theadast_jad` | Adds schedule slots for 2024 batch |
| 2 | [`admin/edit-schedule-GPP.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/edit-schedule-GPP.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_claiming")` | `theadast_claiming` | `localhost / theadast_jad` | Edits GPP claiming schedule slots |
| 3 | [`admin/edit-schedule.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/edit-schedule.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2025")` | `theadast_byo2025` | `localhost / theadast_jad` | Edits photo schedule slots for 2025 batch |
| 4 | [`admin/passreq.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/passreq.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2025')` | `theadast_byo2025` | `localhost / theadast_jad` | Admin user password request handler |
| 5 | [`admin/passreqdupe.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/passreqdupe.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2025')` | `theadast_byo2025` | `localhost / theadast_jad` | Duplicate password request handler |
| 6 | [`admin/validate-username.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/validate-username.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2025')` | `theadast_byo2025` | `localhost / theadast_jad` | Validates admin usernames |
| 7 | [`admin/schedule-manager-php/add_schedule.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php/add_schedule.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2024")` | `theadast_byo2024` | `localhost / theadast_jad` | Adds photoshoot schedules |
| 8 | [`admin/php/add-schedule.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/add-schedule.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2024")` | `theadast_byo2024` | `localhost / theadast_jad` | Schedule creation endpoint |
| 9 | [`admin/php/addSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/addSchedule.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2026")` | `theadast_byo2026` | `localhost / theadast_jad` | Schedule creation endpoint (2026) |
| 10 | [`admin/php/addSchedule_GPP.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/addSchedule_GPP.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_claiming")` | `theadast_claiming` | `localhost / theadast_jad` | GPP claiming schedule creation |
| 11 | [`admin/php/deleteAppointment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/deleteAppointment.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2026")` | `theadast_byo2026` | `localhost / theadast_jad` | Deletes appointments (2026) |
| 12 | [`admin/php/deleteAppointment_GPP.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/deleteAppointment_GPP.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_claiming")` | `theadast_claiming` | `localhost / theadast_jad` | Deletes GPP claiming appointments |
| 13 | [`admin/php/editCabinet.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editCabinet.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_cspanel")` | `theadast_cspanel` | `localhost / theadast_jad` | Edits cabinet records in CS Panel |
| 14 | [`admin/php/editPackage.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editPackage.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2026")` | `theadast_byo2026` | `localhost / theadast_jad` | Updates package prices/details (2026) |
| 15 | [`admin/php/editWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editWriteUp.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2024")` | `theadast_byo2024` | `localhost / theadast_jad` | Updates student write-up text (2024) |
| 16 | [`admin/php/editWriteUp2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editWriteUp2025.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2025")` | `theadast_byo2025` | `localhost / theadast_jad` | Updates student write-up text (2025) |
| 17 | [`admin/php/editWriteUp2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editWriteUp2026.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2026")` | `theadast_byo2026` | `localhost / theadast_jad` | Updates student write-up text (2026) |
| 18 | [`admin/php/editWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editWriteUpApproval.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2025")` | `theadast_byo2025` | `localhost / theadast_jad` | Updates write-up review status (2025) |
| 19 | [`admin/php/editYearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editYearbook.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_cspanel")` | `theadast_cspanel` | `localhost / theadast_jad` | Edits yearbook configuration in CS Panel |
| 20 | [`admin/php/editYearbookDetails.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editYearbookDetails.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_cspanel")` | `theadast_cspanel` | `localhost / theadast_jad` | Edits yearbook specification details |
| 21 | [`admin/php/getOrNumber.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/getOrNumber.php) | `mysqli_connect($host, $username, $password, $database)` | `Dynamic database` | `localhost / theadast_jad` | Fetches OR number for payment verification |
| 22 | [`admin/php/2025/getOrNumber2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/getOrNumber2025.php) | `mysqli_connect($host, $username, $password, $database)` | `theadast_byo2025` | `localhost / theadast_jad` | Fetches OR number for 2025 payment verification |
| 23 | [`admin/php/2026/getOrNumber2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/getOrNumber2026.php) | `mysqli_connect($host, $username, $password, $database)` | `theadast_byo2026` | `localhost / theadast_jad` | Fetches OR number for 2026 payment verification |
| 24 | [`admin/php/2027/getOrNumber2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/getOrNumber2027.php) | `mysqli_connect($host, $username, $password, $database)` | `theadast_byo2027` | `localhost / theadast_jad` | Fetches OR number for 2027 payment verification |
| 25 | [`admin/php/updateSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateSchedule.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2026")` | `theadast_byo2026` | `localhost / theadast_jad` | Updates appointment scheduling data |
| 26 | [`admin/php/updateSchedule_GPP.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateSchedule_GPP.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_claiming")` | `theadast_claiming` | `localhost / theadast_jad` | Updates GPP appointment slots |
| 27 | [`admin/php/updateSchedule_GPP_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateSchedule_GPP_v1.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_claiming")` | `theadast_claiming` | `localhost / theadast_jad` | Version 1 GPP schedule updater |
| 28 | [`adastra2023/passreq.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/passreq.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2023')` | `theadast_byo2023` | `localhost / theadast_jad` | Student password reset request processing (2023) |
| 29 | [`adastra2024/passreq.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/passreq.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2024')` | `theadast_byo2024` | `localhost / theadast_jad` | Student password reset request processing (2024) |
| 30 | [`adastra2024/passreq2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/passreq2.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2024')` | `theadast_byo2024` | `localhost / theadast_jad` | Alternative password reset request processor (2024) |
| 31 | [`adastra2025/passreq.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/passreq.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2025')` | `theadast_byo2025` | `localhost / theadast_jad` | Student password reset request processing (2025) |
| 32 | [`adastra2025/passreq_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/passreq_v2.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2025')` | `theadast_byo2025` | `localhost / theadast_jad` | Alternative password reset request processor (2025) |
| 33 | [`adastra2025/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/testCheckIn.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2025")` | `theadast_byo2025` | `localhost / theadast_jad` | Diagnostic check-in test script (2025) |
| 34 | [`adastra2025/php/testCheckOut.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/testCheckOut.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2025")` | `theadast_byo2025` | `localhost / theadast_jad` | Diagnostic check-out test script (2025) |
| 35 | [`adastra2026/passreq.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/passreq.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2026')` | `theadast_byo2026` | `localhost / theadast_jad` | Student password reset request processing (2026) |
| 36 | [`adastra2026/passreq_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/passreq_v2.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2026')` | `theadast_byo2026` | `localhost / theadast_jad` | Alternative password reset request processor (2026) |
| 37 | [`adastra2026/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/testCheckIn.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2026")` | `theadast_byo2026` | `localhost / theadast_jad` | Diagnostic check-in test script (2026) |
| 38 | [`adastra2026/php/testCheckOut.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/testCheckOut.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2026")` | `theadast_byo2026` | `localhost / theadast_jad` | Diagnostic check-out test script (2026) |
| 39 | [`adastra2027/passreq.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/passreq.php) | `new mysqli('localhost','theadast_jad','...','theadast_byo2027')` | `theadast_byo2027` | `localhost / theadast_jad` | Student password reset request processing (2027) |
| 40 | [`adastra2027/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/testCheckIn.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2027")` | `theadast_byo2027` | `localhost / theadast_jad` | Diagnostic check-in test script (2027) |
| 41 | [`adastra2027/php/testCheckOut.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/testCheckOut.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2027")` | `theadast_byo2027` | `localhost / theadast_jad` | Diagnostic check-out test script (2027) |
| 42 | [`gpp_claiming/gpp_php/validateSubscriber.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/validateSubscriber.php) | `new mysqli($db_host, $db_user, $db_pass, $db_name)` | `theadast_byo2016..2026 (dynamic)` | `localhost / theadast_jad` | Validates student ID against multi-year databases |
| 43 | [`2026ticket/store.php`](file:///Users/lei/Downloads/adastra/manualtest/2026ticket/store.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_adastra_byo2026")` | `theadast_adastra_byo2026` | `localhost / theadast_jad` | Test connection script |
| 44 | [`2027ticket/store.php`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/store.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_adastra_byo2026")` | `theadast_adastra_byo2026` | `localhost / theadast_jad` | Stale test connection script targeting 2026 DB |
| 45 | [`2027ticket/photobooth.php`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/photobooth.php) | `mysqli_connect("localhost","theadast_jad","...","theadast_byo2027")` | `theadast_byo2027` | `localhost / theadast_jad` | Photobooth photo upload and email lookup handler |
| 46 | [`fragment202122/fragmenttest/success.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/fragmenttest/success.php) | `new mysqli("localhost","theadast_jad","...","theadast_fragmentTest")` | `theadast_fragmentTest` | `localhost / theadast_jad` | Test success page DB confirmation handler |

### 2.3 Database Connection Definition Files (`connect.php` - 45 Files)

There are **45 files** named `connect.php` across the workspace. They fall into two distinct functional categories:

#### Category A: Form POST Action Handlers (8 Files)
These files are located in root campaign folders. Instead of acting as passive configuration includes, they directly receive `$_POST` form data from web browsers, establish a database connection, execute an `INSERT` or `UPDATE` statement, and redirect or output a response.

| # | File Path | Target Database | Action Executed |
| :- | :--- | :--- | :--- |
| 1 | [`2026ticket/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/2026ticket/connect.php) | `theadast_byo2026` | Receives ticket registration form POST; inserts into `ticket_registrations` / logs. |
| 2 | [`2027ticket/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/connect.php) | `theadast_byo2027` | Receives ticket registration form POST; inserts into `ticket_registrations` / logs. |
| 3 | [`gpp_2023/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/connect.php) | `theadast_byo2023` | Receives GPP claiming form; inserts into `gpp_claiming` and appends to `claimedGPP.txt`. |
| 4 | [`gpp_2024/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/connect.php) | `theadast_byo2024` | Receives GPP claiming form; inserts into `gpp_claiming` and appends to `claimedGPP.txt`. |
| 5 | [`gpp_2025/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/connect.php) | `theadast_byo2025` | Receives GPP claiming form; inserts into `gpp_claiming` and appends to `claimedGPP.txt`. |
| 6 | [`gpp_2026/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/connect.php) | `theadast_byo2026` | Receives GPP claiming form; inserts into `gpp_claiming` and appends to `claimedGPP.txt`. |
| 7 | [`gpp_claiming/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/connect.php) | `theadast_byo2024` | Receives claiming form; inserts into `gpp_claiming` table. |
| 8 | [`gpp_fragment/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/connect.php) | `theadast_byo2021` | Receives Fragment GPP claiming form; inserts into `gpp_claiming`. |

#### Category B: Shared Central Connection Includes (37 Files)
These files act as central connection managers. They define connection objects (`$conn`, `$conn2026`, `$conn2025`, etc.) that are included by administrative and user API scripts.

| # | File Path | Established Connections | Included By |
| :- | :--- | :--- | :--- |
| 1 | [`admin/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/connect.php) | `theadast_byo2027` | Admin main pages including dashboard and approvals |
| 2 | [`admin/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/connect.php) | Multi-year (`byo2016`..`2027`, `recruitment`) | Central admin backend endpoints in `admin/php/*.php` |
| 3 | [`admin/php/2025/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/connect.php) | `theadast_byo2025` + multi-year archives | 2025 batch administrative endpoints in `admin/php/2025/` |
| 4 | [`admin/php/2026/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/connect.php) | `theadast_byo2026` + multi-year archives | 2026 batch administrative endpoints in `admin/php/2026/` |
| 5 | [`admin/php/2027/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/connect.php) | `theadast_byo2027` + multi-year archives | 2027 batch administrative endpoints in `admin/php/2027/` |
| 6 | [`adastra2023/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/php/connect.php) | `theadast_byo2019` | `adastra2023/dbcon.php`, subscription scripts |
| 7 | [`adastra2023/adastra/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/connect.php) | `theadast_byo2023` | `adastra2023/adastra/php/*.php` admin endpoints |
| 8 | [`adastra2023/adastra/dashboard/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/connect.php) | `theadast_byo2023` | `adastra2023/adastra/dashboard/php/*.php` endpoints |
| 9 | [`adastra2024/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/php/connect.php) | `theadast_byo2019` | `adastra2024/dbcon.php`, subscription scripts |
| 10 | [`adastra2024/adastra/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/connect.php) | `theadast_byo2024` | `adastra2024/adastra/php/*.php` admin endpoints |
| 11 | [`adastra2024/adastra/dashboard/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/connect.php) | `theadast_byo2023` | `adastra2024/adastra/dashboard/php/*.php` endpoints |
| 12 | [`adastra2024/adastra/dashboard/write-up/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/write-up/connect.php) | `theadast_byo2023` | Write-up editing and submission handlers |
| 13 | [`adastra2025/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/connect.php) | `theadast_byo2019` | `adastra2025/dbcon.php`, subscription scripts |
| 14 | [`adastra2025/adastra/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/connect.php) | `theadast_byo2025` | `adastra2025/adastra/php/*.php` admin endpoints |
| 15 | [`adastra2025/adastra/dashboard/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/connect.php) | `theadast_byo2025` | `adastra2025/adastra/dashboard/php/*.php` endpoints |
| 16 | [`adastra2025/adastra/dashboard/write-up/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/write-up/connect.php) | `theadast_byo2023` | Write-up editing and submission handlers |
| 17 | [`adastra2025/adastra/dashboard/gpp_php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/connect.php) | `theadast_byo2025` | GPP claiming endpoints in 2025 dashboard |
| 18 | [`adastra2026/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/connect.php) | Multi-year (`byo2019`, `byo2020`, `byo2021`, `byo2023`, `byo2024`, `byo2025`, `byo2026`) | `adastra2026/dbcon.php`, `adastra2026/passreq.php`, etc. |
| 19 | [`adastra2026/adastra/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/connect.php) | Multi-year yearbook and recruitment databases | `adastra2026/adastra/php/*.php` admin endpoints |
| 20 | [`adastra2026/adastra/dashboard/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/connect.php) | `theadast_byo2026` | `adastra2026/adastra/dashboard/php/*.php` endpoints |
| 21 | [`adastra2026/adastra/dashboard/write-up/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/write-up/connect.php) | `theadast_byo2026` | Write-up editing and submission handlers |
| 22 | [`adastra2027/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/connect.php) | Multi-year (`byo2019` through `byo2027`) | `adastra2027/dbcon.php`, `adastra2027/passreq.php`, etc. |
| 23 | [`adastra2027/adastra/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/connect.php) | Multi-year yearbook and recruitment databases | `adastra2027/adastra/php/*.php` admin endpoints |
| 24 | [`adastra2027/adastra/dashboard/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/connect.php) | `theadast_byo2027` | `adastra2027/adastra/dashboard/php/*.php` endpoints |
| 25 | [`adastra2027/adastra/dashboard/write-up/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/write-up/connect.php) | `theadast_byo2027` | Write-up editing and submission handlers |
| 26 | [`fragment202122/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/connect.php) | `theadast_byo2021`, `byo2020`, `byo2019`, `byo2018`, `byo2016` | `saveRegistration.php`, `testStudentNumber.php`, etc. |
| 27 | [`fragment202122/adastra/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/connect.php) | Multi-year yearbook databases | `fragment202122/adastra/php/*.php` admin endpoints |
| 28 | [`fragment202122/adastra/dashboard/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/connect.php) | `theadast_byo2021` | `fragment202122/adastra/dashboard/php/*.php` endpoints |
| 29 | [`gpp_2023/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/connect.php) | Multi-year (`byo2016`..`2024`, `recruitment`) | Included by scripts in `gpp_2023/php/` |
| 30 | [`gpp_2024/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/connect.php) | Multi-year (`byo2016`..`2024`, `recruitment`) | Included by scripts in `gpp_2024/php/` |
| 31 | [`gpp_2025/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/connect.php) | Multi-year (`byo2016`..`2024`, `recruitment`) | Included by scripts in `gpp_2025/php/` |
| 32 | [`gpp_2026/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/connect.php) | Multi-year (`byo2016`..`2024`, `recruitment`) | Included by scripts in `gpp_2026/php/` |
| 33 | [`gpp_claiming/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/connect.php) | Multi-year (`byo2016`..`2024`, `recruitment`) | Included by scripts in `gpp_claiming/php/` |
| 34 | [`gpp_claiming/gpp_php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/connect.php) | `theadast_byo2024` | Included by active appointment scheduling scripts in `gpp_php/` |
| 35 | [`gpp_fragment/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/connect.php) | Multi-year (`byo2016`..`2024`, `recruitment`) | Included by scripts in `gpp_fragment/php/` |
| 36 | [`2026ticket/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/2026ticket/php/connect.php) | `theadast_byo2026` | Available for ticketing scripts in 2026ticket |
| 37 | [`2027ticket/php/connect.php`](file:///Users/lei/Downloads/adastra/manualtest/2027ticket/php/connect.php) | `theadast_byo2027` | Available for ticketing scripts in 2027ticket |

### 2.4 Custom Database Wrappers and Their Callers (25 Files)

Four custom abstraction classes and form handlers establish database connections outside `connect.php`, called by 21 specific scripts:

#### 1. `obra2019/includes/database_master.php` (DatabaseMaster Class)
- **Definition:** [`obra2019/includes/database_master.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/database_master.php)
- **Credentials Encapsulated:** `$host = 'localhost'`, `$user = 'theadast'`, `$password = 'Org Y 2017!'`, `$db_name = 'theadast_byo2019'` via `mysqli_connect`.
- **Direct Callers (15 scripts in `obra2019/`):**
  - [`obra2019/addSubmission.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/addSubmission.php)
  - [`obra2019/addPIS.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/addPIS.php)
  - [`obra2019/upload_personal_photo.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/upload_personal_photo.php)
  - [`obra2019/idVerification.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/idVerification.php)
  - [`obra2019/editProfile.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/editProfile.php)
  - [`obra2019/pictorial_conforme.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/pictorial_conforme.php)
  - [`obra2019/schedulePictorial_action.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/schedulePictorial_action.php)
  - [`obra2019/flag_photo.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/flag_photo.php)
  - [`obra2019/reload_photos.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/reload_photos.php)
  - [`obra2019/includes/fetch_data.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/fetch_data.php)
  - [`obra2019/includes/home.inc.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/home.inc.php)
  - [`obra2019/includes/landingpage.inc.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/landingpage.inc.php)
  - [`obra2019/includes/landingpage-open.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/landingpage-open.php)
  - [`obra2019/includes/conforme.inc.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/conforme.inc.php)
  - [`obra2019/includes/landingpage-aj.inc.php`](file:///Users/lei/Downloads/adastra/manualtest/obra2019/includes/landingpage-aj.inc.php)

#### 2. `fragment202122/databaseWrapper.php` (DatabaseWrapper Class)
- **Definition:** [`fragment202122/databaseWrapper.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/databaseWrapper.php)
- **Credentials Encapsulated:** `$host = 'localhost'`, `$user = 'theadast'`, `$password = 'Org Y 2017!'`, `$databaseName = 'theadast_byo2021'` via `mysqli_connect`.
- **Direct Callers (3 scripts):**
  - [`fragment202122/processRegistration.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/processRegistration.php)
  - [`fragment202122/approveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/approveUser.php)
  - [`fragment202122/fragment-cs.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/fragment-cs.php)

#### 3. `fragment202122/fragmenttest/databaseWrapper.php` (Test Duplicate Wrapper)
- **Definition:** [`fragment202122/fragmenttest/databaseWrapper.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/fragmenttest/databaseWrapper.php)
- **Direct Callers (3 scripts in test clone):**
  - [`fragment202122/fragmenttest/processRegistration.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/fragmenttest/processRegistration.php)
  - [`fragment202122/fragmenttest/approveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/fragmenttest/approveUser.php)
  - [`fragment202122/fragmenttest/fragment-cs.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/fragmenttest/fragment-cs.php)

#### 4. `fragment202122/recruitmentTest/connection.php`
- **Definition:** [`fragment202122/recruitmentTest/connection.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/recruitmentTest/connection.php)
- **Details:** Standalone form handler with hardcoded `$conn = new mysqli('localhost','theadast_jad','??LS6_p&DJY=','theadast_recruitment2021');`.

### 2.5 Files Calling the Database via `connect.php` (1,354 Files)

A total of **1,354 files** call the database by including `connect.php` (`include "php/connect.php"`, `include "connect.php"`, `require_once "connect.php"`).

Below is the breakdown across all directory modules:

| Directory Module | File Count | Primary Functionality & Key Files |
| :--- | :---: | :--- |
| `admin` | **308** | Central admin approval interfaces, photo manager, schedule manager, user manager, and backend API endpoints (`admin/php/*.php`, `admin/php/2025/`, `admin/php/2026/`, `admin/php/2027/`) |
| `adastra2025` | **105** | `adastra2025/dbcon.php`, subscription scripts, write-up APIs, and dashboard APIs (`adastra2025/adastra/dashboard/php/*.php`) |
| `adastra2024` | **93** | `adastra2024/dbcon.php`, subscription scripts, and dashboard APIs (`adastra2024/adastra/dashboard/php/*.php`) |
| `adastra2023` | **92** | `adastra2023/dbcon.php`, subscription scripts, and dashboard APIs (`adastra2023/adastra/dashboard/php/*.php`) |
| `adastra2026` | **86** | `adastra2026/dbcon.php`, subscription actions, and dashboard APIs (`adastra2026/adastra/dashboard/php/*.php`) |
| `adastra2027` | **85** | `adastra2027/dbcon.php`, subscription actions, and dashboard APIs (`adastra2027/adastra/dashboard/php/*.php`) |
| `gpp_claiming` | **102** | `gpp_claiming/gpp_php/*.php` active endpoints & `gpp_claiming/php/*.php` |
| `fragment202122` | **73** | `fragment202122/saveRegistration.php`, subscription tools, and admin APIs (`adastra/dashboard/php/*.php`) |
| `gpp_2023` | **82** | `gpp_2023/php/*.php` backend endpoints |
| `gpp_2024` | **82** | `gpp_2024/php/*.php` backend endpoints |
| `gpp_2025` | **82** | `gpp_2025/php/*.php` backend endpoints |
| `gpp_2026` | **82** | `gpp_2026/php/*.php` backend endpoints |
| `gpp_fragment` | **82** | `gpp_fragment/php/*.php` backend endpoints |
| **Total** | **1,354** | |

#### Module `admin` (308 files)
<details>
<summary>Click to view all 308 files in `admin` calling `connect.php`</summary>

- [`admin/PHPExcel-1.8/Examples/01simple-download-xls.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/Examples/01simple-download-xls.php)
- [`admin/PHPExcel-1.8/Examples/01simple-download-xlsx.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/Examples/01simple-download-xlsx.php)
- [`admin/PHPExcel-1.8/Examples/refunds-xlsx.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/Examples/refunds-xlsx.php)
- [`admin/PHPExcel-1.8/Examples/subscribers-202122.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/Examples/subscribers-202122.php)
- [`admin/PHPExcel-1.8/Examples/subscribers-2022.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/Examples/subscribers-2022.php)
- [`admin/PHPExcel-1.8/Examples/subscribers-2023.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/Examples/subscribers-2023.php)
- [`admin/PHPExcel-1.8/Examples/subscribers-2024.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/Examples/subscribers-2024.php)
- [`admin/PHPExcel-1.8/Examples/subscribers-2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/Examples/subscribers-2025.php)
- [`admin/PHPExcel-1.8/subscribers-2022.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/PHPExcel-1.8/subscribers-2022.php)
- [`admin/dashboard-claiming.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/dashboard-claiming.php)
- [`admin/directory-announcements.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/directory-announcements.php)
- [`admin/directory-campaign.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/directory-campaign.php)
- [`admin/directory-office.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/directory-office.php)
- [`admin/directory-ontrack.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/directory-ontrack.php)
- [`admin/directory-projects.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/directory-projects.php)
- [`admin/directory-recruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/directory-recruitment.php)
- [`admin/directory-segments.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/directory-segments.php)
- [`admin/directory-yearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/directory-yearbook.php)
- [`admin/filterFolder.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/filterFolder.php)
- [`admin/gpp-appointment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/gpp-appointment.php)
- [`admin/gpp_scheduling.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/gpp_scheduling.php)
- [`admin/late-deductibles-php/late_table.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/late-deductibles-php/late_table.php)
- [`admin/photo-manager/loadSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/photo-manager/loadSubscribers.php)
- [`admin/photo-manager/loadTable.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/photo-manager/loadTable.php)
- [`admin/php/2025/approvePayment2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/approvePayment2025.php)
- [`admin/php/2025/approveProof_2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/approveProof_2025.php)
- [`admin/php/2025/approveReviseNumber2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/approveReviseNumber2025.php)
- [`admin/php/2025/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/approveReviseWriteUp.php)
- [`admin/php/2025/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/approveWriteUp.php)
- [`admin/php/2025/disapprovePayment2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/disapprovePayment2025.php)
- [`admin/php/2025/disapproveProof_2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/disapproveProof_2025.php)
- [`admin/php/2025/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/disapproveWriteUp.php)
- [`admin/php/2025/pull2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/pull2025.php)
- [`admin/php/2025/pullPaymentApproval2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/pullPaymentApproval2025.php)
- [`admin/php/2025/pullProofApproval2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/pullProofApproval2025.php)
- [`admin/php/2025/pullWriteUpApproval2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/pullWriteUpApproval2025.php)
- [`admin/php/2025/pullWriteUps2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2025/pullWriteUps2025.php)
- [`admin/php/2026/approvePayment2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/approvePayment2026.php)
- [`admin/php/2026/approveProof_2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/approveProof_2026.php)
- [`admin/php/2026/approveReviseNumber2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/approveReviseNumber2026.php)
- [`admin/php/2026/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/approveReviseWriteUp.php)
- [`admin/php/2026/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/approveWriteUp.php)
- [`admin/php/2026/disapprovePayment2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/disapprovePayment2026.php)
- [`admin/php/2026/disapproveProof_2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/disapproveProof_2026.php)
- [`admin/php/2026/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/disapproveWriteUp.php)
- [`admin/php/2026/pull2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/pull2026.php)
- [`admin/php/2026/pullPaymentApproval2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/pullPaymentApproval2026.php)
- [`admin/php/2026/pullProofApproval2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/pullProofApproval2026.php)
- [`admin/php/2026/pullWriteUpApproval2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/pullWriteUpApproval2026.php)
- [`admin/php/2026/pullWriteUps2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2026/pullWriteUps2026.php)
- [`admin/php/2027/approvePayment2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/approvePayment2027.php)
- [`admin/php/2027/approveProof_2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/approveProof_2027.php)
- [`admin/php/2027/approveReviseNumber2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/approveReviseNumber2027.php)
- [`admin/php/2027/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/approveReviseWriteUp.php)
- [`admin/php/2027/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/approveWriteUp.php)
- [`admin/php/2027/disapprovePayment2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/disapprovePayment2027.php)
- [`admin/php/2027/disapproveProof_2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/disapproveProof_2027.php)
- [`admin/php/2027/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/disapproveWriteUp.php)
- [`admin/php/2027/pull2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/pull2027.php)
- [`admin/php/2027/pullPaymentApproval2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/pullPaymentApproval2027.php)
- [`admin/php/2027/pullProofApproval2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/pullProofApproval2027.php)
- [`admin/php/2027/pullWriteUpApproval2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/pullWriteUpApproval2027.php)
- [`admin/php/2027/pullWriteUps2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/2027/pullWriteUps2027.php)
- [`admin/php/CopyUpdate.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/CopyUpdate.php)
- [`admin/php/UpdateDisplayTableSec_GPP.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/UpdateDisplayTableSec_GPP.php)
- [`admin/php/addCabinet.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/addCabinet.php)
- [`admin/php/addNewYearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/addNewYearbook.php)
- [`admin/php/addYearbookDetails.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/addYearbookDetails.php)
- [`admin/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approvePayment.php)
- [`admin/php/approveProof.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveProof.php)
- [`admin/php/approveProof_2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveProof_2025.php)
- [`admin/php/approveRefund.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveRefund.php)
- [`admin/php/approveRefund2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveRefund2025.php)
- [`admin/php/approveRefund2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveRefund2026.php)
- [`admin/php/approveRefund2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveRefund2027.php)
- [`admin/php/approveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveResched.php)
- [`admin/php/approveReviseNumber.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveReviseNumber.php)
- [`admin/php/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveReviseWriteUp.php)
- [`admin/php/approveTransfer.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveTransfer.php)
- [`admin/php/approveTransfer2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveTransfer2025.php)
- [`admin/php/approveTransfer2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveTransfer2026.php)
- [`admin/php/approveTransfer2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveTransfer2027.php)
- [`admin/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/approveWriteUp.php)
- [`admin/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/changePassword.php)
- [`admin/php/completeRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/completeRequest.php)
- [`admin/php/deductibles.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/deductibles.php)
- [`admin/php/disableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disableAccount.php)
- [`admin/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapprovePayment.php)
- [`admin/php/disapproveProof.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveProof.php)
- [`admin/php/disapproveProof_2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveProof_2025.php)
- [`admin/php/disapproveRefund.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveRefund.php)
- [`admin/php/disapproveRefund2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveRefund2025.php)
- [`admin/php/disapproveRefund2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveRefund2026.php)
- [`admin/php/disapproveRefund2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveRefund2027.php)
- [`admin/php/disapproveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveResched.php)
- [`admin/php/disapproveTransfer.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveTransfer.php)
- [`admin/php/disapproveTransfer2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveTransfer2025.php)
- [`admin/php/disapproveTransfer2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveTransfer2026.php)
- [`admin/php/disapproveTransfer2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveTransfer2027.php)
- [`admin/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/disapproveWriteUp.php)
- [`admin/php/editUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/editUserPermissions.php)
- [`admin/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/emailForgot.php)
- [`admin/php/enableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/enableAccount.php)
- [`admin/php/fetchGppScheduleTable.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/fetchGppScheduleTable.php)
- [`admin/php/get_signature.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/get_signature.php)
- [`admin/php/listSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/listSchedules.php)
- [`admin/php/listSchedules2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/listSchedules2.php)
- [`admin/php/listSchedules3.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/listSchedules3.php)
- [`admin/php/listSchedules4.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/listSchedules4.php)
- [`admin/php/listSchedulesGPP.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/listSchedulesGPP.php)
- [`admin/php/listSchedulesGPP_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/listSchedulesGPP_v1.php)
- [`admin/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/loadWriteUpHistory.php)
- [`admin/php/mainwebsite/announcements/pullAnnouncement.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/announcements/pullAnnouncement.php)
- [`admin/php/mainwebsite/campaign/pullCampaign.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/campaign/pullCampaign.php)
- [`admin/php/mainwebsite/office/pullOffice.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/office/pullOffice.php)
- [`admin/php/mainwebsite/ontrack/pullOntrack.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/ontrack/pullOntrack.php)
- [`admin/php/mainwebsite/projects/pullProjects.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/projects/pullProjects.php)
- [`admin/php/mainwebsite/pullPartners.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/pullPartners.php)
- [`admin/php/mainwebsite/pullSegment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/pullSegment.php)
- [`admin/php/mainwebsite/recruitment/pullRecruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/recruitment/pullRecruitment.php)
- [`admin/php/mainwebsite/toggleAcknowledgement.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/toggleAcknowledgement.php)
- [`admin/php/mainwebsite/yearbook/pullYearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/mainwebsite/yearbook/pullYearbook.php)
- [`admin/php/newpassword.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/newpassword.php)
- [`admin/php/options.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/options.php)
- [`admin/php/optionscopy.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/optionscopy.php)
- [`admin/php/pull2023.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2023.php)
- [`admin/php/pull2023Payments.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2023Payments.php)
- [`admin/php/pull2024.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2024.php)
- [`admin/php/pull2024Payments.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2024Payments.php)
- [`admin/php/pull2024Proof.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2024Proof.php)
- [`admin/php/pull2024V2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2024V2.php)
- [`admin/php/pull2025V1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2025V1.php)
- [`admin/php/pull2025V2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2025V2.php)
- [`admin/php/pull2025V3.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2025V3.php)
- [`admin/php/pull2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2026.php)
- [`admin/php/pull2026V1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pull2026V1.php)
- [`admin/php/pullA&D.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullA&D.php)
- [`admin/php/pullApplicants.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants.php)
- [`admin/php/pullApplicants1T2324.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants1T2324.php)
- [`admin/php/pullApplicants1T2425.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants1T2425.php)
- [`admin/php/pullApplicants1T2526.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants1T2526.php)
- [`admin/php/pullApplicants2T.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants2T.php)
- [`admin/php/pullApplicants2T2425.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants2T2425.php)
- [`admin/php/pullApplicants2T2526.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants2T2526.php)
- [`admin/php/pullApplicants3T2425.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants3T2425.php)
- [`admin/php/pullApplicants3T2526.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullApplicants3T2526.php)
- [`admin/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullBrowse.php)
- [`admin/php/pullCS.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullCS.php)
- [`admin/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullCalendar.php)
- [`admin/php/pullCheckin.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullCheckin.php)
- [`admin/php/pullClaim.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullClaim.php)
- [`admin/php/pullClassicPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullClassicPayments.php)
- [`admin/php/pullClassicSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullClassicSubscribers.php)
- [`admin/php/pullConDev.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullConDev.php)
- [`admin/php/pullConcerns.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullConcerns.php)
- [`admin/php/pullDashboard2024-v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDashboard2024-v1.php)
- [`admin/php/pullDashboard2024.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDashboard2024.php)
- [`admin/php/pullDashboard2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDashboard2025.php)
- [`admin/php/pullDashboard2026-v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDashboard2026-v1.php)
- [`admin/php/pullDashboard2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDashboard2026.php)
- [`admin/php/pullDashboard2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDashboard2027.php)
- [`admin/php/pullDashboardClaiming.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDashboardClaiming.php)
- [`admin/php/pullDashboardOG.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDashboardOG.php)
- [`admin/php/pullDimensionsSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullDimensionsSubscribers.php)
- [`admin/php/pullFeedbacks.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullFeedbacks.php)
- [`admin/php/pullFragmentSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullFragmentSubscribers.php)
- [`admin/php/pullGPPDashboard2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullGPPDashboard2025.php)
- [`admin/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullLogs.php)
- [`admin/php/pullLuxeoPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullLuxeoPayments.php)
- [`admin/php/pullLuxeoSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullLuxeoSubscribers.php)
- [`admin/php/pullManageSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullManageSchedules.php)
- [`admin/php/pullManageSchedulesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullManageSchedulesOnline.php)
- [`admin/php/pullManageTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullManageTimeSlot.php)
- [`admin/php/pullManageTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullManageTimeSlotOnline.php)
- [`admin/php/pullManaging.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullManaging.php)
- [`admin/php/pullMarketing.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullMarketing.php)
- [`admin/php/pullObraPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullObraPayments.php)
- [`admin/php/pullObraSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullObraSubscribers.php)
- [`admin/php/pullOnlineUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullOnlineUploads.php)
- [`admin/php/pullOrHistory2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullOrHistory2025.php)
- [`admin/php/pullOrHistory2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullOrHistory2026.php)
- [`admin/php/pullOrHistory2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullOrHistory2027.php)
- [`admin/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullPaymentApproval.php)
- [`admin/php/pullPaymentApproval2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullPaymentApproval2025.php)
- [`admin/php/pullPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullPayments.php)
- [`admin/php/pullPhoto.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullPhoto.php)
- [`admin/php/pullPopHistory2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullPopHistory2025.php)
- [`admin/php/pullPopHistory2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullPopHistory2026.php)
- [`admin/php/pullPopHistory2026_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullPopHistory2026_TEST.php)
- [`admin/php/pullPopHistory2027_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullPopHistory2027_TEST.php)
- [`admin/php/pullProjectionSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullProjectionSubscribers.php)
- [`admin/php/pullProofApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullProofApproval.php)
- [`admin/php/pullRecruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRecruitment.php)
- [`admin/php/pullRefund.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRefund.php)
- [`admin/php/pullRefund2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRefund2025.php)
- [`admin/php/pullRefund2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRefund2026.php)
- [`admin/php/pullRefund2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRefund2027.php)
- [`admin/php/pullRefundApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRefundApproval.php)
- [`admin/php/pullRefundApproval2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRefundApproval2025.php)
- [`admin/php/pullRefundApproval2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRefundApproval2026.php)
- [`admin/php/pullRefundApproval2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullRefundApproval2027.php)
- [`admin/php/pullReschedApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullReschedApproval.php)
- [`admin/php/pullScheduleAppointments.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullScheduleAppointments.php)
- [`admin/php/pullScheduleAppointmentsOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullScheduleAppointmentsOnline.php)
- [`admin/php/pullScheduleDates.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullScheduleDates.php)
- [`admin/php/pullScheduleDatesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullScheduleDatesOnline.php)
- [`admin/php/pullScheduleTimes.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullScheduleTimes.php)
- [`admin/php/pullScheduleTimesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullScheduleTimesOnline.php)
- [`admin/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullSchedules.php)
- [`admin/php/pullSchools.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullSchools.php)
- [`admin/php/pullSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullSecondPayment.php)
- [`admin/php/pullSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullSubscribers.php)
- [`admin/php/pullTickets.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTickets.php)
- [`admin/php/pullTransfer.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransfer.php)
- [`admin/php/pullTransfer2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransfer2025.php)
- [`admin/php/pullTransfer2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransfer2026.php)
- [`admin/php/pullTransfer2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransfer2027.php)
- [`admin/php/pullTransferApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransferApproval.php)
- [`admin/php/pullTransferApproval2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransferApproval2025.php)
- [`admin/php/pullTransferApproval2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransferApproval2026.php)
- [`admin/php/pullTransferApproval2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransferApproval2027.php)
- [`admin/php/pullTransfers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullTransfers.php)
- [`admin/php/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullUploads.php)
- [`admin/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullUserList.php)
- [`admin/php/pullUserPermissions-og.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullUserPermissions-og.php)
- [`admin/php/pullUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullUserPermissions.php)
- [`admin/php/pullVideo.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullVideo.php)
- [`admin/php/pullWebDev.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWebDev.php)
- [`admin/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUpApproval.php)
- [`admin/php/pullWriteUps.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps.php)
- [`admin/php/pullWriteUps2016.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2016.php)
- [`admin/php/pullWriteUps2017.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2017.php)
- [`admin/php/pullWriteUps2018.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2018.php)
- [`admin/php/pullWriteUps2019.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2019.php)
- [`admin/php/pullWriteUps2020.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2020.php)
- [`admin/php/pullWriteUps2023.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2023.php)
- [`admin/php/pullWriteUps2024.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2024.php)
- [`admin/php/pullWriteUps2024V2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2024V2.php)
- [`admin/php/pullWriteUps2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2025.php)
- [`admin/php/pullWriteUps2026V2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUps2026V2.php)
- [`admin/php/pullWriteUpsTest.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteUpsTest.php)
- [`admin/php/pullWriteups2025V2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullWriteups2025V2.php)
- [`admin/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/pullinfo.php)
- [`admin/php/resetStudentPass-Luxeo.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/resetStudentPass-Luxeo.php)
- [`admin/php/resetStudentPass-fragment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/resetStudentPass-fragment.php)
- [`admin/php/resetStudentPass.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/resetStudentPass.php)
- [`admin/php/resetStudentPass2023.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/resetStudentPass2023.php)
- [`admin/php/resetStudentPass2024.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/resetStudentPass2024.php)
- [`admin/php/resetStudentPass2025.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/resetStudentPass2025.php)
- [`admin/php/resetStudentPass2026.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/resetStudentPass2026.php)
- [`admin/php/resetStudentPass2027.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/resetStudentPass2027.php)
- [`admin/php/retrieveExamDetails.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/retrieveExamDetails.php)
- [`admin/php/retrieveExamDetails_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/retrieveExamDetails_v1.php)
- [`admin/php/retrieveExamDetails_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/retrieveExamDetails_v2.php)
- [`admin/php/retrieveExamDetails_v3.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/retrieveExamDetails_v3.php)
- [`admin/php/sample.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/sample.php)
- [`admin/php/saveManageAddSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/saveManageAddSchedule.php)
- [`admin/php/saveManageAddScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/saveManageAddScheduleOnline.php)
- [`admin/php/saveSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/saveSchedule.php)
- [`admin/php/saveScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/saveScheduleOnline.php)
- [`admin/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/saveUser.php)
- [`admin/php/saveUser_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/saveUser_v2.php)
- [`admin/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/saveWriteUp.php)
- [`admin/php/setScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/setScheduleActive.php)
- [`admin/php/setScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/setScheduleActiveOnline.php)
- [`admin/php/setScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/setScheduleInActive.php)
- [`admin/php/setScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/setScheduleInActiveOnline.php)
- [`admin/php/setSingleScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/setSingleScheduleActive.php)
- [`admin/php/setSingleScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/setSingleScheduleActiveOnline.php)
- [`admin/php/setSingleScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/setSingleScheduleInActive.php)
- [`admin/php/setSingleScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/setSingleScheduleInActiveOnline.php)
- [`admin/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/testAdminLogin.php)
- [`admin/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/testLogin.php)
- [`admin/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/testUsername.php)
- [`admin/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/testWriteUpCount.php)
- [`admin/php/updateConcern.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateConcern.php)
- [`admin/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateContact.php)
- [`admin/php/updateDisplayTable.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateDisplayTable.php)
- [`admin/php/updateDisplayTableSec.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateDisplayTableSec.php)
- [`admin/php/updateDisplayTableSecV2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateDisplayTableSecV2.php)
- [`admin/php/updateDisplayTableSec_OG.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateDisplayTableSec_OG.php)
- [`admin/php/updateDisplayTableThird.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateDisplayTableThird.php)
- [`admin/php/updateScore.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateScore.php)
- [`admin/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updateShipping.php)
- [`admin/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/updatepassword.php)
- [`admin/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/php/uploadPayment.php)
- [`admin/pictorial-copy.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/pictorial-copy.php)
- [`admin/pictorial_scheduling.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/pictorial_scheduling.php)
- [`admin/pictorial_scheduling_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/pictorial_scheduling_v2.php)
- [`admin/pictorial_scheduling_v3.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/pictorial_scheduling_v3.php)
- [`admin/schedule-add.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-add.php)
- [`admin/schedule-attendance-v2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-attendance-v2.php)
- [`admin/schedule-attendance.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-attendance.php)
- [`admin/schedule-manager-fix.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-fix.php)
- [`admin/schedule-manager-php/almosthere.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php/almosthere.php)
- [`admin/schedule-manager-php/changeMode.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php/changeMode.php)
- [`admin/schedule-manager-php/deductible.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php/deductible.php)
- [`admin/schedule-manager-php/deductibles.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php/deductibles.php)
- [`admin/schedule-manager-php/fix.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php/fix.php)
- [`admin/schedule-manager-php/updateDisplayTable.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php/updateDisplayTable.php)
- [`admin/schedule-manager-php/updateDisplayTable2.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/schedule-manager-php/updateDisplayTable2.php)
- [`admin/sm-fix.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/sm-fix.php)
- [`admin/studio-file-manager-2027/populate.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/studio-file-manager-2027/populate.php)
- [`admin/updatepass.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/updatepass.php)
- [`admin/updatepassdupe.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/updatepassdupe.php)
- [`admin/yearbook-manager/loadLuxeo.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/yearbook-manager/loadLuxeo.php)
- [`admin/yearbook-manager/loadObra.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/yearbook-manager/loadObra.php)
- [`admin/yearbook-manager/loadSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/admin/yearbook-manager/loadSubscribers.php)

</details>

#### Module `adastra2025` (105 files)
<details>
<summary>Click to view all 105 files in `adastra2025` calling `connect.php`</summary>

- [`adastra2025/adastra/dashboard/backup.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/backup.php)
- [`adastra2025/adastra/dashboard/gpp_php/dateChecker.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/dateChecker.php)
- [`adastra2025/adastra/dashboard/gpp_php/deleteSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/deleteSched.php)
- [`adastra2025/adastra/dashboard/gpp_php/deleteSchedOrig.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/deleteSchedOrig.php)
- [`adastra2025/adastra/dashboard/gpp_php/getSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/getSched.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullCalendar.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullList.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullListOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullListOnline.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullReservation.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullReservationOnline.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullSchedules.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullTimeSlot.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullTimeSlotOnline.php)
- [`adastra2025/adastra/dashboard/gpp_php/pullTimeSlot_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/pullTimeSlot_v1.php)
- [`adastra2025/adastra/dashboard/gpp_php/saveOthers.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/saveOthers.php)
- [`adastra2025/adastra/dashboard/gpp_php/saveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/saveResched.php)
- [`adastra2025/adastra/dashboard/gpp_php/saveReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/saveReservation.php)
- [`adastra2025/adastra/dashboard/gpp_php/saveReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/saveReservationOnline.php)
- [`adastra2025/adastra/dashboard/gpp_php/saveSubscriberSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/gpp_php/saveSubscriberSchedule.php)
- [`adastra2025/adastra/dashboard/my-photos/check-photo-types.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/check-photo-types.php)
- [`adastra2025/adastra/dashboard/my-photos/checkComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/checkComplete.php)
- [`adastra2025/adastra/dashboard/my-photos/finalizeSelection.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/finalizeSelection.php)
- [`adastra2025/adastra/dashboard/my-photos/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/index.php)
- [`adastra2025/adastra/dashboard/my-photos/onlineSelect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/onlineSelect.php)
- [`adastra2025/adastra/dashboard/my-photos/photo-diagnostic.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/photo-diagnostic.php)
- [`adastra2025/adastra/dashboard/my-photos/proceed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/proceed.php)
- [`adastra2025/adastra/dashboard/my-photos/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/pullUploads.php)
- [`adastra2025/adastra/dashboard/my-photos/saveFeedback.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/saveFeedback.php)
- [`adastra2025/adastra/dashboard/my-photos/updateSelected.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/updateSelected.php)
- [`adastra2025/adastra/dashboard/my-photos/upload-photos-og.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/upload-photos-og.php)
- [`adastra2025/adastra/dashboard/my-photos/uploadPhoto-BACKUP.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/uploadPhoto-BACKUP.php)
- [`adastra2025/adastra/dashboard/my-photos/uploadPhoto-OLD.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/uploadPhoto-OLD.php)
- [`adastra2025/adastra/dashboard/my-photos/uploadPhoto.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/uploadPhoto.php)
- [`adastra2025/adastra/dashboard/my-photos/verifyComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/my-photos/verifyComplete.php)
- [`adastra2025/adastra/dashboard/php/dateChecker.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/dateChecker.php)
- [`adastra2025/adastra/dashboard/php/deleteSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/deleteSched.php)
- [`adastra2025/adastra/dashboard/php/deleteSchedOrig.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/deleteSchedOrig.php)
- [`adastra2025/adastra/dashboard/php/getSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/getSched.php)
- [`adastra2025/adastra/dashboard/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullCalendar.php)
- [`adastra2025/adastra/dashboard/php/pullList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullList.php)
- [`adastra2025/adastra/dashboard/php/pullListOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullListOnline.php)
- [`adastra2025/adastra/dashboard/php/pullReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullReservation.php)
- [`adastra2025/adastra/dashboard/php/pullReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullReservationOnline.php)
- [`adastra2025/adastra/dashboard/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullSchedules.php)
- [`adastra2025/adastra/dashboard/php/pullTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullTimeSlot.php)
- [`adastra2025/adastra/dashboard/php/pullTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullTimeSlotOnline.php)
- [`adastra2025/adastra/dashboard/php/pullTimeSlot_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/pullTimeSlot_v1.php)
- [`adastra2025/adastra/dashboard/php/saveOthers.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/saveOthers.php)
- [`adastra2025/adastra/dashboard/php/saveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/saveResched.php)
- [`adastra2025/adastra/dashboard/php/saveReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/saveReservation.php)
- [`adastra2025/adastra/dashboard/php/saveReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/saveReservationOnline.php)
- [`adastra2025/adastra/dashboard/php/saveSubscriberSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/php/saveSubscriberSchedule.php)
- [`adastra2025/adastra/dashboard/profile/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/profile/index.php)
- [`adastra2025/adastra/dashboard/testfile.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/testfile.php)
- [`adastra2025/adastra/dashboard/write-up/closed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/write-up/closed.php)
- [`adastra2025/adastra/dashboard/write-up/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/write-up/index.php)
- [`adastra2025/adastra/dashboard/write-up/indexV2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/write-up/indexV2.php)
- [`adastra2025/adastra/dashboard/yearbook-status/digital-yearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/dashboard/yearbook-status/digital-yearbook.php)
- [`adastra2025/adastra/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/approvePayment.php)
- [`adastra2025/adastra/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/approveWriteUp.php)
- [`adastra2025/adastra/php/bankRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/bankRequest.php)
- [`adastra2025/adastra/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/changePassword.php)
- [`adastra2025/adastra/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/disapprovePayment.php)
- [`adastra2025/adastra/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/disapproveWriteUp.php)
- [`adastra2025/adastra/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/emailForgot.php)
- [`adastra2025/adastra/php/letterRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/letterRequest.php)
- [`adastra2025/adastra/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/loadWriteUpHistory.php)
- [`adastra2025/adastra/php/parentRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/parentRequest.php)
- [`adastra2025/adastra/php/processSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/processSecondPayment.php)
- [`adastra2025/adastra/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullBrowse.php)
- [`adastra2025/adastra/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullDashboard.php)
- [`adastra2025/adastra/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullLogs.php)
- [`adastra2025/adastra/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullPaymentApproval.php)
- [`adastra2025/adastra/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullUserList.php)
- [`adastra2025/adastra/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullWriteUpApproval.php)
- [`adastra2025/adastra/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullinfo.php)
- [`adastra2025/adastra/php/pullinfo_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullinfo_v2.php)
- [`adastra2025/adastra/php/pullinfo_v2_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullinfo_v2_TEST.php)
- [`adastra2025/adastra/php/pullinfo_v3.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullinfo_v3.php)
- [`adastra2025/adastra/php/pullinfoorg.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/pullinfoorg.php)
- [`adastra2025/adastra/php/refundRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/refundRequest.php)
- [`adastra2025/adastra/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/saveUser.php)
- [`adastra2025/adastra/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/saveWriteUp.php)
- [`adastra2025/adastra/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/testAdminLogin.php)
- [`adastra2025/adastra/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/testCheckIn.php)
- [`adastra2025/adastra/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/testLogin.php)
- [`adastra2025/adastra/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/testUsername.php)
- [`adastra2025/adastra/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/testWriteUpCount.php)
- [`adastra2025/adastra/php/transferRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/transferRequest.php)
- [`adastra2025/adastra/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/updateContact.php)
- [`adastra2025/adastra/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/updateShipping.php)
- [`adastra2025/adastra/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/updatepassword.php)
- [`adastra2025/adastra/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/uploadPayment.php)
- [`adastra2025/adastra/php/uploadProof.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/adastra/php/uploadProof.php)
- [`adastra2025/check_email.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/check_email.php)
- [`adastra2025/check_emailAd.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/check_emailAd.php)
- [`adastra2025/check_id_number.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/check_id_number.php)
- [`adastra2025/dbcon.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/dbcon.php)
- [`adastra2025/php/esig.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/esig.php)
- [`adastra2025/php/forgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/forgot.php)
- [`adastra2025/php/sendsubinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/sendsubinfo.php)
- [`adastra2025/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/testLogin.php)
- [`adastra2025/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/php/updatepassword.php)
- [`adastra2025/sendsubinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/sendsubinfo.php)
- [`adastra2025/updatepass.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2025/updatepass.php)

</details>

#### Module `adastra2024` (93 files)
<details>
<summary>Click to view all 93 files in `adastra2024` calling `connect.php`</summary>

- [`adastra2024/adastra/PHPExcel-1.8/Examples/01simple-download-xlsx.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/PHPExcel-1.8/Examples/01simple-download-xlsx.php)
- [`adastra2024/adastra/dashboard/backup.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/backup.php)
- [`adastra2024/adastra/dashboard/my-photos/checkComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/checkComplete.php)
- [`adastra2024/adastra/dashboard/my-photos/finalizeSelection.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/finalizeSelection.php)
- [`adastra2024/adastra/dashboard/my-photos/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/index.php)
- [`adastra2024/adastra/dashboard/my-photos/onlineSelect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/onlineSelect.php)
- [`adastra2024/adastra/dashboard/my-photos/proceed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/proceed.php)
- [`adastra2024/adastra/dashboard/my-photos/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/pullUploads.php)
- [`adastra2024/adastra/dashboard/my-photos/saveFeedback.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/saveFeedback.php)
- [`adastra2024/adastra/dashboard/my-photos/updateSelected.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/updateSelected.php)
- [`adastra2024/adastra/dashboard/my-photos/uploadPhoto.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/uploadPhoto.php)
- [`adastra2024/adastra/dashboard/my-photos/verifyComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/my-photos/verifyComplete.php)
- [`adastra2024/adastra/dashboard/photoshoot-schedule/recruitmentmailblast_copy.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/photoshoot-schedule/recruitmentmailblast_copy.php)
- [`adastra2024/adastra/dashboard/php/dateChecker.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/dateChecker.php)
- [`adastra2024/adastra/dashboard/php/deleteSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/deleteSched.php)
- [`adastra2024/adastra/dashboard/php/deleteSchedOrig.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/deleteSchedOrig.php)
- [`adastra2024/adastra/dashboard/php/getSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/getSched.php)
- [`adastra2024/adastra/dashboard/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/pullCalendar.php)
- [`adastra2024/adastra/dashboard/php/pullList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/pullList.php)
- [`adastra2024/adastra/dashboard/php/pullListOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/pullListOnline.php)
- [`adastra2024/adastra/dashboard/php/pullReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/pullReservation.php)
- [`adastra2024/adastra/dashboard/php/pullReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/pullReservationOnline.php)
- [`adastra2024/adastra/dashboard/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/pullSchedules.php)
- [`adastra2024/adastra/dashboard/php/pullTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/pullTimeSlot.php)
- [`adastra2024/adastra/dashboard/php/pullTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/pullTimeSlotOnline.php)
- [`adastra2024/adastra/dashboard/php/saveOthers.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/saveOthers.php)
- [`adastra2024/adastra/dashboard/php/saveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/saveResched.php)
- [`adastra2024/adastra/dashboard/php/saveReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/saveReservation.php)
- [`adastra2024/adastra/dashboard/php/saveReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/saveReservationOnline.php)
- [`adastra2024/adastra/dashboard/php/saveSubscriberSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/php/saveSubscriberSchedule.php)
- [`adastra2024/adastra/dashboard/profile/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/profile/index.php)
- [`adastra2024/adastra/dashboard/testfile.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/testfile.php)
- [`adastra2024/adastra/dashboard/write-up/closed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/write-up/closed.php)
- [`adastra2024/adastra/dashboard/write-up/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/write-up/index.php)
- [`adastra2024/adastra/dashboard/yearbook-status/digital-yearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/dashboard/yearbook-status/digital-yearbook.php)
- [`adastra2024/adastra/mailBlaster.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/mailBlaster.php)
- [`adastra2024/adastra/mailBlasterRev.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/mailBlasterRev.php)
- [`adastra2024/adastra/mailFrag.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/mailFrag.php)
- [`adastra2024/adastra/mailFragMain.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/mailFragMain.php)
- [`adastra2024/adastra/mailLuxeo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/mailLuxeo.php)
- [`adastra2024/adastra/mailSecondOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/mailSecondOnline.php)
- [`adastra2024/adastra/mailUpdatedObra.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/mailUpdatedObra.php)
- [`adastra2024/adastra/mailblastertest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/mailblastertest.php)
- [`adastra2024/adastra/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/approvePayment.php)
- [`adastra2024/adastra/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/approveWriteUp.php)
- [`adastra2024/adastra/php/bankRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/bankRequest.php)
- [`adastra2024/adastra/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/changePassword.php)
- [`adastra2024/adastra/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/disapprovePayment.php)
- [`adastra2024/adastra/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/disapproveWriteUp.php)
- [`adastra2024/adastra/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/emailForgot.php)
- [`adastra2024/adastra/php/letterRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/letterRequest.php)
- [`adastra2024/adastra/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/loadWriteUpHistory.php)
- [`adastra2024/adastra/php/parentRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/parentRequest.php)
- [`adastra2024/adastra/php/processSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/processSecondPayment.php)
- [`adastra2024/adastra/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/pullBrowse.php)
- [`adastra2024/adastra/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/pullDashboard.php)
- [`adastra2024/adastra/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/pullLogs.php)
- [`adastra2024/adastra/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/pullPaymentApproval.php)
- [`adastra2024/adastra/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/pullUserList.php)
- [`adastra2024/adastra/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/pullWriteUpApproval.php)
- [`adastra2024/adastra/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/pullinfo.php)
- [`adastra2024/adastra/php/pullinfoorg.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/pullinfoorg.php)
- [`adastra2024/adastra/php/refundRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/refundRequest.php)
- [`adastra2024/adastra/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/saveUser.php)
- [`adastra2024/adastra/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/saveWriteUp.php)
- [`adastra2024/adastra/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/testAdminLogin.php)
- [`adastra2024/adastra/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/testCheckIn.php)
- [`adastra2024/adastra/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/testLogin.php)
- [`adastra2024/adastra/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/testUsername.php)
- [`adastra2024/adastra/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/testWriteUpCount.php)
- [`adastra2024/adastra/php/transferRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/transferRequest.php)
- [`adastra2024/adastra/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/updateContact.php)
- [`adastra2024/adastra/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/updateShipping.php)
- [`adastra2024/adastra/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/updatepassword.php)
- [`adastra2024/adastra/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/uploadPayment.php)
- [`adastra2024/adastra/php/uploadProof.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/php/uploadProof.php)
- [`adastra2024/adastra/recruitmentmailblast.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/recruitmentmailblast.php)
- [`adastra2024/adastra/testemail.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/testemail.php)
- [`adastra2024/adastra/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/adastra/updatepassword.php)
- [`adastra2024/check_email.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/check_email.php)
- [`adastra2024/check_emailAd.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/check_emailAd.php)
- [`adastra2024/check_id_number.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/check_id_number.php)
- [`adastra2024/dbcon.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/dbcon.php)
- [`adastra2024/emailBlastLuxeo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/emailBlastLuxeo.php)
- [`adastra2024/emailGeneral.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/emailGeneral.php)
- [`adastra2024/emailblast.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/emailblast.php)
- [`adastra2024/emailblastpictorial.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/emailblastpictorial.php)
- [`adastra2024/php/forgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/php/forgot.php)
- [`adastra2024/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/php/testLogin.php)
- [`adastra2024/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/php/updatepassword.php)
- [`adastra2024/testblast.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/testblast.php)
- [`adastra2024/updatepass.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/updatepass.php)
- [`adastra2024/updatepass2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2024/updatepass2.php)

</details>

#### Module `adastra2023` (92 files)
<details>
<summary>Click to view all 92 files in `adastra2023` calling `connect.php`</summary>

- [`adastra2023/adastra/PHPExcel-1.8/Examples/01simple-download-xlsx.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/PHPExcel-1.8/Examples/01simple-download-xlsx.php)
- [`adastra2023/adastra/dashboard/my-photos/checkComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/checkComplete.php)
- [`adastra2023/adastra/dashboard/my-photos/finalizeSelection.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/finalizeSelection.php)
- [`adastra2023/adastra/dashboard/my-photos/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/index.php)
- [`adastra2023/adastra/dashboard/my-photos/onlineSelect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/onlineSelect.php)
- [`adastra2023/adastra/dashboard/my-photos/proceed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/proceed.php)
- [`adastra2023/adastra/dashboard/my-photos/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/pullUploads.php)
- [`adastra2023/adastra/dashboard/my-photos/saveFeedback.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/saveFeedback.php)
- [`adastra2023/adastra/dashboard/my-photos/updateSelected.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/updateSelected.php)
- [`adastra2023/adastra/dashboard/my-photos/upload-photos.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/upload-photos.php)
- [`adastra2023/adastra/dashboard/my-photos/uploadPhoto.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/uploadPhoto.php)
- [`adastra2023/adastra/dashboard/my-photos/verifyComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/my-photos/verifyComplete.php)
- [`adastra2023/adastra/dashboard/php/dateChecker.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/dateChecker.php)
- [`adastra2023/adastra/dashboard/php/deleteSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/deleteSched.php)
- [`adastra2023/adastra/dashboard/php/getSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/getSched.php)
- [`adastra2023/adastra/dashboard/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/pullCalendar.php)
- [`adastra2023/adastra/dashboard/php/pullList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/pullList.php)
- [`adastra2023/adastra/dashboard/php/pullListOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/pullListOnline.php)
- [`adastra2023/adastra/dashboard/php/pullReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/pullReservation.php)
- [`adastra2023/adastra/dashboard/php/pullReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/pullReservationOnline.php)
- [`adastra2023/adastra/dashboard/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/pullSchedules.php)
- [`adastra2023/adastra/dashboard/php/pullTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/pullTimeSlot.php)
- [`adastra2023/adastra/dashboard/php/pullTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/pullTimeSlotOnline.php)
- [`adastra2023/adastra/dashboard/php/saveOthers.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/saveOthers.php)
- [`adastra2023/adastra/dashboard/php/saveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/saveResched.php)
- [`adastra2023/adastra/dashboard/php/saveReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/saveReservation.php)
- [`adastra2023/adastra/dashboard/php/saveReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/saveReservationOnline.php)
- [`adastra2023/adastra/dashboard/php/saveSubscriberSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/php/saveSubscriberSchedule.php)
- [`adastra2023/adastra/dashboard/profile/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/profile/index.php)
- [`adastra2023/adastra/dashboard/testfile.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/testfile.php)
- [`adastra2023/adastra/dashboard/yearbook-status/digital-yearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/dashboard/yearbook-status/digital-yearbook.php)
- [`adastra2023/adastra/mailBlaster.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/mailBlaster.php)
- [`adastra2023/adastra/mailBlasterRev.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/mailBlasterRev.php)
- [`adastra2023/adastra/mailFrag.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/mailFrag.php)
- [`adastra2023/adastra/mailFragMain.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/mailFragMain.php)
- [`adastra2023/adastra/mailLuxeo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/mailLuxeo.php)
- [`adastra2023/adastra/mailSecondOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/mailSecondOnline.php)
- [`adastra2023/adastra/mailUpdatedObra.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/mailUpdatedObra.php)
- [`adastra2023/adastra/mailblastertest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/mailblastertest.php)
- [`adastra2023/adastra/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/approvePayment.php)
- [`adastra2023/adastra/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/approveWriteUp.php)
- [`adastra2023/adastra/php/bankRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/bankRequest.php)
- [`adastra2023/adastra/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/changePassword.php)
- [`adastra2023/adastra/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/disapprovePayment.php)
- [`adastra2023/adastra/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/disapproveWriteUp.php)
- [`adastra2023/adastra/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/emailForgot.php)
- [`adastra2023/adastra/php/letterRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/letterRequest.php)
- [`adastra2023/adastra/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/loadWriteUpHistory.php)
- [`adastra2023/adastra/php/parentRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/parentRequest.php)
- [`adastra2023/adastra/php/processSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/processSecondPayment.php)
- [`adastra2023/adastra/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullBrowse.php)
- [`adastra2023/adastra/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullDashboard.php)
- [`adastra2023/adastra/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullLogs.php)
- [`adastra2023/adastra/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullPaymentApproval.php)
- [`adastra2023/adastra/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullUserList.php)
- [`adastra2023/adastra/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullWriteUpApproval.php)
- [`adastra2023/adastra/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullinfo.php)
- [`adastra2023/adastra/php/pullinfo_closed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullinfo_closed.php)
- [`adastra2023/adastra/php/pullinfo_openTRforms.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/pullinfo_openTRforms.php)
- [`adastra2023/adastra/php/refundRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/refundRequest.php)
- [`adastra2023/adastra/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/saveUser.php)
- [`adastra2023/adastra/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/saveWriteUp.php)
- [`adastra2023/adastra/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/testAdminLogin.php)
- [`adastra2023/adastra/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/testCheckIn.php)
- [`adastra2023/adastra/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/testLogin.php)
- [`adastra2023/adastra/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/testUsername.php)
- [`adastra2023/adastra/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/testWriteUpCount.php)
- [`adastra2023/adastra/php/transferRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/transferRequest.php)
- [`adastra2023/adastra/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/updateContact.php)
- [`adastra2023/adastra/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/updateShipping.php)
- [`adastra2023/adastra/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/updatepassword.php)
- [`adastra2023/adastra/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/php/uploadPayment.php)
- [`adastra2023/adastra/recruitmentmailblast.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/recruitmentmailblast.php)
- [`adastra2023/adastra/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/adastra/updatepassword.php)
- [`adastra2023/dbcon.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/dbcon.php)
- [`adastra2023/emailGeneral.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/emailGeneral.php)
- [`adastra2023/emailTransfer.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/emailTransfer.php)
- [`adastra2023/emailblast.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/emailblast.php)
- [`adastra2023/mailFirstBatch.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailFirstBatch.php)
- [`adastra2023/mailNoAppointment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailNoAppointment.php)
- [`adastra2023/mailNoGPP.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailNoGPP.php)
- [`adastra2023/mailNoPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailNoPayment.php)
- [`adastra2023/mailNoWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailNoWriteUp.php)
- [`adastra2023/mailObraClaim.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailObraClaim.php)
- [`adastra2023/mailPassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailPassword.php)
- [`adastra2023/mailSecondBatch.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailSecondBatch.php)
- [`adastra2023/mailTransfer.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailTransfer.php)
- [`adastra2023/mailWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/mailWriteUp.php)
- [`adastra2023/php/forgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/php/forgot.php)
- [`adastra2023/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/php/testLogin.php)
- [`adastra2023/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/php/updatepassword.php)
- [`adastra2023/updatepass.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2023/updatepass.php)

</details>

#### Module `adastra2026` (86 files)
<details>
<summary>Click to view all 86 files in `adastra2026` calling `connect.php`</summary>

- [`adastra2026/adastra/dashboard/backup.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/backup.php)
- [`adastra2026/adastra/dashboard/my-photos/checkComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/checkComplete.php)
- [`adastra2026/adastra/dashboard/my-photos/finalizeSelection.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/finalizeSelection.php)
- [`adastra2026/adastra/dashboard/my-photos/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/index.php)
- [`adastra2026/adastra/dashboard/my-photos/onlineSelect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/onlineSelect.php)
- [`adastra2026/adastra/dashboard/my-photos/proceed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/proceed.php)
- [`adastra2026/adastra/dashboard/my-photos/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/pullUploads.php)
- [`adastra2026/adastra/dashboard/my-photos/saveFeedback.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/saveFeedback.php)
- [`adastra2026/adastra/dashboard/my-photos/setup-2026.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/setup-2026.php)
- [`adastra2026/adastra/dashboard/my-photos/test-photo-load.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/test-photo-load.php)
- [`adastra2026/adastra/dashboard/my-photos/updateSelected.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/updateSelected.php)
- [`adastra2026/adastra/dashboard/my-photos/upload-photos-og.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/upload-photos-og.php)
- [`adastra2026/adastra/dashboard/my-photos/uploadPhoto.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/uploadPhoto.php)
- [`adastra2026/adastra/dashboard/my-photos/verifyComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/my-photos/verifyComplete.php)
- [`adastra2026/adastra/dashboard/php/dateChecker.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/dateChecker.php)
- [`adastra2026/adastra/dashboard/php/deleteSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/deleteSched.php)
- [`adastra2026/adastra/dashboard/php/deleteSchedOrig.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/deleteSchedOrig.php)
- [`adastra2026/adastra/dashboard/php/getSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/getSched.php)
- [`adastra2026/adastra/dashboard/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullCalendar.php)
- [`adastra2026/adastra/dashboard/php/pullList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullList.php)
- [`adastra2026/adastra/dashboard/php/pullListOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullListOnline.php)
- [`adastra2026/adastra/dashboard/php/pullReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullReservation.php)
- [`adastra2026/adastra/dashboard/php/pullReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullReservationOnline.php)
- [`adastra2026/adastra/dashboard/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullSchedules.php)
- [`adastra2026/adastra/dashboard/php/pullTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullTimeSlot.php)
- [`adastra2026/adastra/dashboard/php/pullTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullTimeSlotOnline.php)
- [`adastra2026/adastra/dashboard/php/pullTimeSlot_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/pullTimeSlot_v1.php)
- [`adastra2026/adastra/dashboard/php/saveOthers.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/saveOthers.php)
- [`adastra2026/adastra/dashboard/php/saveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/saveResched.php)
- [`adastra2026/adastra/dashboard/php/saveReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/saveReservation.php)
- [`adastra2026/adastra/dashboard/php/saveReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/saveReservationOnline.php)
- [`adastra2026/adastra/dashboard/php/saveSubscriberSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/php/saveSubscriberSchedule.php)
- [`adastra2026/adastra/dashboard/profile/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/profile/index.php)
- [`adastra2026/adastra/dashboard/testfile.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/testfile.php)
- [`adastra2026/adastra/dashboard/write-up/closed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/write-up/closed.php)
- [`adastra2026/adastra/dashboard/write-up/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/write-up/index.php)
- [`adastra2026/adastra/dashboard/write-up/indexV2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/write-up/indexV2.php)
- [`adastra2026/adastra/dashboard/yearbook-status/digital-yearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/dashboard/yearbook-status/digital-yearbook.php)
- [`adastra2026/adastra/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/approvePayment.php)
- [`adastra2026/adastra/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/approveWriteUp.php)
- [`adastra2026/adastra/php/bankRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/bankRequest.php)
- [`adastra2026/adastra/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/changePassword.php)
- [`adastra2026/adastra/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/disapprovePayment.php)
- [`adastra2026/adastra/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/disapproveWriteUp.php)
- [`adastra2026/adastra/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/emailForgot.php)
- [`adastra2026/adastra/php/get_signature.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/get_signature.php)
- [`adastra2026/adastra/php/letterRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/letterRequest.php)
- [`adastra2026/adastra/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/loadWriteUpHistory.php)
- [`adastra2026/adastra/php/parentRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/parentRequest.php)
- [`adastra2026/adastra/php/processSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/processSecondPayment.php)
- [`adastra2026/adastra/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullBrowse.php)
- [`adastra2026/adastra/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullDashboard.php)
- [`adastra2026/adastra/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullLogs.php)
- [`adastra2026/adastra/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullPaymentApproval.php)
- [`adastra2026/adastra/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullUserList.php)
- [`adastra2026/adastra/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullWriteUpApproval.php)
- [`adastra2026/adastra/php/pullinfo_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullinfo_v2.php)
- [`adastra2026/adastra/php/pullinfo_v2_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullinfo_v2_TEST.php)
- [`adastra2026/adastra/php/pullinfo_v3.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/pullinfo_v3.php)
- [`adastra2026/adastra/php/refundRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/refundRequest.php)
- [`adastra2026/adastra/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/saveUser.php)
- [`adastra2026/adastra/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/saveWriteUp.php)
- [`adastra2026/adastra/php/submitConcern.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/submitConcern.php)
- [`adastra2026/adastra/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/testAdminLogin.php)
- [`adastra2026/adastra/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/testCheckIn.php)
- [`adastra2026/adastra/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/testLogin.php)
- [`adastra2026/adastra/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/testUsername.php)
- [`adastra2026/adastra/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/testWriteUpCount.php)
- [`adastra2026/adastra/php/transferRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/transferRequest.php)
- [`adastra2026/adastra/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/updateContact.php)
- [`adastra2026/adastra/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/updateShipping.php)
- [`adastra2026/adastra/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/updatepassword.php)
- [`adastra2026/adastra/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/uploadPayment.php)
- [`adastra2026/adastra/php/uploadProof.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/php/uploadProof.php)
- [`adastra2026/adastra/pullinfo_v2_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/adastra/pullinfo_v2_TEST.php)
- [`adastra2026/check_email.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/check_email.php)
- [`adastra2026/check_emailAd.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/check_emailAd.php)
- [`adastra2026/check_id_number.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/check_id_number.php)
- [`adastra2026/dbcon.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/dbcon.php)
- [`adastra2026/php/esig.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/esig.php)
- [`adastra2026/php/forgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/forgot.php)
- [`adastra2026/php/pullinfo_v2_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/pullinfo_v2_TEST.php)
- [`adastra2026/php/sendsubinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/sendsubinfo.php)
- [`adastra2026/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/testLogin.php)
- [`adastra2026/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/php/updatepassword.php)
- [`adastra2026/updatepass.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2026/updatepass.php)

</details>

#### Module `adastra2027` (85 files)
<details>
<summary>Click to view all 85 files in `adastra2027` calling `connect.php`</summary>

- [`adastra2027/adastra/dashboard/backup.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/backup.php)
- [`adastra2027/adastra/dashboard/my-photos/checkComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/checkComplete.php)
- [`adastra2027/adastra/dashboard/my-photos/finalizeSelection.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/finalizeSelection.php)
- [`adastra2027/adastra/dashboard/my-photos/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/index.php)
- [`adastra2027/adastra/dashboard/my-photos/onlineSelect.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/onlineSelect.php)
- [`adastra2027/adastra/dashboard/my-photos/proceed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/proceed.php)
- [`adastra2027/adastra/dashboard/my-photos/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/pullUploads.php)
- [`adastra2027/adastra/dashboard/my-photos/saveFeedback.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/saveFeedback.php)
- [`adastra2027/adastra/dashboard/my-photos/setup-2026.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/setup-2026.php)
- [`adastra2027/adastra/dashboard/my-photos/test-photo-load.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/test-photo-load.php)
- [`adastra2027/adastra/dashboard/my-photos/updateSelected.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/updateSelected.php)
- [`adastra2027/adastra/dashboard/my-photos/upload-photos-og.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/upload-photos-og.php)
- [`adastra2027/adastra/dashboard/my-photos/uploadPhoto.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/uploadPhoto.php)
- [`adastra2027/adastra/dashboard/my-photos/verifyComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/my-photos/verifyComplete.php)
- [`adastra2027/adastra/dashboard/php/dateChecker.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/dateChecker.php)
- [`adastra2027/adastra/dashboard/php/deleteSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/deleteSched.php)
- [`adastra2027/adastra/dashboard/php/deleteSchedOrig.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/deleteSchedOrig.php)
- [`adastra2027/adastra/dashboard/php/getSched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/getSched.php)
- [`adastra2027/adastra/dashboard/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullCalendar.php)
- [`adastra2027/adastra/dashboard/php/pullList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullList.php)
- [`adastra2027/adastra/dashboard/php/pullListOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullListOnline.php)
- [`adastra2027/adastra/dashboard/php/pullReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullReservation.php)
- [`adastra2027/adastra/dashboard/php/pullReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullReservationOnline.php)
- [`adastra2027/adastra/dashboard/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullSchedules.php)
- [`adastra2027/adastra/dashboard/php/pullTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullTimeSlot.php)
- [`adastra2027/adastra/dashboard/php/pullTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullTimeSlotOnline.php)
- [`adastra2027/adastra/dashboard/php/pullTimeSlot_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/pullTimeSlot_v1.php)
- [`adastra2027/adastra/dashboard/php/saveOthers.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/saveOthers.php)
- [`adastra2027/adastra/dashboard/php/saveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/saveResched.php)
- [`adastra2027/adastra/dashboard/php/saveReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/saveReservation.php)
- [`adastra2027/adastra/dashboard/php/saveReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/saveReservationOnline.php)
- [`adastra2027/adastra/dashboard/php/saveSubscriberSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/php/saveSubscriberSchedule.php)
- [`adastra2027/adastra/dashboard/profile/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/profile/index.php)
- [`adastra2027/adastra/dashboard/testfile.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/testfile.php)
- [`adastra2027/adastra/dashboard/write-up/closed.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/write-up/closed.php)
- [`adastra2027/adastra/dashboard/write-up/index.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/write-up/index.php)
- [`adastra2027/adastra/dashboard/write-up/indexV2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/write-up/indexV2.php)
- [`adastra2027/adastra/dashboard/yearbook-status/digital-yearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/dashboard/yearbook-status/digital-yearbook.php)
- [`adastra2027/adastra/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/approvePayment.php)
- [`adastra2027/adastra/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/approveWriteUp.php)
- [`adastra2027/adastra/php/bankRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/bankRequest.php)
- [`adastra2027/adastra/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/changePassword.php)
- [`adastra2027/adastra/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/disapprovePayment.php)
- [`adastra2027/adastra/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/disapproveWriteUp.php)
- [`adastra2027/adastra/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/emailForgot.php)
- [`adastra2027/adastra/php/get_signature.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/get_signature.php)
- [`adastra2027/adastra/php/letterRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/letterRequest.php)
- [`adastra2027/adastra/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/loadWriteUpHistory.php)
- [`adastra2027/adastra/php/parentRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/parentRequest.php)
- [`adastra2027/adastra/php/processSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/processSecondPayment.php)
- [`adastra2027/adastra/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullBrowse.php)
- [`adastra2027/adastra/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullDashboard.php)
- [`adastra2027/adastra/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullLogs.php)
- [`adastra2027/adastra/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullPaymentApproval.php)
- [`adastra2027/adastra/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullUserList.php)
- [`adastra2027/adastra/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullWriteUpApproval.php)
- [`adastra2027/adastra/php/pullinfo_v2.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullinfo_v2.php)
- [`adastra2027/adastra/php/pullinfo_v2_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullinfo_v2_TEST.php)
- [`adastra2027/adastra/php/pullinfo_v3.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/pullinfo_v3.php)
- [`adastra2027/adastra/php/refundRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/refundRequest.php)
- [`adastra2027/adastra/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/saveUser.php)
- [`adastra2027/adastra/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/saveWriteUp.php)
- [`adastra2027/adastra/php/submitConcern.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/submitConcern.php)
- [`adastra2027/adastra/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/testAdminLogin.php)
- [`adastra2027/adastra/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/testCheckIn.php)
- [`adastra2027/adastra/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/testLogin.php)
- [`adastra2027/adastra/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/testUsername.php)
- [`adastra2027/adastra/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/testWriteUpCount.php)
- [`adastra2027/adastra/php/transferRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/transferRequest.php)
- [`adastra2027/adastra/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/updateContact.php)
- [`adastra2027/adastra/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/updateShipping.php)
- [`adastra2027/adastra/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/updatepassword.php)
- [`adastra2027/adastra/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/uploadPayment.php)
- [`adastra2027/adastra/php/uploadProof.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/php/uploadProof.php)
- [`adastra2027/adastra/pullinfo_v2_TEST.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/adastra/pullinfo_v2_TEST.php)
- [`adastra2027/check_email.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/check_email.php)
- [`adastra2027/check_emailAd.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/check_emailAd.php)
- [`adastra2027/check_id_number.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/check_id_number.php)
- [`adastra2027/dbcon.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/dbcon.php)
- [`adastra2027/php/esig.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/esig.php)
- [`adastra2027/php/forgot.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/forgot.php)
- [`adastra2027/php/sendsubinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/sendsubinfo.php)
- [`adastra2027/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/testLogin.php)
- [`adastra2027/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/php/updatepassword.php)
- [`adastra2027/updatepass.php`](file:///Users/lei/Downloads/adastra/manualtest/adastra2027/updatepass.php)

</details>

#### Module `gpp_claiming` (102 files)
<details>
<summary>Click to view all 102 files in `gpp_claiming` calling `connect.php`</summary>

- [`gpp_claiming/gpp_php/checkExistingAppointment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/checkExistingAppointment.php)
- [`gpp_claiming/gpp_php/dateChecker.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/dateChecker.php)
- [`gpp_claiming/gpp_php/deleteSched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/deleteSched.php)
- [`gpp_claiming/gpp_php/deleteSchedOrig.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/deleteSchedOrig.php)
- [`gpp_claiming/gpp_php/getSched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/getSched.php)
- [`gpp_claiming/gpp_php/old-pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/old-pullCalendar.php)
- [`gpp_claiming/gpp_php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullCalendar.php)
- [`gpp_claiming/gpp_php/pullList.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullList.php)
- [`gpp_claiming/gpp_php/pullListOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullListOnline.php)
- [`gpp_claiming/gpp_php/pullReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullReservation.php)
- [`gpp_claiming/gpp_php/pullReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullReservationOnline.php)
- [`gpp_claiming/gpp_php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullSchedules.php)
- [`gpp_claiming/gpp_php/pullTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullTimeSlot.php)
- [`gpp_claiming/gpp_php/pullTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullTimeSlotOnline.php)
- [`gpp_claiming/gpp_php/pullTimeSlot_v1.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/pullTimeSlot_v1.php)
- [`gpp_claiming/gpp_php/saveOthers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/saveOthers.php)
- [`gpp_claiming/gpp_php/saveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/saveResched.php)
- [`gpp_claiming/gpp_php/saveReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/saveReservation.php)
- [`gpp_claiming/gpp_php/saveReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/saveReservationOnline.php)
- [`gpp_claiming/gpp_php/saveSubscriberSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/gpp_php/saveSubscriberSchedule.php)
- [`gpp_claiming/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/approvePayment.php)
- [`gpp_claiming/php/approveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/approveResched.php)
- [`gpp_claiming/php/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/approveReviseWriteUp.php)
- [`gpp_claiming/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/approveWriteUp.php)
- [`gpp_claiming/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/changePassword.php)
- [`gpp_claiming/php/completeRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/completeRequest.php)
- [`gpp_claiming/php/disableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/disableAccount.php)
- [`gpp_claiming/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/disapprovePayment.php)
- [`gpp_claiming/php/disapproveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/disapproveResched.php)
- [`gpp_claiming/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/disapproveWriteUp.php)
- [`gpp_claiming/php/editUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/editUserPermissions.php)
- [`gpp_claiming/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/emailForgot.php)
- [`gpp_claiming/php/enableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/enableAccount.php)
- [`gpp_claiming/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/loadWriteUpHistory.php)
- [`gpp_claiming/php/pull2023.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pull2023.php)
- [`gpp_claiming/php/pull2023Payments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pull2023Payments.php)
- [`gpp_claiming/php/pullApplicants.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullApplicants.php)
- [`gpp_claiming/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullBrowse.php)
- [`gpp_claiming/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullCalendar.php)
- [`gpp_claiming/php/pullCheckin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullCheckin.php)
- [`gpp_claiming/php/pullClassicPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullClassicPayments.php)
- [`gpp_claiming/php/pullClassicSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullClassicSubscribers.php)
- [`gpp_claiming/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullDashboard.php)
- [`gpp_claiming/php/pullDimensionsSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullDimensionsSubscribers.php)
- [`gpp_claiming/php/pullFeedbacks.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullFeedbacks.php)
- [`gpp_claiming/php/pullFragmentSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullFragmentSubscribers.php)
- [`gpp_claiming/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullLogs.php)
- [`gpp_claiming/php/pullLuxeoPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullLuxeoPayments.php)
- [`gpp_claiming/php/pullLuxeoSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullLuxeoSubscribers.php)
- [`gpp_claiming/php/pullManageSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullManageSchedules.php)
- [`gpp_claiming/php/pullManageSchedulesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullManageSchedulesOnline.php)
- [`gpp_claiming/php/pullManageTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullManageTimeSlot.php)
- [`gpp_claiming/php/pullManageTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullManageTimeSlotOnline.php)
- [`gpp_claiming/php/pullObraPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullObraPayments.php)
- [`gpp_claiming/php/pullObraSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullObraSubscribers.php)
- [`gpp_claiming/php/pullOnlineUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullOnlineUploads.php)
- [`gpp_claiming/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullPaymentApproval.php)
- [`gpp_claiming/php/pullPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullPayments.php)
- [`gpp_claiming/php/pullProjectionSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullProjectionSubscribers.php)
- [`gpp_claiming/php/pullRecruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullRecruitment.php)
- [`gpp_claiming/php/pullReschedApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullReschedApproval.php)
- [`gpp_claiming/php/pullScheduleAppointments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullScheduleAppointments.php)
- [`gpp_claiming/php/pullScheduleAppointmentsOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullScheduleAppointmentsOnline.php)
- [`gpp_claiming/php/pullScheduleDates.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullScheduleDates.php)
- [`gpp_claiming/php/pullScheduleDatesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullScheduleDatesOnline.php)
- [`gpp_claiming/php/pullScheduleTimes.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullScheduleTimes.php)
- [`gpp_claiming/php/pullScheduleTimesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullScheduleTimesOnline.php)
- [`gpp_claiming/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullSchedules.php)
- [`gpp_claiming/php/pullSchools.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullSchools.php)
- [`gpp_claiming/php/pullSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullSecondPayment.php)
- [`gpp_claiming/php/pullSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullSubscribers.php)
- [`gpp_claiming/php/pullTickets.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullTickets.php)
- [`gpp_claiming/php/pullTransfers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullTransfers.php)
- [`gpp_claiming/php/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullUploads.php)
- [`gpp_claiming/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullUserList.php)
- [`gpp_claiming/php/pullUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullUserPermissions.php)
- [`gpp_claiming/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullWriteUpApproval.php)
- [`gpp_claiming/php/pullWriteUps.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullWriteUps.php)
- [`gpp_claiming/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/pullinfo.php)
- [`gpp_claiming/php/resetStudentPass.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/resetStudentPass.php)
- [`gpp_claiming/php/saveManageAddSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/saveManageAddSchedule.php)
- [`gpp_claiming/php/saveManageAddScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/saveManageAddScheduleOnline.php)
- [`gpp_claiming/php/saveSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/saveSchedule.php)
- [`gpp_claiming/php/saveScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/saveScheduleOnline.php)
- [`gpp_claiming/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/saveUser.php)
- [`gpp_claiming/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/saveWriteUp.php)
- [`gpp_claiming/php/setScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/setScheduleActive.php)
- [`gpp_claiming/php/setScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/setScheduleActiveOnline.php)
- [`gpp_claiming/php/setScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/setScheduleInActive.php)
- [`gpp_claiming/php/setScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/setScheduleInActiveOnline.php)
- [`gpp_claiming/php/setSingleScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/setSingleScheduleActive.php)
- [`gpp_claiming/php/setSingleScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/setSingleScheduleActiveOnline.php)
- [`gpp_claiming/php/setSingleScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/setSingleScheduleInActive.php)
- [`gpp_claiming/php/setSingleScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/setSingleScheduleInActiveOnline.php)
- [`gpp_claiming/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/testAdminLogin.php)
- [`gpp_claiming/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/testLogin.php)
- [`gpp_claiming/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/testUsername.php)
- [`gpp_claiming/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/testWriteUpCount.php)
- [`gpp_claiming/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/updateContact.php)
- [`gpp_claiming/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/updateShipping.php)
- [`gpp_claiming/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/updatepassword.php)
- [`gpp_claiming/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_claiming/php/uploadPayment.php)

</details>

#### Module `fragment202122` (73 files)
<details>
<summary>Click to view all 73 files in `fragment202122` calling `connect.php`</summary>

- [`fragment202122/adastra/PHPExcel-1.8/Examples/01simple-download-xlsx.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/PHPExcel-1.8/Examples/01simple-download-xlsx.php)
- [`fragment202122/adastra/dashboard/my-photos/checkComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/checkComplete.php)
- [`fragment202122/adastra/dashboard/my-photos/finalizeSelection.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/finalizeSelection.php)
- [`fragment202122/adastra/dashboard/my-photos/index.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/index.php)
- [`fragment202122/adastra/dashboard/my-photos/onlineSelect.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/onlineSelect.php)
- [`fragment202122/adastra/dashboard/my-photos/proceed.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/proceed.php)
- [`fragment202122/adastra/dashboard/my-photos/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/pullUploads.php)
- [`fragment202122/adastra/dashboard/my-photos/saveFeedback.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/saveFeedback.php)
- [`fragment202122/adastra/dashboard/my-photos/updateSelected.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/updateSelected.php)
- [`fragment202122/adastra/dashboard/my-photos/upload-photos.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/upload-photos.php)
- [`fragment202122/adastra/dashboard/my-photos/uploadPhoto.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/uploadPhoto.php)
- [`fragment202122/adastra/dashboard/my-photos/verifyComplete.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/my-photos/verifyComplete.php)
- [`fragment202122/adastra/dashboard/php/deleteSched.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/deleteSched.php)
- [`fragment202122/adastra/dashboard/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/pullCalendar.php)
- [`fragment202122/adastra/dashboard/php/pullList.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/pullList.php)
- [`fragment202122/adastra/dashboard/php/pullListOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/pullListOnline.php)
- [`fragment202122/adastra/dashboard/php/pullReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/pullReservation.php)
- [`fragment202122/adastra/dashboard/php/pullReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/pullReservationOnline.php)
- [`fragment202122/adastra/dashboard/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/pullSchedules.php)
- [`fragment202122/adastra/dashboard/php/pullTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/pullTimeSlot.php)
- [`fragment202122/adastra/dashboard/php/pullTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/pullTimeSlotOnline.php)
- [`fragment202122/adastra/dashboard/php/saveOthers.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/saveOthers.php)
- [`fragment202122/adastra/dashboard/php/saveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/saveResched.php)
- [`fragment202122/adastra/dashboard/php/saveReservation.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/saveReservation.php)
- [`fragment202122/adastra/dashboard/php/saveReservationOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/saveReservationOnline.php)
- [`fragment202122/adastra/dashboard/php/saveSubscriberSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/php/saveSubscriberSchedule.php)
- [`fragment202122/adastra/dashboard/profile/index.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/profile/index.php)
- [`fragment202122/adastra/dashboard/testfile.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/testfile.php)
- [`fragment202122/adastra/dashboard/yearbook-status/digital-yearbook.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/dashboard/yearbook-status/digital-yearbook.php)
- [`fragment202122/adastra/mailBlaster.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailBlaster.php)
- [`fragment202122/adastra/mailBlasterRev.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailBlasterRev.php)
- [`fragment202122/adastra/mailClassic.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailClassic.php)
- [`fragment202122/adastra/mailFrag.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailFrag.php)
- [`fragment202122/adastra/mailFragMain.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailFragMain.php)
- [`fragment202122/adastra/mailGPP.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailGPP.php)
- [`fragment202122/adastra/mailLuxeo.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailLuxeo.php)
- [`fragment202122/adastra/mailSecondOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailSecondOnline.php)
- [`fragment202122/adastra/mailSubscription.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailSubscription.php)
- [`fragment202122/adastra/mailTransfer.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailTransfer.php)
- [`fragment202122/adastra/mailTransferLuxeo.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailTransferLuxeo.php)
- [`fragment202122/adastra/mailUpdatedObra.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailUpdatedObra.php)
- [`fragment202122/adastra/mailblastertest.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/mailblastertest.php)
- [`fragment202122/adastra/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/approvePayment.php)
- [`fragment202122/adastra/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/approveWriteUp.php)
- [`fragment202122/adastra/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/changePassword.php)
- [`fragment202122/adastra/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/disapprovePayment.php)
- [`fragment202122/adastra/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/disapproveWriteUp.php)
- [`fragment202122/adastra/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/emailForgot.php)
- [`fragment202122/adastra/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/loadWriteUpHistory.php)
- [`fragment202122/adastra/php/processSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/processSecondPayment.php)
- [`fragment202122/adastra/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/pullBrowse.php)
- [`fragment202122/adastra/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/pullDashboard.php)
- [`fragment202122/adastra/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/pullLogs.php)
- [`fragment202122/adastra/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/pullPaymentApproval.php)
- [`fragment202122/adastra/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/pullUserList.php)
- [`fragment202122/adastra/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/pullWriteUpApproval.php)
- [`fragment202122/adastra/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/pullinfo.php)
- [`fragment202122/adastra/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/saveUser.php)
- [`fragment202122/adastra/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/saveWriteUp.php)
- [`fragment202122/adastra/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/testAdminLogin.php)
- [`fragment202122/adastra/php/testCheckIn.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/testCheckIn.php)
- [`fragment202122/adastra/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/testLogin.php)
- [`fragment202122/adastra/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/testUsername.php)
- [`fragment202122/adastra/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/testWriteUpCount.php)
- [`fragment202122/adastra/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/updateContact.php)
- [`fragment202122/adastra/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/updateShipping.php)
- [`fragment202122/adastra/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/updatepassword.php)
- [`fragment202122/adastra/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/php/uploadPayment.php)
- [`fragment202122/adastra/recruitmentmailblast.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/recruitmentmailblast.php)
- [`fragment202122/adastra/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/adastra/updatepassword.php)
- [`fragment202122/saveRegistration.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/saveRegistration.php)
- [`fragment202122/saveTestRegistration.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/saveTestRegistration.php)
- [`fragment202122/testStudentNumber.php`](file:///Users/lei/Downloads/adastra/manualtest/fragment202122/testStudentNumber.php)

</details>

#### Module `gpp_2023` (82 files)
<details>
<summary>Click to view all 82 files in `gpp_2023` calling `connect.php`</summary>

- [`gpp_2023/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/approvePayment.php)
- [`gpp_2023/php/approveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/approveResched.php)
- [`gpp_2023/php/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/approveReviseWriteUp.php)
- [`gpp_2023/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/approveWriteUp.php)
- [`gpp_2023/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/changePassword.php)
- [`gpp_2023/php/completeRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/completeRequest.php)
- [`gpp_2023/php/disableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/disableAccount.php)
- [`gpp_2023/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/disapprovePayment.php)
- [`gpp_2023/php/disapproveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/disapproveResched.php)
- [`gpp_2023/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/disapproveWriteUp.php)
- [`gpp_2023/php/editUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/editUserPermissions.php)
- [`gpp_2023/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/emailForgot.php)
- [`gpp_2023/php/enableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/enableAccount.php)
- [`gpp_2023/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/loadWriteUpHistory.php)
- [`gpp_2023/php/pull2023.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pull2023.php)
- [`gpp_2023/php/pull2023Payments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pull2023Payments.php)
- [`gpp_2023/php/pullApplicants.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullApplicants.php)
- [`gpp_2023/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullBrowse.php)
- [`gpp_2023/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullCalendar.php)
- [`gpp_2023/php/pullCheckin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullCheckin.php)
- [`gpp_2023/php/pullClassicPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullClassicPayments.php)
- [`gpp_2023/php/pullClassicSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullClassicSubscribers.php)
- [`gpp_2023/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullDashboard.php)
- [`gpp_2023/php/pullDimensionsSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullDimensionsSubscribers.php)
- [`gpp_2023/php/pullFeedbacks.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullFeedbacks.php)
- [`gpp_2023/php/pullFragmentSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullFragmentSubscribers.php)
- [`gpp_2023/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullLogs.php)
- [`gpp_2023/php/pullLuxeoPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullLuxeoPayments.php)
- [`gpp_2023/php/pullLuxeoSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullLuxeoSubscribers.php)
- [`gpp_2023/php/pullManageSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullManageSchedules.php)
- [`gpp_2023/php/pullManageSchedulesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullManageSchedulesOnline.php)
- [`gpp_2023/php/pullManageTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullManageTimeSlot.php)
- [`gpp_2023/php/pullManageTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullManageTimeSlotOnline.php)
- [`gpp_2023/php/pullObraPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullObraPayments.php)
- [`gpp_2023/php/pullObraSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullObraSubscribers.php)
- [`gpp_2023/php/pullOnlineUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullOnlineUploads.php)
- [`gpp_2023/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullPaymentApproval.php)
- [`gpp_2023/php/pullPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullPayments.php)
- [`gpp_2023/php/pullProjectionSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullProjectionSubscribers.php)
- [`gpp_2023/php/pullRecruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullRecruitment.php)
- [`gpp_2023/php/pullReschedApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullReschedApproval.php)
- [`gpp_2023/php/pullScheduleAppointments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullScheduleAppointments.php)
- [`gpp_2023/php/pullScheduleAppointmentsOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullScheduleAppointmentsOnline.php)
- [`gpp_2023/php/pullScheduleDates.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullScheduleDates.php)
- [`gpp_2023/php/pullScheduleDatesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullScheduleDatesOnline.php)
- [`gpp_2023/php/pullScheduleTimes.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullScheduleTimes.php)
- [`gpp_2023/php/pullScheduleTimesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullScheduleTimesOnline.php)
- [`gpp_2023/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullSchedules.php)
- [`gpp_2023/php/pullSchools.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullSchools.php)
- [`gpp_2023/php/pullSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullSecondPayment.php)
- [`gpp_2023/php/pullSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullSubscribers.php)
- [`gpp_2023/php/pullTickets.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullTickets.php)
- [`gpp_2023/php/pullTransfers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullTransfers.php)
- [`gpp_2023/php/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullUploads.php)
- [`gpp_2023/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullUserList.php)
- [`gpp_2023/php/pullUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullUserPermissions.php)
- [`gpp_2023/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullWriteUpApproval.php)
- [`gpp_2023/php/pullWriteUps.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullWriteUps.php)
- [`gpp_2023/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/pullinfo.php)
- [`gpp_2023/php/resetStudentPass.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/resetStudentPass.php)
- [`gpp_2023/php/saveManageAddSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/saveManageAddSchedule.php)
- [`gpp_2023/php/saveManageAddScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/saveManageAddScheduleOnline.php)
- [`gpp_2023/php/saveSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/saveSchedule.php)
- [`gpp_2023/php/saveScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/saveScheduleOnline.php)
- [`gpp_2023/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/saveUser.php)
- [`gpp_2023/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/saveWriteUp.php)
- [`gpp_2023/php/setScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/setScheduleActive.php)
- [`gpp_2023/php/setScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/setScheduleActiveOnline.php)
- [`gpp_2023/php/setScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/setScheduleInActive.php)
- [`gpp_2023/php/setScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/setScheduleInActiveOnline.php)
- [`gpp_2023/php/setSingleScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/setSingleScheduleActive.php)
- [`gpp_2023/php/setSingleScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/setSingleScheduleActiveOnline.php)
- [`gpp_2023/php/setSingleScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/setSingleScheduleInActive.php)
- [`gpp_2023/php/setSingleScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/setSingleScheduleInActiveOnline.php)
- [`gpp_2023/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/testAdminLogin.php)
- [`gpp_2023/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/testLogin.php)
- [`gpp_2023/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/testUsername.php)
- [`gpp_2023/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/testWriteUpCount.php)
- [`gpp_2023/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/updateContact.php)
- [`gpp_2023/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/updateShipping.php)
- [`gpp_2023/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/updatepassword.php)
- [`gpp_2023/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2023/php/uploadPayment.php)

</details>

#### Module `gpp_2024` (82 files)
<details>
<summary>Click to view all 82 files in `gpp_2024` calling `connect.php`</summary>

- [`gpp_2024/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/approvePayment.php)
- [`gpp_2024/php/approveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/approveResched.php)
- [`gpp_2024/php/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/approveReviseWriteUp.php)
- [`gpp_2024/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/approveWriteUp.php)
- [`gpp_2024/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/changePassword.php)
- [`gpp_2024/php/completeRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/completeRequest.php)
- [`gpp_2024/php/disableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/disableAccount.php)
- [`gpp_2024/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/disapprovePayment.php)
- [`gpp_2024/php/disapproveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/disapproveResched.php)
- [`gpp_2024/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/disapproveWriteUp.php)
- [`gpp_2024/php/editUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/editUserPermissions.php)
- [`gpp_2024/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/emailForgot.php)
- [`gpp_2024/php/enableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/enableAccount.php)
- [`gpp_2024/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/loadWriteUpHistory.php)
- [`gpp_2024/php/pull2023.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pull2023.php)
- [`gpp_2024/php/pull2023Payments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pull2023Payments.php)
- [`gpp_2024/php/pullApplicants.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullApplicants.php)
- [`gpp_2024/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullBrowse.php)
- [`gpp_2024/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullCalendar.php)
- [`gpp_2024/php/pullCheckin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullCheckin.php)
- [`gpp_2024/php/pullClassicPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullClassicPayments.php)
- [`gpp_2024/php/pullClassicSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullClassicSubscribers.php)
- [`gpp_2024/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullDashboard.php)
- [`gpp_2024/php/pullDimensionsSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullDimensionsSubscribers.php)
- [`gpp_2024/php/pullFeedbacks.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullFeedbacks.php)
- [`gpp_2024/php/pullFragmentSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullFragmentSubscribers.php)
- [`gpp_2024/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullLogs.php)
- [`gpp_2024/php/pullLuxeoPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullLuxeoPayments.php)
- [`gpp_2024/php/pullLuxeoSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullLuxeoSubscribers.php)
- [`gpp_2024/php/pullManageSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullManageSchedules.php)
- [`gpp_2024/php/pullManageSchedulesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullManageSchedulesOnline.php)
- [`gpp_2024/php/pullManageTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullManageTimeSlot.php)
- [`gpp_2024/php/pullManageTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullManageTimeSlotOnline.php)
- [`gpp_2024/php/pullObraPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullObraPayments.php)
- [`gpp_2024/php/pullObraSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullObraSubscribers.php)
- [`gpp_2024/php/pullOnlineUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullOnlineUploads.php)
- [`gpp_2024/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullPaymentApproval.php)
- [`gpp_2024/php/pullPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullPayments.php)
- [`gpp_2024/php/pullProjectionSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullProjectionSubscribers.php)
- [`gpp_2024/php/pullRecruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullRecruitment.php)
- [`gpp_2024/php/pullReschedApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullReschedApproval.php)
- [`gpp_2024/php/pullScheduleAppointments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullScheduleAppointments.php)
- [`gpp_2024/php/pullScheduleAppointmentsOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullScheduleAppointmentsOnline.php)
- [`gpp_2024/php/pullScheduleDates.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullScheduleDates.php)
- [`gpp_2024/php/pullScheduleDatesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullScheduleDatesOnline.php)
- [`gpp_2024/php/pullScheduleTimes.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullScheduleTimes.php)
- [`gpp_2024/php/pullScheduleTimesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullScheduleTimesOnline.php)
- [`gpp_2024/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullSchedules.php)
- [`gpp_2024/php/pullSchools.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullSchools.php)
- [`gpp_2024/php/pullSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullSecondPayment.php)
- [`gpp_2024/php/pullSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullSubscribers.php)
- [`gpp_2024/php/pullTickets.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullTickets.php)
- [`gpp_2024/php/pullTransfers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullTransfers.php)
- [`gpp_2024/php/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullUploads.php)
- [`gpp_2024/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullUserList.php)
- [`gpp_2024/php/pullUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullUserPermissions.php)
- [`gpp_2024/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullWriteUpApproval.php)
- [`gpp_2024/php/pullWriteUps.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullWriteUps.php)
- [`gpp_2024/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/pullinfo.php)
- [`gpp_2024/php/resetStudentPass.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/resetStudentPass.php)
- [`gpp_2024/php/saveManageAddSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/saveManageAddSchedule.php)
- [`gpp_2024/php/saveManageAddScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/saveManageAddScheduleOnline.php)
- [`gpp_2024/php/saveSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/saveSchedule.php)
- [`gpp_2024/php/saveScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/saveScheduleOnline.php)
- [`gpp_2024/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/saveUser.php)
- [`gpp_2024/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/saveWriteUp.php)
- [`gpp_2024/php/setScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/setScheduleActive.php)
- [`gpp_2024/php/setScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/setScheduleActiveOnline.php)
- [`gpp_2024/php/setScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/setScheduleInActive.php)
- [`gpp_2024/php/setScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/setScheduleInActiveOnline.php)
- [`gpp_2024/php/setSingleScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/setSingleScheduleActive.php)
- [`gpp_2024/php/setSingleScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/setSingleScheduleActiveOnline.php)
- [`gpp_2024/php/setSingleScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/setSingleScheduleInActive.php)
- [`gpp_2024/php/setSingleScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/setSingleScheduleInActiveOnline.php)
- [`gpp_2024/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/testAdminLogin.php)
- [`gpp_2024/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/testLogin.php)
- [`gpp_2024/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/testUsername.php)
- [`gpp_2024/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/testWriteUpCount.php)
- [`gpp_2024/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/updateContact.php)
- [`gpp_2024/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/updateShipping.php)
- [`gpp_2024/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/updatepassword.php)
- [`gpp_2024/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2024/php/uploadPayment.php)

</details>

#### Module `gpp_2025` (82 files)
<details>
<summary>Click to view all 82 files in `gpp_2025` calling `connect.php`</summary>

- [`gpp_2025/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/approvePayment.php)
- [`gpp_2025/php/approveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/approveResched.php)
- [`gpp_2025/php/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/approveReviseWriteUp.php)
- [`gpp_2025/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/approveWriteUp.php)
- [`gpp_2025/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/changePassword.php)
- [`gpp_2025/php/completeRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/completeRequest.php)
- [`gpp_2025/php/disableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/disableAccount.php)
- [`gpp_2025/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/disapprovePayment.php)
- [`gpp_2025/php/disapproveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/disapproveResched.php)
- [`gpp_2025/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/disapproveWriteUp.php)
- [`gpp_2025/php/editUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/editUserPermissions.php)
- [`gpp_2025/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/emailForgot.php)
- [`gpp_2025/php/enableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/enableAccount.php)
- [`gpp_2025/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/loadWriteUpHistory.php)
- [`gpp_2025/php/pull2023.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pull2023.php)
- [`gpp_2025/php/pull2023Payments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pull2023Payments.php)
- [`gpp_2025/php/pullApplicants.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullApplicants.php)
- [`gpp_2025/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullBrowse.php)
- [`gpp_2025/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullCalendar.php)
- [`gpp_2025/php/pullCheckin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullCheckin.php)
- [`gpp_2025/php/pullClassicPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullClassicPayments.php)
- [`gpp_2025/php/pullClassicSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullClassicSubscribers.php)
- [`gpp_2025/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullDashboard.php)
- [`gpp_2025/php/pullDimensionsSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullDimensionsSubscribers.php)
- [`gpp_2025/php/pullFeedbacks.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullFeedbacks.php)
- [`gpp_2025/php/pullFragmentSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullFragmentSubscribers.php)
- [`gpp_2025/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullLogs.php)
- [`gpp_2025/php/pullLuxeoPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullLuxeoPayments.php)
- [`gpp_2025/php/pullLuxeoSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullLuxeoSubscribers.php)
- [`gpp_2025/php/pullManageSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullManageSchedules.php)
- [`gpp_2025/php/pullManageSchedulesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullManageSchedulesOnline.php)
- [`gpp_2025/php/pullManageTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullManageTimeSlot.php)
- [`gpp_2025/php/pullManageTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullManageTimeSlotOnline.php)
- [`gpp_2025/php/pullObraPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullObraPayments.php)
- [`gpp_2025/php/pullObraSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullObraSubscribers.php)
- [`gpp_2025/php/pullOnlineUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullOnlineUploads.php)
- [`gpp_2025/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullPaymentApproval.php)
- [`gpp_2025/php/pullPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullPayments.php)
- [`gpp_2025/php/pullProjectionSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullProjectionSubscribers.php)
- [`gpp_2025/php/pullRecruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullRecruitment.php)
- [`gpp_2025/php/pullReschedApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullReschedApproval.php)
- [`gpp_2025/php/pullScheduleAppointments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullScheduleAppointments.php)
- [`gpp_2025/php/pullScheduleAppointmentsOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullScheduleAppointmentsOnline.php)
- [`gpp_2025/php/pullScheduleDates.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullScheduleDates.php)
- [`gpp_2025/php/pullScheduleDatesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullScheduleDatesOnline.php)
- [`gpp_2025/php/pullScheduleTimes.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullScheduleTimes.php)
- [`gpp_2025/php/pullScheduleTimesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullScheduleTimesOnline.php)
- [`gpp_2025/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullSchedules.php)
- [`gpp_2025/php/pullSchools.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullSchools.php)
- [`gpp_2025/php/pullSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullSecondPayment.php)
- [`gpp_2025/php/pullSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullSubscribers.php)
- [`gpp_2025/php/pullTickets.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullTickets.php)
- [`gpp_2025/php/pullTransfers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullTransfers.php)
- [`gpp_2025/php/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullUploads.php)
- [`gpp_2025/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullUserList.php)
- [`gpp_2025/php/pullUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullUserPermissions.php)
- [`gpp_2025/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullWriteUpApproval.php)
- [`gpp_2025/php/pullWriteUps.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullWriteUps.php)
- [`gpp_2025/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/pullinfo.php)
- [`gpp_2025/php/resetStudentPass.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/resetStudentPass.php)
- [`gpp_2025/php/saveManageAddSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/saveManageAddSchedule.php)
- [`gpp_2025/php/saveManageAddScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/saveManageAddScheduleOnline.php)
- [`gpp_2025/php/saveSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/saveSchedule.php)
- [`gpp_2025/php/saveScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/saveScheduleOnline.php)
- [`gpp_2025/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/saveUser.php)
- [`gpp_2025/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/saveWriteUp.php)
- [`gpp_2025/php/setScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/setScheduleActive.php)
- [`gpp_2025/php/setScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/setScheduleActiveOnline.php)
- [`gpp_2025/php/setScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/setScheduleInActive.php)
- [`gpp_2025/php/setScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/setScheduleInActiveOnline.php)
- [`gpp_2025/php/setSingleScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/setSingleScheduleActive.php)
- [`gpp_2025/php/setSingleScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/setSingleScheduleActiveOnline.php)
- [`gpp_2025/php/setSingleScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/setSingleScheduleInActive.php)
- [`gpp_2025/php/setSingleScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/setSingleScheduleInActiveOnline.php)
- [`gpp_2025/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/testAdminLogin.php)
- [`gpp_2025/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/testLogin.php)
- [`gpp_2025/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/testUsername.php)
- [`gpp_2025/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/testWriteUpCount.php)
- [`gpp_2025/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/updateContact.php)
- [`gpp_2025/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/updateShipping.php)
- [`gpp_2025/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/updatepassword.php)
- [`gpp_2025/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2025/php/uploadPayment.php)

</details>

#### Module `gpp_2026` (82 files)
<details>
<summary>Click to view all 82 files in `gpp_2026` calling `connect.php`</summary>

- [`gpp_2026/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/approvePayment.php)
- [`gpp_2026/php/approveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/approveResched.php)
- [`gpp_2026/php/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/approveReviseWriteUp.php)
- [`gpp_2026/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/approveWriteUp.php)
- [`gpp_2026/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/changePassword.php)
- [`gpp_2026/php/completeRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/completeRequest.php)
- [`gpp_2026/php/disableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/disableAccount.php)
- [`gpp_2026/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/disapprovePayment.php)
- [`gpp_2026/php/disapproveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/disapproveResched.php)
- [`gpp_2026/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/disapproveWriteUp.php)
- [`gpp_2026/php/editUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/editUserPermissions.php)
- [`gpp_2026/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/emailForgot.php)
- [`gpp_2026/php/enableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/enableAccount.php)
- [`gpp_2026/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/loadWriteUpHistory.php)
- [`gpp_2026/php/pull2023.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pull2023.php)
- [`gpp_2026/php/pull2023Payments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pull2023Payments.php)
- [`gpp_2026/php/pullApplicants.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullApplicants.php)
- [`gpp_2026/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullBrowse.php)
- [`gpp_2026/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullCalendar.php)
- [`gpp_2026/php/pullCheckin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullCheckin.php)
- [`gpp_2026/php/pullClassicPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullClassicPayments.php)
- [`gpp_2026/php/pullClassicSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullClassicSubscribers.php)
- [`gpp_2026/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullDashboard.php)
- [`gpp_2026/php/pullDimensionsSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullDimensionsSubscribers.php)
- [`gpp_2026/php/pullFeedbacks.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullFeedbacks.php)
- [`gpp_2026/php/pullFragmentSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullFragmentSubscribers.php)
- [`gpp_2026/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullLogs.php)
- [`gpp_2026/php/pullLuxeoPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullLuxeoPayments.php)
- [`gpp_2026/php/pullLuxeoSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullLuxeoSubscribers.php)
- [`gpp_2026/php/pullManageSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullManageSchedules.php)
- [`gpp_2026/php/pullManageSchedulesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullManageSchedulesOnline.php)
- [`gpp_2026/php/pullManageTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullManageTimeSlot.php)
- [`gpp_2026/php/pullManageTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullManageTimeSlotOnline.php)
- [`gpp_2026/php/pullObraPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullObraPayments.php)
- [`gpp_2026/php/pullObraSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullObraSubscribers.php)
- [`gpp_2026/php/pullOnlineUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullOnlineUploads.php)
- [`gpp_2026/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullPaymentApproval.php)
- [`gpp_2026/php/pullPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullPayments.php)
- [`gpp_2026/php/pullProjectionSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullProjectionSubscribers.php)
- [`gpp_2026/php/pullRecruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullRecruitment.php)
- [`gpp_2026/php/pullReschedApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullReschedApproval.php)
- [`gpp_2026/php/pullScheduleAppointments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullScheduleAppointments.php)
- [`gpp_2026/php/pullScheduleAppointmentsOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullScheduleAppointmentsOnline.php)
- [`gpp_2026/php/pullScheduleDates.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullScheduleDates.php)
- [`gpp_2026/php/pullScheduleDatesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullScheduleDatesOnline.php)
- [`gpp_2026/php/pullScheduleTimes.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullScheduleTimes.php)
- [`gpp_2026/php/pullScheduleTimesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullScheduleTimesOnline.php)
- [`gpp_2026/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullSchedules.php)
- [`gpp_2026/php/pullSchools.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullSchools.php)
- [`gpp_2026/php/pullSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullSecondPayment.php)
- [`gpp_2026/php/pullSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullSubscribers.php)
- [`gpp_2026/php/pullTickets.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullTickets.php)
- [`gpp_2026/php/pullTransfers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullTransfers.php)
- [`gpp_2026/php/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullUploads.php)
- [`gpp_2026/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullUserList.php)
- [`gpp_2026/php/pullUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullUserPermissions.php)
- [`gpp_2026/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullWriteUpApproval.php)
- [`gpp_2026/php/pullWriteUps.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullWriteUps.php)
- [`gpp_2026/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/pullinfo.php)
- [`gpp_2026/php/resetStudentPass.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/resetStudentPass.php)
- [`gpp_2026/php/saveManageAddSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/saveManageAddSchedule.php)
- [`gpp_2026/php/saveManageAddScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/saveManageAddScheduleOnline.php)
- [`gpp_2026/php/saveSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/saveSchedule.php)
- [`gpp_2026/php/saveScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/saveScheduleOnline.php)
- [`gpp_2026/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/saveUser.php)
- [`gpp_2026/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/saveWriteUp.php)
- [`gpp_2026/php/setScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/setScheduleActive.php)
- [`gpp_2026/php/setScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/setScheduleActiveOnline.php)
- [`gpp_2026/php/setScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/setScheduleInActive.php)
- [`gpp_2026/php/setScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/setScheduleInActiveOnline.php)
- [`gpp_2026/php/setSingleScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/setSingleScheduleActive.php)
- [`gpp_2026/php/setSingleScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/setSingleScheduleActiveOnline.php)
- [`gpp_2026/php/setSingleScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/setSingleScheduleInActive.php)
- [`gpp_2026/php/setSingleScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/setSingleScheduleInActiveOnline.php)
- [`gpp_2026/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/testAdminLogin.php)
- [`gpp_2026/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/testLogin.php)
- [`gpp_2026/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/testUsername.php)
- [`gpp_2026/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/testWriteUpCount.php)
- [`gpp_2026/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/updateContact.php)
- [`gpp_2026/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/updateShipping.php)
- [`gpp_2026/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/updatepassword.php)
- [`gpp_2026/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_2026/php/uploadPayment.php)

</details>

#### Module `gpp_fragment` (82 files)
<details>
<summary>Click to view all 82 files in `gpp_fragment` calling `connect.php`</summary>

- [`gpp_fragment/php/approvePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/approvePayment.php)
- [`gpp_fragment/php/approveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/approveResched.php)
- [`gpp_fragment/php/approveReviseWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/approveReviseWriteUp.php)
- [`gpp_fragment/php/approveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/approveWriteUp.php)
- [`gpp_fragment/php/changePassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/changePassword.php)
- [`gpp_fragment/php/completeRequest.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/completeRequest.php)
- [`gpp_fragment/php/disableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/disableAccount.php)
- [`gpp_fragment/php/disapprovePayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/disapprovePayment.php)
- [`gpp_fragment/php/disapproveResched.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/disapproveResched.php)
- [`gpp_fragment/php/disapproveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/disapproveWriteUp.php)
- [`gpp_fragment/php/editUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/editUserPermissions.php)
- [`gpp_fragment/php/emailForgot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/emailForgot.php)
- [`gpp_fragment/php/enableAccount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/enableAccount.php)
- [`gpp_fragment/php/loadWriteUpHistory.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/loadWriteUpHistory.php)
- [`gpp_fragment/php/pull2023.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pull2023.php)
- [`gpp_fragment/php/pull2023Payments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pull2023Payments.php)
- [`gpp_fragment/php/pullApplicants.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullApplicants.php)
- [`gpp_fragment/php/pullBrowse.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullBrowse.php)
- [`gpp_fragment/php/pullCalendar.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullCalendar.php)
- [`gpp_fragment/php/pullCheckin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullCheckin.php)
- [`gpp_fragment/php/pullClassicPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullClassicPayments.php)
- [`gpp_fragment/php/pullClassicSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullClassicSubscribers.php)
- [`gpp_fragment/php/pullDashboard.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullDashboard.php)
- [`gpp_fragment/php/pullDimensionsSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullDimensionsSubscribers.php)
- [`gpp_fragment/php/pullFeedbacks.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullFeedbacks.php)
- [`gpp_fragment/php/pullFragmentSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullFragmentSubscribers.php)
- [`gpp_fragment/php/pullLogs.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullLogs.php)
- [`gpp_fragment/php/pullLuxeoPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullLuxeoPayments.php)
- [`gpp_fragment/php/pullLuxeoSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullLuxeoSubscribers.php)
- [`gpp_fragment/php/pullManageSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullManageSchedules.php)
- [`gpp_fragment/php/pullManageSchedulesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullManageSchedulesOnline.php)
- [`gpp_fragment/php/pullManageTimeSlot.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullManageTimeSlot.php)
- [`gpp_fragment/php/pullManageTimeSlotOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullManageTimeSlotOnline.php)
- [`gpp_fragment/php/pullObraPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullObraPayments.php)
- [`gpp_fragment/php/pullObraSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullObraSubscribers.php)
- [`gpp_fragment/php/pullOnlineUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullOnlineUploads.php)
- [`gpp_fragment/php/pullPaymentApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullPaymentApproval.php)
- [`gpp_fragment/php/pullPayments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullPayments.php)
- [`gpp_fragment/php/pullProjectionSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullProjectionSubscribers.php)
- [`gpp_fragment/php/pullRecruitment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullRecruitment.php)
- [`gpp_fragment/php/pullReschedApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullReschedApproval.php)
- [`gpp_fragment/php/pullScheduleAppointments.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullScheduleAppointments.php)
- [`gpp_fragment/php/pullScheduleAppointmentsOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullScheduleAppointmentsOnline.php)
- [`gpp_fragment/php/pullScheduleDates.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullScheduleDates.php)
- [`gpp_fragment/php/pullScheduleDatesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullScheduleDatesOnline.php)
- [`gpp_fragment/php/pullScheduleTimes.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullScheduleTimes.php)
- [`gpp_fragment/php/pullScheduleTimesOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullScheduleTimesOnline.php)
- [`gpp_fragment/php/pullSchedules.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullSchedules.php)
- [`gpp_fragment/php/pullSchools.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullSchools.php)
- [`gpp_fragment/php/pullSecondPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullSecondPayment.php)
- [`gpp_fragment/php/pullSubscribers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullSubscribers.php)
- [`gpp_fragment/php/pullTickets.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullTickets.php)
- [`gpp_fragment/php/pullTransfers.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullTransfers.php)
- [`gpp_fragment/php/pullUploads.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullUploads.php)
- [`gpp_fragment/php/pullUserList.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullUserList.php)
- [`gpp_fragment/php/pullUserPermissions.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullUserPermissions.php)
- [`gpp_fragment/php/pullWriteUpApproval.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullWriteUpApproval.php)
- [`gpp_fragment/php/pullWriteUps.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullWriteUps.php)
- [`gpp_fragment/php/pullinfo.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/pullinfo.php)
- [`gpp_fragment/php/resetStudentPass.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/resetStudentPass.php)
- [`gpp_fragment/php/saveManageAddSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/saveManageAddSchedule.php)
- [`gpp_fragment/php/saveManageAddScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/saveManageAddScheduleOnline.php)
- [`gpp_fragment/php/saveSchedule.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/saveSchedule.php)
- [`gpp_fragment/php/saveScheduleOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/saveScheduleOnline.php)
- [`gpp_fragment/php/saveUser.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/saveUser.php)
- [`gpp_fragment/php/saveWriteUp.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/saveWriteUp.php)
- [`gpp_fragment/php/setScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/setScheduleActive.php)
- [`gpp_fragment/php/setScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/setScheduleActiveOnline.php)
- [`gpp_fragment/php/setScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/setScheduleInActive.php)
- [`gpp_fragment/php/setScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/setScheduleInActiveOnline.php)
- [`gpp_fragment/php/setSingleScheduleActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/setSingleScheduleActive.php)
- [`gpp_fragment/php/setSingleScheduleActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/setSingleScheduleActiveOnline.php)
- [`gpp_fragment/php/setSingleScheduleInActive.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/setSingleScheduleInActive.php)
- [`gpp_fragment/php/setSingleScheduleInActiveOnline.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/setSingleScheduleInActiveOnline.php)
- [`gpp_fragment/php/testAdminLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/testAdminLogin.php)
- [`gpp_fragment/php/testLogin.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/testLogin.php)
- [`gpp_fragment/php/testUsername.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/testUsername.php)
- [`gpp_fragment/php/testWriteUpCount.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/testWriteUpCount.php)
- [`gpp_fragment/php/updateContact.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/updateContact.php)
- [`gpp_fragment/php/updateShipping.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/updateShipping.php)
- [`gpp_fragment/php/updatepassword.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/updatepassword.php)
- [`gpp_fragment/php/uploadPayment.php`](file:///Users/lei/Downloads/adastra/manualtest/gpp_fragment/php/uploadPayment.php)

</details>

### 2.6 Master Database and Credential Inventory Matrix

The table below consolidates all MySQL databases referenced across the codebase, identifying the user account, connection type, and consuming modules:

| Database Name | Primary User Account | Connection Types | Consuming Modules | Primary Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `theadast_byo2027` | `theadast_jad` | connect.php + hardcoded | `admin`, `adastra2027`, `2027ticket` | Current graduating batch yearbook subscriptions & photobooth |
| `theadast_byo2026` | `theadast_jad` | connect.php + hardcoded | `admin`, `adastra2026`, `2026ticket`, `gpp_2026` | 2026 batch yearbook subscriptions & claiming |
| `theadast_byo2025` | `theadast_jad` | connect.php + hardcoded | `admin`, `adastra2025`, `adastra2026`, `adastra2027`, `gpp_2025` | 2025 batch yearbook subscriptions & claiming |
| `theadast_byo2024` | `theadast_jad` | connect.php + hardcoded | `admin`, `adastra2024`, `adastra2026`, `adastra2027`, `gpp_2024`, `gpp_claiming` | 2024 batch yearbook subscriptions & claiming |
| `theadast_byo2023` | `theadast_jad` | connect.php + hardcoded | `admin`, `adastra2023`, `adastra2026`, `adastra2027`, `gpp_2023` | 2023 batch yearbook subscriptions & claiming |
| `theadast_claiming` | `theadast_jad` | Hardcoded direct connection | `admin` | Central GPP claiming, schedules & appointment management |
| `theadast_cspanel` | `theadast_jad` | Hardcoded direct connection | `admin` | Cabinet, yearbook specifications & customer service panel |
| `theadast_byo2021` | `theadast_jad / theadast` | connect.php + databaseWrapper.php | `admin`, `fragment202122`, `gpp_fragment` | Fragment 2021 yearbook subscriptions |
| `theadast_byo2020` | `theadast_jad` | connect.php | `admin`, `fragment202122`, multi-year admin lookups | 2020 batch yearbook historical archive |
| `theadast_byo2019` | `theadast_jad / theadast` | connect.php + database_master.php | `admin`, `obra2019`, multi-year admin lookups | Obra 2019 yearbook portal & historical data |
| `theadast_byo2018` | `theadast_jad` | connect.php | `admin`, multi-year admin lookups | 2018 batch historical archive |
| `theadast_byo` | `theadast_jad` | connect.php | `admin`, multi-year admin lookups | Dimensions 2017 historical archive |
| `theadast_byo2016` | `theadast_jad` | connect.php | `admin`, multi-year admin lookups | Projection 2016 historical archive |
| `theadast_recruitment2021` | `theadast_jad` | connection.php (hardcoded) | `fragment202122/recruitmentTest` | Recruitment form applications (test) |
| `theadast_recruitment` | `theadast_jad` | connect.php | `admin`, multi-year admin lookups | Staff and editorial recruitment records |
| `theadast_fragmentTest` | `theadast_jad` | success.php (hardcoded) | `fragment202122/fragmenttest` | Test registration sandbox |

---

*Report generated autonomously by Antigravity Codebase Audit Agent.*
