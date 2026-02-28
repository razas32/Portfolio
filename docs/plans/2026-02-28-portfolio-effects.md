# Portfolio Effects Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Add scramble text on headings, convert project section to a tilt-card grid, and add a slide-in detail panel.

**Architecture:** All vanilla JS/CSS in index.html + global.css. Project data moves from hardcoded HTML into a JS array (trusted, static data — no user input) so cards and the detail panel share one source of truth. Panel/overlay markup added once to the bottom of body.

**Tech Stack:** Vanilla JS, CSS custom properties, IntersectionObserver (already in use), CSS transforms

---

## Project Data Reference

7 projects currently in the page:
1. Franky — Private
2. Real-time SDR for mono/stereo FM — github.com/razas32/sdr
3. Hardware-Based Image Decompressor — github.com/razas32/dq_project
4. Payroll by Vanguard Financial — Private
5. LiDAR Spatial Mapper — Private
6. Kinship Canada — kinshipcanada.com (Private repo)
7. Model Pacemaker — github.com/shoongi/3K04_Project

---

### Task 1: Define project data as a JS array

**Files:**
- Modify: index.html — add const PROJECTS = [...] at top of existing script block

**Step 1:** Find the existing inline script tag near bottom of body. Add the PROJECTS array at the top of that block:

```js
const PROJECTS = [
  {
    id: 'franky',
    title: 'Franky',
    team: 1,
    timeline: 'February 2024 – June 2024',
    summary: 'AI-powered Rocket League bot using deep reinforcement learning.',
    description: 'Franky is an AI-powered bot for Rocket League, developed using Python and RLGym. Leveraging deep reinforcement learning, Franky adapts to high-level gameplay with real-time decision-making and dynamic in-game responses.',
    highlights: [
      'Implemented behavior logic for positioning, velocity management, and boost tracking.',
      'Encoded player, ball, and boost data into numerical formats for neural network processing.',
      'Designed a neural network with an LSTM model for sequential decision-making.',
      'Developed a reward system to encourage scoring, defense, and speed maintenance.',
      'Operates at 120 TPS, optimizing actions while avoiding unnecessary calculations.',
    ],
    tags: ['Python', 'RLGym', 'Pandas', 'PyTorch', 'Machine Learning', 'Game Development'],
    link: null, linkLabel: null, linkIcon: null, private: true,
  },
  {
    id: 'sdr',
    title: 'Real-time SDR for mono/stereo FM',
    team: 4,
    timeline: 'February 2024 – March 2024',
    summary: 'Software-defined radio system for FM and RDS decoding in a Unix environment.',
    description: 'Developed a software-defined radio (SDR) system for FM and RDS decoding within a Unix environment, leveraging pipelines for efficient data flow. Implemented advanced DSP algorithms in C++ for real-time mono and stereo FM demodulation with multithreading. Utilized Python for system validation and performance analysis.',
    highlights: [],
    tags: ['Linux', 'C++', 'Python', 'Gnuplot', 'CMake', 'SciPy', 'NumPy', 'DSP'],
    link: 'https://github.com/razas32/sdr', linkLabel: 'View on GitHub', linkIcon: 'github', private: false,
  },
  {
    id: 'fpga',
    title: 'Hardware-Based Image Decompressor',
    team: 2,
    timeline: 'October 2023 – December 2023',
    summary: 'Hardware image decompression system using Verilog on an Altera DE2-115 FPGA.',
    description: 'Hardware-based image decompression on an Altera DE2-115 FPGA. Developed YUV-to-RGB conversion logic, optimized memory management across SRAM and dual-port RAMs, integrated UART communication, and interfaced with a VGA controller for real-time display.',
    highlights: [],
    tags: ['Verilog', 'ModelSim', 'Quartus Prime', 'FPGA', 'Digital Design'],
    link: 'https://github.com/razas32/dq_project', linkLabel: 'View on GitHub', linkIcon: 'github', private: false,
  },
  {
    id: 'payroll',
    title: 'Payroll by Vanguard Financial',
    team: 1,
    timeline: 'May 2023 – Aug 2023',
    summary: 'Full-stack payroll management system with role-based access for accountants and clients.',
    description: 'Full-stack application to streamline payroll management for Vanguard Financial. Separate login systems, API routes, and query permissions for accountants vs. clients.',
    highlights: [
      'TypeScript backend with PostgreSQL for data storage and retrieval.',
      'JWT authentication and authorization for both client and accountant roles.',
      'Client interface for payroll upload and employee onboarding.',
      'Standardized API routes for accountant payroll processing.',
      'React.js frontend focused on intuitive, user-friendly interface.',
    ],
    tags: ['TypeScript', 'PostgreSQL', 'JWT', 'React.js', 'REST API', 'Full-Stack Development'],
    link: null, linkLabel: null, linkIcon: null, private: true,
  },
  {
    id: 'lidar',
    title: 'LiDAR Spatial Mapper',
    team: 1,
    timeline: 'February 2023 – April 2023',
    summary: 'Real-time 3D spatial mapping system using a ToF sensor and MSP432 microcontroller.',
    description: 'Real-time 3D spatial mapping integrating a VL531X time-of-flight sensor, 28BYJ-48 stepper motor, and MSP432E401Y microcontroller. I2C and UART transmit sensor data to PC. Python + Open3D transform raw distances into a dynamic 3D point cloud.',
    highlights: [],
    tags: ['C', 'Python', 'NumPy', 'Open3D', 'Embedded Systems'],
    link: null, linkLabel: null, linkIcon: null, private: true,
  },
  {
    id: 'kinship',
    title: 'Kinship Canada',
    team: 2,
    timeline: 'January 2023 – March 2023',
    summary: 'CRA-compliant custom payment processing system for a Canadian charity.',
    description: 'Custom payment processing system compliant with Canada Revenue Agency regulations, built for Kinship Canada. TypeScript/Node.js backend handles transactions, donation tracking, and donor management. Stripe manages financial operations.',
    highlights: [],
    tags: ['JavaScript', 'TypeScript', 'React', 'Node.js', 'Stripe', 'Full-stack Development'],
    link: 'https://www.kinshipcanada.com/', linkLabel: 'Visit Website', linkIcon: 'external', private: true,
  },
  {
    id: 'pacemaker',
    title: 'Model Pacemaker',
    team: 5,
    timeline: 'September 2022 – December 2022',
    summary: 'Pacemaker simulation model with a custom Python/Tkinter GUI for real-time control.',
    description: 'Pacemaker simulation in MATLAB and Simulink with algorithms for atrial and ventricular pacing across multiple operational modes. Packaged with a custom Python/Tkinter GUI for real-time user interaction and control.',
    highlights: [],
    tags: ['MATLAB', 'Simulink', 'Python', 'Tkinter', 'pySerial', 'GUI Design'],
    link: 'https://github.com/shoongi/3K04_Project', linkLabel: 'View on GitHub', linkIcon: 'github', private: false,
  },
];
```

