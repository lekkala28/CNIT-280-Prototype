# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Special Space** is a static HTML/CSS/JS prototype (CNIT 280 course project) for a community platform connecting families, caregivers, and individuals with special needs to local events, volunteers, and support resources in "Whispering Hills." There is no build system, no framework, and no backend -- all pages are standalone HTML files opened directly in a browser or served via GitHub Pages.

## Running the Site

Open any `.html` file directly in a browser. No build step, no dev server, no dependencies to install. Pushes to `main` auto-deploy to GitHub Pages via `.github/workflows/static.yml`.

## Architecture

### Data Persistence

All data is stored in the browser's `localStorage`. Pages share data through these keys:

- `special_space_volunteers` -- volunteer list (volunteer.html, dashboard.html)
- `special_space_events_v1` -- events (events.html, dashboard.html)
- `special_space_announcement_v1` -- announcements (dashboard.html)
- `special_space_success_stories_v1` -- success stories (success.html)
- `special_space_jobs_v1` -- job postings (jobs.html)
- `special_space_appointments_v1` -- booked appointments (appointments.html). Old non-namespaced key `appointments` is auto-migrated on page load.
- `special_space_slots_v1` -- available appointment time slots (appointments.html)
- `special_space_families_v1` -- family directory entries (families.html)
- `special_space_health_entries_v1` -- health metric entries (health.html), each entry has optional `clientId` for caregiver-tracked clients
- `special_space_care_plan_v1` -- user's own care plan (health.html)
- `special_space_care_plan_v1_clients` -- object keyed by client ID containing client care plans (health.html)
- `special_space_clients_v1` -- caregiver's client list (health.html)
- `special_space_caregiver_mode_v1` -- boolean toggle for caregiver mode (health.html)
- `special_space_job_applications_v1` -- job applications submitted via job.html
- `background_check_requests_v1` -- background check requests (background.html)
- `selected_volunteer` -- transient handoff from volunteer.html to backgroundcheck.html

### Navigation

Every page (except login.html) includes the same fixed-left 220px black sidebar with 15 links in a consistent order:
Home, Dashboard, Events, Volunteer, Open Jobs, Appointments, Families, Success Stories, Health Info, Background Check, Newsletter, Help, Contact, Login, and a Logout button that `alert()`s. The sidebar has `overflow-y: auto` so it scrolls on short viewports.

Pages reserve 220px of left padding on `<body>` to keep content clear of the fixed sidebar.

### UI Theme

Three background themes coexist but all share the same sidebar:

1. **Dark purple (`#1a0033`):** index, contact, success, jobs, health, newsletter, background, backgroundcheck -- uses Orbitron font for headings
2. **Dark navy (`#0b1220` / `#03122b`):** dashboard, events, help, volunteer, job -- uses Arial
3. **Light (`#f4f7fb`):** appointments, families

### Two Separate Job Pages

`jobs.html` and `job.html` are two independent pages. `jobs.html` is the community job-posting board (users post/browse jobs, stored in `special_space_jobs_v1`). `job.html` is a static listing of internal Special Space openings with an application form that writes to `special_space_job_applications_v1`. Sidebar "Open Jobs" links to `jobs.html` on every page; only `index.html` has an additional link to `job.html`.

### Shared Patterns

- `escapeHtml()` is copy-pasted into every file that renders user content -- there is no shared JS file
- All CSS is inline `<style>` blocks per page -- there are no external stylesheets
- Forms use `localStorage` for persistence and `alert()` / `confirm()` for user feedback

### Missing Pages

- `profile.html` does not exist and is intentionally not linked from any sidebar.
