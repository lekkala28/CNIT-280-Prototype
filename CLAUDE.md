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
- `appointments` -- booked appointments (appointments.html; note: not namespaced like the others)

The dashboard reads from profile, volunteers, events, and announcement keys to display aggregate KPIs.

### UI Design Systems

The pages use three different visual themes that are not unified:

1. **Sidebar layout (dark purple `#1a0033` background):** index.html, contact.html, success.html, jobs.html -- uses Orbitron font, fixed 220px black sidebar with white buttons
2. **Header/nav layout (dark navy `#0b1220` background):** dashboard.html, events.html, help.html, volunteer.html -- uses Arial, top header bar with pill-shaped nav links
3. **Standalone pages:** login.html (centered card, dark purple gradient) and appointments.html (light theme, white card on `#f4f7fb` background) each have their own unique layout

### Shared Patterns

- `escapeHtml()` is copy-pasted into every file that renders user content -- there is no shared JS file
- All CSS is inline `<style>` blocks per page -- there are no external stylesheets
- Forms use `localStorage` for persistence and `alert()` / `confirm()` for user feedback
- Navigation is duplicated per page with inconsistent link sets across the two layout systems

### Missing Pages

- `profile.html` -- linked from navigation on multiple pages but does not exist in the repo
- `resources.html` -- referenced in the CLAUDE.md previously but does not exist in the repo

### Known Issues

- success.html sidebar links to `success-stories.html` but the actual file is `success.html`
- `success.html:275` has a stray `s` after `stories.splice(i, 1);s` causing a runtime error when deleting stories
- volunteer.html does not escape user-provided HTML in rendered output (XSS risk)
- appointments.html does not escape user-provided HTML in rendered output (XSS risk)
- appointments.html localStorage key (`appointments`) is not namespaced like the other keys (`special_space_*`)
- Navigation links vary by page -- sidebar-layout pages and header-layout pages link to different subsets of pages