**Step 2 — Visual check:** Open browser console on index.html. Type PROJECTS. Should log array of 7 objects with no errors.

**Step 3 — Commit:**
```bash
git add index.html
git commit -m "feat: define project data as JS array"
```

---

### Task 2: Restructure projects section HTML + add panel markup

**Files:**
- Modify: index.html

**Step 1:** Inside the section#projects, keep the h2 but remove all 7 project divs. Replace with one empty div:

```html
<section id="projects" class="section" aria-labelledby="projects-heading">
    <h2 id="projects-heading">Projects</h2>
    <div class="projects-grid" id="projects-grid"></div>
</section>
```

**Step 2:** Before the closing body tag, add the panel and overlay:

```html
<div class="panel-overlay" id="panel-overlay" aria-hidden="true"></div>
<aside class="project-detail-panel" id="project-detail-panel" role="dialog" aria-modal="true" aria-label="Project details">
    <button class="panel-close-btn" id="panel-close-btn" aria-label="Close panel">&times;</button>
    <div class="panel-inner" id="panel-inner"></div>
</aside>
```

**Step 3 — Visual check:** Reload. Projects section shows h2 and empty space. DevTools Elements confirms #projects-grid, #panel-overlay, #project-detail-panel exist.

**Step 4 — Commit:**
```bash
git add index.html
git commit -m "feat: restructure projects HTML for grid + panel"
```

