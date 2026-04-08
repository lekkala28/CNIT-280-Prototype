# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Special Space** is a static HTML/CSS/JS prototype (CNIT 280 course project) for a community platform connecting families, caregivers, and individuals with special needs to local events, volunteers, and support resources in "Whispering Hills." There is no build system, no framework, and no backend -- all pages are standalone HTML files opened directly in a browser or served via GitHub Pages.

## Running the Site

Open any `.html` file directly in a browser. No build step, no dev server, no dependencies to install. Pushes to `main` auto-deploy to GitHub Pages via `.github/workflows/static.yml`.

## Architecture

### Data Persistence

All data is stored in the browser's `localStorage`. Pages share data through these keys:

- `special_space_profile_v1` -- user profile (profile.html, dashboard.html)
- `special_space_volunteers` -- volunteer list (volunteer.html, dashboard.html)
- `special_space_events_v1` -- events (events.html, dashboard.html)
- `special_space_announcement_v1` -- announcements (dashboard.html)
- `special_space_success_stories_v1` -- success stories (success.html)
- `special_space_jobs_v1` -- job postings (jobs.html)
- `special_space_slots_v1` -- available appointment time slots (appointments.html)
- `special_space_families_v1` -- family directory entries (families.html)
- `appointments` -- booked appointments (appointments.html; note: not namespaced like the others)
- `special_space_health_entries_v1` -- health metric entries (health.html), each entry has optional `clientId` for caregiver-tracked clients
- `special_space_care_plan_v1` -- user's own care plan (health.html)
- `special_space_care_plan_v1_clients` -- object keyed by client ID containing client care plans (health.html)
- `special_space_clients_v1` -- caregiver's client list (health.html)
- `special_space_caregiver_mode_v1` -- boolean toggle for caregiver mode (health.html)

The dashboard reads from profile, volunteers, events, and announcement keys to display aggregate KPIs.

### UI Design Systems

The pages use three different visual themes that are not unified:

1. **Sidebar layout (dark purple `#1a0033` background):** index.html, contact.html, success.html, jobs.html, health.html -- uses Orbitron font, fixed 220px black sidebar with white buttons
2. **Header/nav layout (dark navy `#0b1220` background):** dashboard.html, events.html, help.html, volunteer.html -- uses Arial, top header bar with pill-shaped nav links
3. **Standalone pages:** login.html (centered card, dark purple gradient), appointments.html (light theme, white card on `#f4f7fb`), families.html (light theme, `#f4f7fb`), and job.html (dark navy `#03122b`, hero layout) each have their own unique layout

### Two Separate Job Pages

`jobs.html` and `job.html` are two independent pages that both deal with job listings but have completely different designs and share no data. `jobs.html` uses the sidebar layout and stores data under `special_space_jobs_v1`. `job.html` is a standalone page with a hero/card layout and hardcoded static job listings (no localStorage). Most nav links point to `jobs.html`; only index.html links to `job.html`.

### Shared Patterns

- `escapeHtml()` is copy-pasted into every file that renders user content -- there is no shared JS file
- All CSS is inline `<style>` blocks per page -- there are no external stylesheets
- Forms use `localStorage` for persistence and `alert()` / `confirm()` for user feedback
- Navigation is duplicated per page with inconsistent link sets across the two layout systems

### Missing Pages

- `profile.html` -- linked from navigation on multiple pages but does not exist in the repo

### Known Issues

- success.html sidebar links to `success-stories.html` but the actual file is `success.html`
- `success.html:275` has a stray `s` after `stories.splice(i, 1);s` causing a runtime error when deleting stories
- volunteer.html does not escape user-provided HTML in rendered output (XSS risk) -- it has no `escapeHtml()` function despite using `innerHTML`
- appointments.html uses `escapeHtml()` in some places but renders user content via `innerHTML` without escaping in others (XSS risk)
- appointments.html localStorage key (`appointments`) is not namespaced like the other keys (`special_space_*`)
- Navigation links vary by page -- sidebar-layout pages and header-layout pages link to different subsets of pages
- index.html links to `job.html` while all other pages link to `jobs.html` -- these are two unrelated pages
