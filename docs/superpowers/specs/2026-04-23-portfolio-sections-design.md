# Portfolio sections — design

**Date:** 2026-04-23
**Status:** Approved, ready for implementation plan
**Scope:** Extend the existing single-page personal site (`index.html`) to include a projects grid, a writing section, and a dedicated socials section. Keep the current dark/indigo aesthetic and the zero-build deployment on GitHub Pages.

## Goals

- Showcase side projects (currently zero visibility) in a scannable grid on the landing page.
- Surface selected Medium posts so visitors can see writing without leaving the site.
- Keep the change lightweight — no new frameworks, no build step, no asset pipeline.

## Non-goals

- No per-project detail pages or case studies. Cards link out to GitHub / live demos.
- No CMS, no JSON data file, no automatic Medium feed. Content is hand-edited in HTML.
- No skills section, no work-history timeline, no sticky navigation bar.

## Architecture

- **Single file:** `index.html` remains the only HTML file. No routing, no new pages.
- **Inline CSS:** the existing `<style>` block is extended with new rules. No external stylesheet.
- **Hardcoded content:** project cards and writing cards are written directly into the HTML. Adding a project = editing the file and committing.
- **Palette preserved:** the existing CSS custom properties (`--primary`, `--primary-dark`, `--secondary`, `--accent`, `--bg-dark`, `--bg-darker`, `--text-light`, `--text-dim`, `--card-bg`) are reused everywhere.
- **Profile photo (`profile-photo.jpg`) unchanged.**

## Page structure

Top-to-bottom, the page becomes:

1. **Hero** (`#home`, existing, lightly revised) — photo, name, bio paragraph, two CTA buttons.
2. **Projects** (`#projects`, new) — card grid of side projects.
3. **Writing** (`#writing`, new) — card grid of Medium posts + "Read more on Medium" link.
4. **Socials** (`#socials`, new) — tagline + social icons row.
5. **Footer** (existing) — unchanged.

No sticky nav bar. Hero CTAs serve as primary navigation. Each new section uses the existing `.fade-in` class so it animates in on scroll via the existing `IntersectionObserver`.

## Hero — revisions

- **Keep:** profile photo (`.hero-profile`), `<h1>Hello, I'm Steph.</h1>`, bio paragraph (`.hero-about`), fadeInUp animation.
- **Remove:** the `.social-links` block currently inside `.hero-content`. Socials move to the dedicated section.
- **Add:** two CTA buttons using existing `.btn` classes.

```html
<div class="hero-buttons">
  <a href="#projects" class="btn btn-primary">View projects</a>
  <a href="#socials" class="btn btn-outline">Get in touch</a>
</div>
```

No new CSS — `.btn`, `.btn-primary`, `.btn-outline`, `.hero-buttons` are already defined.

## Projects section

### Markup

```html
<section id="projects" class="fade-in">
  <h2 class="section-title">Projects</h2>
  <div class="projects-grid">
    <!-- project cards -->
  </div>
</section>
```

### Card markup

```html
<article class="project-card">
  <div class="project-icon">🔐</div>
  <h3 class="project-title">agentdid</h3>
  <p class="project-description">Cryptographic identity for AI agents. Ed25519 + W3C DIDs. Prove a human stands behind the bot.</p>
  <div class="project-tags">
    <span class="tag">Python</span>
    <span class="tag">Cryptography</span>
    <span class="tag">PyPI</span>
  </div>
  <div class="project-links">
    <a href="https://github.com/Mr-Perfection/agentdid" target="_blank" rel="noopener">GitHub →</a>
    <a href="https://pypi.org/project/agentdid/" target="_blank" rel="noopener">PyPI →</a>
  </div>
</article>
```

### Grid behavior

Reuses the existing `.projects-grid` rule: `grid-template-columns: repeat(auto-fit, minmax(300px, 1fr))`, gap 2rem. 3-up on wide desktop, 2-up mid, 1-up mobile.