---

### Task 3: Generate project cards from JS

**Files:**
- Modify: index.html — add renderProjects() to script block, call it on DOMContentLoaded

**Step 1:** Add after the PROJECTS array in the script block:

```js
function renderProjects() {
  const grid = document.getElementById('projects-grid');
  if (!grid) return;
  const fragment = document.createDocumentFragment();
  PROJECTS.forEach(p => {
    const card = document.createElement('div');
    card.className = 'project-card';
    card.dataset.id = p.id;
    card.setAttribute('role', 'button');
    card.setAttribute('tabindex', '0');
    card.setAttribute('aria-label', 'View ' + p.title + ' details');

    // header row
    const header = document.createElement('div');
    header.className = 'project-card-header';
    const meta = document.createElement('div');
    meta.className = 'project-card-meta';
    meta.textContent = '\u{1F465} ' + p.team + '  \u{1F4C5} ' + p.timeline;
    header.appendChild(meta);
    if (p.private) {
      const badge = document.createElement('span');
      badge.className = 'card-private-badge';
      badge.textContent = '\uD83D\uDD12 Private';
      header.appendChild(badge);
    }

    // title
    const title = document.createElement('h3');
    title.textContent = p.title;

    // summary
    const summary = document.createElement('p');
    summary.className = 'project-card-summary';
    summary.textContent = p.summary;

    // tags (first 4)
    const tagContainer = document.createElement('div');
    tagContainer.className = 'skill-tags';
    p.tags.slice(0, 4).forEach(t => {
      const span = document.createElement('span');
      span.className = 'skill-tag';
      span.textContent = t;
      tagContainer.appendChild(span);
    });
    if (p.tags.length > 4) {
      const more = document.createElement('span');
      more.className = 'skill-tag skill-tag-more';
      more.textContent = '+' + (p.tags.length - 4);
      tagContainer.appendChild(more);
    }

    // glare layer for tilt
    const glare = document.createElement('div');
    glare.className = 'card-glare';

    card.appendChild(header);
    card.appendChild(title);
    card.appendChild(summary);
    card.appendChild(tagContainer);
    card.appendChild(glare);
    fragment.appendChild(card);
  });
  grid.appendChild(fragment);
}
```

Note: Using DOM methods instead of innerHTML to avoid any XSS risk, even though this is trusted static data.

**Step 2:** Find or create DOMContentLoaded listener, add renderProjects() as first call:

```js
document.addEventListener('DOMContentLoaded', () => {
  renderProjects();
  // ... existing init code (IntersectionObserver for sections, cursor glow, etc.)
});
```

**Step 3 — Visual check:** Reload. Projects section shows 7 cards stacked vertically (no grid CSS yet). Console: no errors.

**Step 4 — Commit:**
```bash
git add index.html
git commit -m "feat: render project cards from JS data"
```

---

### Task 4: CSS — grid layout + card styles

**Files:**
- Modify: global.css — append at end

**Step 1:** Append to global.css:

