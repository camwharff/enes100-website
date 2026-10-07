# ENES100 Course Website

**The public course website for ENES100: Introduction to Engineering Design at the University of Maryland**, rebuilt as a statically generated SvelteKit app. Hundreds of first-year engineering students and course staff used it every week for lab hours, hardware library documentation, and course tools.

> **Archived for my portfolio.** This repository is a frozen snapshot of the site as of December 2025, when I handed it off. The live site at [enes100.umd.edu](https://enes100.umd.edu) is still maintained by the ENES100 program and may have changed since then. The original repository is [umdenes100/enes100-website](https://github.com/umdenes100/enes100-website).

**Timeline:** Started July 2024 · Launched October 2024 · Maintained through December 2025

**Stack:** SvelteKit · TypeScript · Svelte 4 · Vite · GitHub Actions · GitHub Pages · Google Calendar API

---

## Overview

ENES100 is UMD's first-year engineering design course. Every semester, hundreds of students build autonomous over-terrain vehicles (OTVs) and rely on the course website for:

- **Lab availability:** whether the two lab rooms are open, have a class in session, or are closed.
- **Hardware library documentation** for the course's Arduino (C++) and MicroPython libraries, plus examples, a tank-platform guide, and FAQs.
- **Course tools:** the simulator, vision system, machine learning, and tutorial video pages.
- **Course information:** the instructional team, the student showcase, and teaching fellow applications.

As an ENES100 Laboratory Teaching Fellow, I proposed rebuilding the previous course site and led the project from design through launch. I designed every page, first as graphic mockups and then as HTML/CSS prototypes. Then I built the site in Svelte together with a fellow teaching fellow who had SvelteKit experience. After launch I was its primary maintainer for more than a year, and before I left the role I trained a successor to take over maintenance and development.

> **A note on the commit history:** much of the early build (August–September 2024) was pair-programmed on a teammate's laptop, so some of my code from that period is committed under his account.

## My contributions

- **Design:** the site's full visual identity, including typography, color palette, page layouts, and responsive layout rules. I took it from graphic mockups to HTML/CSS prototypes before we built it in Svelte.
- **Designed and built the site's pages:** the home page, the About, Team, and Showcase pages, the FAQ and examples documentation, the ML page port, the course technology page, the tutorial videos page, the School Store page, and the teaching fellow application page. I also created the MicroPython documentation page, which other teaching fellows then filled in.
- **Lab status system:** each lab room's weekly schedule is encoded as an hourly grid (7 days × 24 hours) that maps to `closed`, `class`, or `open`. On page load, the home page computes the current and next-hour status for each room. Because the status widget sits on the site's front page, we chose a hardcoded schedule grid over an API call. The status is computed instantly in the browser with no network request, no API key, and no dependency on an outside service, which keeps the most-visited page lightweight. Since the lab schedule changes only once a semester, updating the grid each term was a small cost. I built the original version and updated the schedules every semester.
- **Lab display page (`/labDisplay`):** a full-screen page for a monitor in the lab. It shows a live clock and date, and it pulls current and upcoming staff shifts from a shared Google Calendar through the Google Calendar API.
- **Responsive styling:** home page animations, plus styling that works for a public-facing landing page and for dense technical documentation. I also made every page work on mobile.
- **Deployment and analytics:** debugged and fixed the GitHub Actions deployment to GitHub Pages, and added Google Analytics.
- **Ongoing maintenance:** semester-by-semester content updates (staff, schedules, applications, lab "spirit days"), bug fixes, and support for other teaching fellows who edited documentation pages.

## Conference presentation
 
The website and lab display were presented as part of the poster *"Strategies for Managing a High-Throughput Academic Makerspace"* at **ISAM 2025** (International Symposium on Academic Makerspaces). The lab display appears on the poster as the labs' digital signage. See the [VELMA README](https://github.com/camwharff/enes100-velma#conference-presentation) for the poster.

## Features

| Area | Routes | Description |
|---|---|---|
| Home | `/` | Course landing page with live lab status for JMP 1116 and JMP 1120, plus quick links |
| Lab display | `/labDisplay` | Wall-monitor view: live clock and current/upcoming staff shifts from Google Calendar |
| Tools | `/tools/*` | Simulator, vision system, machine learning, model uploader, and tutorial videos |
| Labs | `/labs/*` | Lab schedule, School Store, maintenance form, and tool check (embedded internal apps) |
| Documentation | `/documentation/*` | Arduino and MicroPython library references, examples, tank library and guide, FAQ |
| About | `/about/*` | Course overview, instructional team, student showcase, and teaching fellow applications |

## Technical notes

- **Static generation:** the site uses `@sveltejs/adapter-static` with `prerender = true`, so every route is built to plain HTML. GitHub Pages can host it with no server.
- **Markdown inside Svelte:** a `MarkdownContent` component renders Markdown written directly inside `.svelte` files with [`marked`](https://github.com/markedjs/marked). Course staff with no web development experience could edit documentation pages, even from the GitHub web editor.
- **Shared layouts:** `Header`, `Footer`, `SidebarLayout`, and `FullPageLayout` components keep navigation and styling consistent across sections. Global styles live in `src/lib/global.css`.
- **Deployment:** `.github/workflows/deploy.yml` installs dependencies, builds to `build/`, and publishes with `actions/deploy-pages`.

## Project structure

```
src/
├── app.html                 # HTML shell (includes analytics tag)
├── lib/                     # Shared components and global styles
│   ├── Header.svelte
│   ├── Footer.svelte
│   ├── MarkdownContent.svelte
│   ├── SidebarLayout.svelte
│   └── global.css
└── routes/                  # File-based routing; folders mirror URLs
    ├── +page.svelte         # Home page + lab status logic
    ├── labDisplay/          # Lab wall-monitor display
    ├── about/
    ├── documentation/
    ├── labs/
    └── tools/
static/
├── img/                     # Images and Interstate font files
└── cad/                     # Downloadable WiFi camera case CAD files
```

## Running locally

Requires Node.js (developed on v22; CI builds with v18).

```bash
git clone https://github.com/camwharff/enes100-website.git
cd enes100-website
npm install
npm run dev        # http://localhost:5173
npm run build      # static output in build/
```

### Editing content (maintainer guide)

This is the workflow I documented for the teaching fellows who maintained content:

1. Find the page's route in the URL (for example, `/documentation/faq`).
2. Open the matching folder under `src/routes/` and edit its `+page.svelte`.
3. Anything inside `<MarkdownContent> … </MarkdownContent>` is Markdown. Anything outside it is HTML/Svelte.
4. For a new page, create a folder with a `+page.svelte` that imports `MarkdownContent` from `$lib/MarkdownContent.svelte`.
5. Commit and push to `master`. GitHub Actions deploys the change automatically.

## Credits

Built for the UMD Keystone Engineering Program's ENES100 course. **Forrest Milner** brought SvelteKit experience to the project. He set up the repository and the GitHub Pages deployment workflow, pair-programmed the initial Svelte build with me (including the shared header, footer, sidebar, and Markdown components), and wrote the ML model uploader. Other ENES100 teaching fellows contributed documentation and content updates, mainly to the MicroPython and machine learning pages.