### New CSS

- `.project-icon` — 48×48 rounded tile (`border-radius: 12px`), `background: linear-gradient(135deg, var(--primary), var(--accent))`, flex-center content, `font-size: 1.5rem`, `margin-bottom: 1rem`.
- `.project-tags` — flex row, `gap: .4rem`, `flex-wrap: wrap`, `margin-bottom: 1rem`.
- `.tag` — `font-size: .72rem`, `padding: .2rem .55rem`, `border: 1px solid rgba(99, 102, 241, .35)`, `color: var(--secondary)`, `border-radius: 999px`.

Existing `.project-card`, `.project-title`, `.project-description`, `.project-link` rules are reused without modification.

### Initial content

| Icon | Title | Description | Tags | Links |
|---|---|---|---|---|
| 🔐 | agentdid | Cryptographic identity for AI agents. Ed25519 + W3C DIDs. Prove a human stands behind the bot. | Python · Cryptography · PyPI | [GitHub](https://github.com/Mr-Perfection/agentdid), [PyPI](https://pypi.org/project/agentdid/) |
| 🛒 | Agentlyze | Free diagnostic tool that scores how ready an e-commerce site is for AI shopping agents (ChatGPT, Perplexity). | Next.js · AI Agents · SEO | [Live demo](https://agent-ready-score.vercel.app/) |

No placeholder "coming soon" tiles. The grid grows as new real cards are added.

## Writing section

### Markup

```html
<section id="writing" class="fade-in">
  <h2 class="section-title">Writing</h2>
  <div class="writing-grid">
    <!-- writing cards -->
  </div>
  <p class="writing-more">
    <a href="https://medium.com/@stephenslee" target="_blank" rel="noopener">Read more on Medium →</a>
  </p>
</section>
```

### Card markup

The entire card is one `<a>` — simpler than project cards because the only action is "read the post."

```html
<a href="..." target="_blank" rel="noopener" class="writing-card">
  <span class="writing-label">TOWARDS AI</span>
  <h3 class="writing-title">OpenClaw Post-Onboarding: Skills, Apple Integrations, and Google Calendar — The Secure Way</h3>
  <p class="writing-excerpt">Configuring an AI agent with security-focused skills and integrations while maintaining privilege separation between admin and standard user accounts on macOS.</p>
  <span class="writing-date">Feb 2026</span>
</a>
```

### New CSS

- `.writing-grid` — same shape as `.projects-grid`: `grid-template-columns: repeat(auto-fit, minmax(300px, 1fr))`, gap 2rem.
- `.writing-card` — `display: block`, `text-decoration: none`, `color: inherit`, `background: var(--card-bg)`, `border: 1px solid rgba(99, 102, 241, 0.2)`, `border-radius: 15px`, `padding: 2rem`, `transition: all 0.3s ease`, `position: relative`, `overflow: hidden`. (Same visual base as `.project-card` but applied directly; the whole card is the anchor.)
- `.writing-card:hover` — `transform: translateY(-5px)`, `box-shadow: 0 10px 30px rgba(99, 102, 241, 0.2)`. Matches `.project-card:hover`.
- `.writing-card::before` — same top gradient bar as `.project-card::before` (scales on hover). Reuse the identical rule.
- `.writing-label` — `font-size: .7rem`, `text-transform: uppercase`, `letter-spacing: .08em`, `color: var(--accent)`, `display: block`, `margin-bottom: .75rem`.
- `.writing-title` — `font-size: 1.15rem`, `color: var(--text-light)`, `margin-bottom: .75rem`, `line-height: 1.4`.
- `.writing-excerpt` — `color: var(--text-dim)`, `font-size: .95rem`, `line-height: 1.6`, `margin-bottom: 1rem`.
- `.writing-date` — `font-size: .8rem`, `color: var(--text-dim)`, `display: block`.
- `.writing-more` — `text-align: center`, `margin-top: 2rem`. The inner `<a>` uses `color: var(--primary)` and transitions to `var(--accent)` on hover (matches `.project-link`).

### Initial content

Ordered newest first.

| Label | Title | Excerpt | Date | URL |
|---|---|---|---|---|
| TOWARDS AI | OpenClaw Post-Onboarding: Skills, Apple Integrations, and Google Calendar — The Secure Way | Configuring an AI agent with security-focused skills and integrations while maintaining privilege separation between admin and standard user accounts on macOS. | Feb 2026 | https://medium.com/towards-artificial-intelligence/openclaw-post-onboarding-skills-apple-integrations-and-google-calendar-the-secure-way-4b4b4e49dfa8?sk=3e0ba8f2273e6f6f671b62f76e11734a |
| MEDIUM | Build your own personal voice AI assistant like Alexa with OpenAI GPT-3, Whisper, and Coqui TTS in 5 minutes | Combining GPT-3, Whisper, and Coqui TTS into a working voice assistant in Python. | Mar 2023 | https://medium.com/@stephenslee/build-your-own-personal-voice-ai-assistant-like-alexa-with-openai-gpt3-openai-whisper-and-coqui-6fe4468828e5?sk=5948fe45d41c8f714681f6ca11736f29 |

## Socials section

### Markup

```html
<section id="socials" class="fade-in">
  <h2 class="section-title">Let's connect</h2>
  <p class="socials-tagline">Find me at tech events, tennis courts, or in the middle of the ocean.</p>
  <div class="social-links">
    <!-- same 5 links as currently in hero: GitHub, Medium, LinkedIn, X, Kaggle -->
  </div>
</section>
```

### New CSS

- `.socials-tagline` — `text-align: center`, `color: var(--text-dim)`, `max-width: 500px`, `margin: 0 auto 2rem`.

Existing `.social-links`, `.social-link`, and the `.social-link:hover` rule (including the rotate-360 effect) are reused without modification. The 5 links (GitHub, Medium, LinkedIn, X, Kaggle) move from the hero to this section unchanged.

## JavaScript changes

The existing `<script>` block at the bottom of `index.html` needs two small updates:

1. **Remove the navbar scroll listener.** The block `window.addEventListener('scroll', ...)` references `document.getElementById('navbar')`, which returns `null` because the `<nav>` is commented out. This currently throws a `TypeError` on every scroll. Delete this listener entirely — there is no nav bar in the design.
2. **Keep everything else:** the smooth-scroll anchor handler (used by the new CTAs), the `IntersectionObserver` for `.fade-in` (used by the new sections), and the `.skill-card` hover handlers (orphaned — safe to delete since no skill cards are rendered, but harmless to leave).

Recommendation: delete the orphaned skill-card handler too, since there is no skills section.

## Testing and verification

No automated tests — this is a single static HTML file. Manual verification checklist:

- Open `index.html` in a browser (double-click or `open index.html`). Load with cache disabled.
- Hero CTAs smooth-scroll to `#projects` and `#socials` respectively.
- All external links (`target="_blank"`) open in a new tab.
- Project cards show hover lift and top-border gradient reveal.
- Writing cards show hover lift (same treatment as project cards).
- Social icons show hover rotate + color swap.
- Grid reflows cleanly at ~1200px, ~768px, and ~400px widths. Resize the window to check.
- Fade-in animation triggers on each new section as it scrolls into view.
- Browser DevTools console shows no errors (should confirm the navbar-listener fix).

## Delivery

- One commit to `main` with the updated `index.html`.
- No other file changes. `.gitignore` was already updated in a prior step to ignore `.superpowers/`.
- GitHub Pages auto-deploys from `main` — no manual deploy step.

## Out of scope (explicitly deferred)

- Project detail pages / case studies.
- Screenshots or image assets for project cards.
- Automated post fetching from Medium RSS.
- Sticky navigation bar.
- Skills / experience sections.
- Dark/light theme toggle.
- Any build system (bundler, minifier, framework).