```css
/* Project Grid */
.projects-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 24px;
  margin-top: 16px;
}

.project-card {
  background: rgba(255, 255, 255, 0.04);
  border: 1px solid rgba(136, 146, 176, 0.15);
  border-radius: 12px;
  padding: 24px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
  transform-style: preserve-3d;
  will-change: transform;
}

.project-card:hover {
  border-color: rgba(136, 146, 176, 0.35);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.4);
}

.project-card:focus-visible {
  outline: 2px solid var(--glow-color);
  outline-offset: 2px;
}

.project-card-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 12px;
  gap: 8px;
}

.project-card-meta {
  font-size: 0.8rem;
  color: var(--text-color);
}

.card-private-badge {
  font-size: 0.75rem;
  color: var(--text-color);
  opacity: 0.55;
  white-space: nowrap;
}

.project-card h3 {
  font-size: 1.15rem;
  color: var(--highlight-color);
  margin-bottom: 10px;
}

.project-card-summary {
  font-size: 0.9rem;
  line-height: 1.5;
  color: var(--text-color);
  margin-bottom: 16px;
}

.skill-tag-more {
  opacity: 0.6;
}

.card-glare {
  position: absolute;
  inset: 0;
  border-radius: 12px;
  pointer-events: none;
  opacity: 0;
  transition: opacity 0.3s ease;
  z-index: 2;
}

.project-card:hover .card-glare {
  opacity: 1;
}

@media (max-width: 768px) {
  .projects-grid {
    grid-template-columns: 1fr;
  }
}
```

**Step 2 — Visual check:** 2-3 column grid on desktop, 1 column mobile. Cards have subtle border. Hover darkens border.

**Step 3 — Commit:**
```bash
git add global.css
git commit -m "feat: add project grid and card CSS"
```

---

### Task 5: CSS — side panel + overlay

**Files:**
- Modify: global.css — append at end

**Step 1:** Append to global.css:

```css
/* Project Detail Panel */
.panel-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.55);
  backdrop-filter: blur(4px);
  z-index: 100;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.35s ease;
}

.panel-overlay.active {
  opacity: 1;
  pointer-events: all;
}

.project-detail-panel {
  position: fixed;
  top: 0;
  right: 0;
  height: 100%;
  width: min(45vw, 680px);
  background: rgba(3, 22, 50, 0.97);
  backdrop-filter: blur(20px);
  border-left: 1px solid rgba(136, 146, 176, 0.2);
  z-index: 101;
  overflow-y: auto;
  transform: translateX(100%);
  transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  padding: 48px 40px 60px;
}

.project-detail-panel.active {
  transform: translateX(0);
}

.panel-close-btn {
  position: absolute;
  top: 20px;
  right: 24px;
  background: none;
  border: none;
  color: var(--text-color);
  font-size: 1.8rem;
  cursor: pointer;
  padding: 4px 8px;
  border-radius: 4px;
  line-height: 1;
  transition: color 0.2s ease, background 0.2s ease;
}

.panel-close-btn:hover {
  color: var(--highlight-color);
  background: rgba(255, 255, 255, 0.06);
}

.panel-inner {
  padding-top: 16px;
}

.panel-inner h3 {
  font-size: 1.6rem;
  color: var(--highlight-color);
  margin-bottom: 16px;
}

.panel-meta {
  display: flex;
  gap: 20px;
  font-size: 0.85rem;
  color: var(--text-color);
  margin-bottom: 24px;
}

.panel-description {
  font-size: 0.95rem;
  line-height: 1.8;
  color: var(--text-color);
  margin-bottom: 24px;
}

.panel-highlights {
  list-style: none;
  padding: 0;
  margin-bottom: 24px;
}

.panel-highlights li {
  position: relative;
  padding-left: 20px;
  margin-bottom: 10px;
  font-size: 0.9rem;
  line-height: 1.6;
  color: var(--text-color);
}

.panel-highlights li::before {
  content: '\25B9';
  position: absolute;
  left: 0;
  color: var(--glow-color);
}

.panel-tags {
  margin-bottom: 28px;
}

.panel-link {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  color: var(--highlight-color);
  text-decoration: none;
  font-size: 0.9rem;
  transition: color 0.2s ease;
}

.panel-link:hover {
  color: var(--glow-color);
}

@media (max-width: 768px) {
  .project-detail-panel {
    width: 100%;
    padding: 40px 24px 60px;
  }
}
```

**Step 2 — Visual check:** DevTools: toggle class active on #project-detail-panel and #panel-overlay. Panel slides in from right, overlay dims background.

**Step 3 — Commit:**
```bash
git add global.css
git commit -m "feat: add side panel and overlay CSS"
```

---

### Task 6: JS — panel open/close logic

**Files:**
- Modify: index.html — add to script block

**Step 1:** Add after renderProjects() in script block. Use DOM methods to populate panel (avoids innerHTML with any dynamic content):

```js
function buildPanelContent(project) {
  const inner = document.getElementById('panel-inner');
  inner.textContent = ''; // clear safely

  const title = document.createElement('h3');
  title.textContent = project.title;

  const meta = document.createElement('div');
  meta.className = 'panel-meta';
  const metaTeam = document.createElement('span');
  metaTeam.textContent = '\uD83D\uDC65 ' + project.team + (project.team === 1 ? ' person' : ' people');
  const metaDate = document.createElement('span');
  metaDate.textContent = '\uD83D\uDCC5 ' + project.timeline;
  meta.appendChild(metaTeam);
  meta.appendChild(metaDate);

  const desc = document.createElement('p');
  desc.className = 'panel-description';
  desc.textContent = project.description;

  const tags = document.createElement('div');
  tags.className = 'panel-tags skill-tags';
  project.tags.forEach(t => {
    const span = document.createElement('span');
    span.className = 'skill-tag';
    span.textContent = t;
    tags.appendChild(span);
  });

  inner.appendChild(title);
  inner.appendChild(meta);
  inner.appendChild(desc);

  if (project.highlights.length) {
    const ul = document.createElement('ul');
    ul.className = 'panel-highlights';
    project.highlights.forEach(h => {
      const li = document.createElement('li');
      li.textContent = h;
      ul.appendChild(li);
    });
    inner.appendChild(ul);
  }

  inner.appendChild(tags);

  // link
  const linkWrap = document.createElement('div');
  linkWrap.style.marginTop = '16px';
  if (project.link) {
    const a = document.createElement('a');
    a.href = project.link;
    a.className = 'panel-link';
    a.target = '_blank';
    a.rel = 'noopener noreferrer';
    a.textContent = project.linkLabel;
    linkWrap.appendChild(a);
  }
  if (project.private) {
    const priv = document.createElement('span');
    priv.textContent = '\uD83D\uDD12 Private Repository';
    priv.style.cssText = 'opacity:0.5;font-size:0.85rem;' + (project.link ? 'margin-left:16px;' : '');
    linkWrap.appendChild(priv);
  }
  inner.appendChild(linkWrap);
}

function openPanel(projectId) {
  const project = PROJECTS.find(p => p.id === projectId);
  if (!project) return;
  buildPanelContent(project);
  document.getElementById('project-detail-panel').classList.add('active');
  document.getElementById('panel-overlay').classList.add('active');
  document.body.style.overflow = 'hidden';
}

function closePanel() {
  document.getElementById('project-detail-panel').classList.remove('active');
  document.getElementById('panel-overlay').classList.remove('active');
  document.body.style.overflow = '';
}
```

**Step 2:** In DOMContentLoaded, after renderProjects():

```js
// Panel open
document.getElementById('projects-grid').addEventListener('click', e => {
  const card = e.target.closest('.project-card');
  if (card) openPanel(card.dataset.id);
});

document.getElementById('projects-grid').addEventListener('keydown', e => {
  if (e.key === 'Enter' || e.key === ' ') {
    const card = e.target.closest('.project-card');
    if (card) { e.preventDefault(); openPanel(card.dataset.id); }
  }
});

// Panel close
document.getElementById('panel-close-btn').addEventListener('click', closePanel);
document.getElementById('panel-overlay').addEventListener('click', closePanel);
document.addEventListener('keydown', e => { if (e.key === 'Escape') closePanel(); });
```

**Step 3 — Visual check:** Click any project card. Panel slides in from right with full project details. Close via x button, overlay click, or Escape key.

**Step 4 — Commit:**
```bash
git add index.html
git commit -m "feat: add project panel open/close logic"
```

---

### Task 7: JS — 3D tilt effect on cards

**Files:**
- Modify: index.html — add initTilt() to script block

**Step 1:** Add after panel functions:

```js
function initTilt() {
  document.querySelectorAll('.project-card').forEach(card => {
    const glare = card.querySelector('.card-glare');

    card.addEventListener('mousemove', e => {
      const rect = card.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;
      const rotateX = ((y - rect.height / 2) / (rect.height / 2)) * -10;
      const rotateY = ((x - rect.width / 2) / (rect.width / 2)) * 10;
      card.style.transform = `perspective(800px) rotateX(${rotateX}deg) rotateY(${rotateY}deg) scale3d(1.02,1.02,1.02)`;
      if (glare) {
        const gx = (x / rect.width) * 100;
        const gy = (y / rect.height) * 100;
        glare.style.background = `radial-gradient(circle at ${gx}% ${gy}%, rgba(255,255,255,0.1) 0%, transparent 60%)`;
        glare.style.opacity = '1';
      }
    });

    card.addEventListener('mouseleave', () => {
      card.style.transform = 'perspective(800px) rotateX(0deg) rotateY(0deg) scale3d(1,1,1)';
      if (glare) { glare.style.background = ''; glare.style.opacity = '0'; }
    });
  });
}
```

**Step 2:** In DOMContentLoaded, after renderProjects():

```js
initTilt();
```

**Step 3 — Visual check:** Hover over project cards slowly. Cards tilt in 3D with glare tracking cursor. Moving away resets to flat.

**Step 4 — Commit:**
```bash
git add index.html
git commit -m "feat: add 3D tilt effect to project cards"
```

---

### Task 8: JS + CSS — scramble text on h2 headings

**Files:**
- Modify: global.css — remove animation from h2
- Modify: index.html — add initScramble() to script block

**Step 1:** In global.css, find the h2 rule and remove the animation line:

```css
/* BEFORE */
h2 {
    font-size: 2.5rem;
    margin-bottom: 1.5rem;
    animation: glow 4s infinite;
}

/* AFTER */
h2 {
    font-size: 2.5rem;
    margin-bottom: 1.5rem;
}
```

(Leave the @keyframes glow block — h1 still uses it.)

**Step 2:** Add in script block:

```js
function scrambleText(el) {
  const original = el.dataset.originalText || el.textContent.trim();
  el.dataset.originalText = original;
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789@#$%&';
  const totalFrames = 20;
  let frame = 0;
  const tick = setInterval(() => {
    el.textContent = original.split('').map((char, i) => {
      if (char === ' ') return ' ';
      if (frame >= (i / original.length) * totalFrames) return char;
      return chars[Math.floor(Math.random() * chars.length)];
    }).join('');
    frame++;
    if (frame > totalFrames) {
      el.textContent = original;
      clearInterval(tick);
    }
  }, 30);
}

function initScramble() {
  const headings = document.querySelectorAll('.section h2');
  const observer = new IntersectionObserver(entries => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        scrambleText(entry.target);
        observer.unobserve(entry.target);
      }
    });
  }, { threshold: 0.3 });
  headings.forEach(h => observer.observe(h));
}
```

**Step 3:** In DOMContentLoaded:

```js
initScramble();
```

**Step 4 — Visual check:** Reload. Scroll to "About Me" — heading scrambles chars and resolves left-to-right. Same for Experience, Projects, Let's Connect. Each fires once.

**Step 5 — Commit:**
```bash
git add index.html global.css
git commit -m "feat: add scramble text effect to section headings"
```

---

## Done

Portfolio now has:
- Scramble text on all h2 section headings (once on scroll-in)
- Project section as responsive CSS grid of tilt cards
- Slide-in side panel with full project details on card click
