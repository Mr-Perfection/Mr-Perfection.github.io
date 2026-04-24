# Portfolio Sections Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend the existing single-page personal site (`index.html`) with Projects, Writing, and Socials sections while preserving the current dark/indigo aesthetic and zero-build GitHub Pages deployment.

**Architecture:** Single static HTML file on GitHub Pages. All CSS stays inline. All content hardcoded in HTML — no build step, no JSON data file, no JS data fetch. Each task leaves the page in a working state (loads without JS errors, all visible links work).

**Tech Stack:** Hand-written HTML + CSS + a tiny vanilla JS block (smooth scroll + IntersectionObserver fade-in). No framework, no bundler, no test runner.

**Spec:** `docs/superpowers/specs/2026-04-23-portfolio-sections-design.md`

**Verification approach:** No automated tests (per spec — a test framework would be more machinery than the change warrants). Each task has an explicit manual verification step with concrete things to check in the browser and in DevTools console.

---

## File Structure

Only one file is modified:

- **Modify:** `index.html` — sole HTML file. All markup, CSS (inside `<style>`), and JS (inside `<script>`) live here.

Directories already present from earlier brainstorming: `docs/superpowers/specs/`, `docs/superpowers/plans/`. `.gitignore` already includes `.superpowers/`. No new files created by this plan.

Task ordering is chosen so every commit leaves the page in a working state:

1. Clean up broken JS (the current `navbar` scroll listener throws on every scroll).
2. Update hero + add new Socials section in the same commit (socials move from hero to a dedicated section without being briefly absent).
3. Add Projects section.
4. Add Writing section.
5. Final manual verification pass.

---

## Task 1: Remove broken JS (navbar listener + orphaned skill-card hover)

The current `<script>` block has two dead pieces of code:
- A `scroll` listener that does `navbar.classList.add(...)` where `navbar` is `null` (the `<nav>` is commented out) — this throws a `TypeError` on every scroll.
- A `.skill-card` hover handler — there are no `.skill-card` elements on the page; the querySelectorAll just returns empty and the loop is a no-op.

**Files:**
- Modify: `index.html` — the `<script>` block currently at lines 576–628.

- [ ] **Step 1: Confirm the current error**

Open `index.html` in a browser (double-click or `open index.html` on macOS), open DevTools Console (Cmd+Opt+I on macOS), then scroll the page.

Expected: a red `TypeError: Cannot read properties of null (reading 'classList')` appears in the console on every scroll event.

This confirms the bug we're about to fix.

- [ ] **Step 2: Delete the navbar scroll listener**

In `index.html`, find the block that starts with `// Navbar scroll effect` and remove the entire listener.

Delete these lines (currently lines 577–585):

```javascript
        // Navbar scroll effect
        window.addEventListener('scroll', function() {
            const navbar = document.getElementById('navbar');
            if (window.scrollY > 50) {
                navbar.classList.add('scrolled');
            } else {
                navbar.classList.remove('scrolled');
            }
        });

```

Leave the rest of the `<script>` block intact.

- [ ] **Step 3: Delete the orphaned skill-card hover handler**

In `index.html`, find the block that starts with `// Add some interactive hover effects` and remove the entire loop.

Delete these lines (currently lines 619–627):

```javascript
        // Add some interactive hover effects
        document.querySelectorAll('.skill-card').forEach(card => {
            card.addEventListener('mouseenter', function() {
                this.style.transform = 'translateY(-5px) scale(1.05)';
            });
            card.addEventListener('mouseleave', function() {
                this.style.transform = 'translateY(0) scale(1)';
            });
        });
```

After the deletions, the `<script>` block should contain only two sections: the smooth-scroll anchor handler and the IntersectionObserver fade-in setup.

- [ ] **Step 4: Verify the fix**

Reload `index.html` in the browser (Cmd+R). Open DevTools Console and scroll the page.

Expected: no errors in the console. The page looks and behaves identically to before (the deleted code had no visible effect).

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "Remove dead JS: broken navbar listener and orphaned skill-card handler"
```

---

## Task 2: Update hero (add CTAs, move socials to new Socials section)

This task does two coupled changes in one commit so the page never loses its socials:

- Remove the `.social-links` block (wrapped in `.contact-content`) from inside the hero.
- Add two CTA buttons to the hero.
- Create a new `#socials` section below the hero (before the footer) containing the tagline and the moved social links.
- Add one new CSS rule: `.socials-tagline`.

**Files:**
- Modify: `index.html` — hero section (currently lines 503–542), add new `#socials` section before the `<footer>` (currently line 572), add `.socials-tagline` CSS rule inside the existing `<style>` block (add near the other `.social-*` rules, around line 418).

- [ ] **Step 1: Add the `.socials-tagline` CSS rule**

In `index.html`, find the `.social-links` CSS rule inside the `<style>` block (currently starts around line 413). Immediately before it, insert a new rule for the tagline used in the new Socials section.

Insert this block before the `.social-links` rule:

```css
        .socials-tagline {
            text-align: center;
            color: var(--text-dim);
            max-width: 500px;
            margin: 0 auto 2rem;
            line-height: 1.6;
        }

```

The existing `.social-links`, `.social-link`, `.social-link svg`, and `.social-link:hover` rules stay exactly as they are — we're reusing them for the new section.

- [ ] **Step 2: Replace the social-links block in the hero with CTA buttons**

In `index.html`, find the hero `<section>` (currently lines 503–542). Locate the `<div class="contact-content">` that wraps the `<div class="social-links">` block (lines 516–540).

Replace that entire `<div class="contact-content">...</div>` block with:

```html
            <div class="hero-buttons">
                <a href="#projects" class="btn btn-primary">View projects</a>
                <a href="#socials" class="btn btn-outline">Get in touch</a>
            </div>
```

After this edit, the hero section should look like:

```html
    <!-- Hero Section -->
    <section id="home" class="hero">
        <div class="hero-content">
            <div class="profile-img hero-profile">
                <img src="profile-photo.jpg" alt="Stephen S. Lee">
            </div>
            <h1>Hello, I'm Steph.</h1>
            <p class="hero-about">
            I'm working on the foundation models team at Amazon, building Rufus AI. 
            I developed distributed training infrastructure and frameworks to train LLMs with thousands of GPUs. 
            Before that, I was at various companies working on full-stack development and distributed systems. 
            Outside of work, you can find me at tech events, tennis courts, or in the middle of the ocean (probably surfing or spearfishing).</p>

            <div class="hero-buttons">
                <a href="#projects" class="btn btn-primary">View projects</a>
                <a href="#socials" class="btn btn-outline">Get in touch</a>
            </div>
        </div>
    </section>
```

Existing `.hero-buttons` CSS (around line 192) already defines `display: flex; gap: 1.5rem; justify-content: center; flex-wrap: wrap;` — no CSS change needed for the button row itself. Existing `.btn`, `.btn-primary`, `.btn-outline` rules are also already defined.

- [ ] **Step 3: Add the new Socials section before the footer**

In `index.html`, find the `<!-- Footer -->` comment and the `<footer>` tag that follows it (currently starting line 571). Immediately before that `<!-- Footer -->` comment, insert the new Socials section.

Insert this block:

```html
    <!-- Socials Section -->
    <section id="socials" class="fade-in">
        <h2 class="section-title">Let's connect</h2>
        <p class="socials-tagline">Find me at tech events, tennis courts, or in the middle of the ocean.</p>
        <div class="social-links">
            <a href="https://github.com/Mr-Perfection" class="social-link" target="_blank" rel="noopener" title="GitHub">
                <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
                </svg>
            </a>
            <a href="https://medium.com/@stephenslee" class="social-link" target="_blank" rel="noopener" title="Medium">
                <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path d="M13.54 12a6.8 6.8 0 01-6.77 6.82A6.8 6.8 0 010 12a6.8 6.8 0 016.77-6.82A6.8 6.8 0 0113.54 12zM20.96 12c0 3.54-1.51 6.42-3.38 6.42-1.87 0-3.39-2.88-3.39-6.42s1.52-6.42 3.39-6.42 3.38 2.88 3.38 6.42M24 12c0 3.17-.53 5.75-1.19 5.75-.66 0-1.19-2.58-1.19-5.75s.53-5.75 1.19-5.75C23.47 6.25 24 8.83 24 12z"/>
                </svg>
            </a>
            <a href="https://www.linkedin.com/in/stephensungsoolee/" class="social-link" target="_blank" rel="noopener" title="LinkedIn">
                <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"/>
                </svg>
            </a>
            <a href="https://x.com/sungsoost" class="social-link" target="_blank" rel="noopener" title="X">
                <span>🐦</span>
            </a>
            <a href="https://www.kaggle.com/stephenslee" class="social-link" target="_blank" rel="noopener" title="Kaggle">
                <span>Kaggle</span>
            </a>
        </div>
    </section>

```

Note: the URLs and icon markup are copied directly from the existing in-hero block, plus `rel="noopener"` added to each link for security (recommended with `target="_blank"`).

- [ ] **Step 4: Also delete the large commented-out contact section**

While we're here, remove the stale commented-out `<!-- Contact Section -->` block (currently lines 544–569) — it's a duplicate of the old hero socials and now replaced by the new `#socials` section. Keeping it around is noise.

Delete the entire block from `<!-- Contact Section -->` through `</section> -->` (the `-->` that closes it).

- [ ] **Step 5: Manual verification**

Reload `index.html` in the browser. Check:

- Hero shows the photo, name, bio, and two buttons ("View projects" and "Get in touch") — no social icons in the hero.
- Clicking "View projects" → nothing scrolls (target `#projects` doesn't exist yet — this is expected; the anchor click handler does nothing when target is missing, by design).
- Clicking "Get in touch" → smooth-scrolls down to the new Socials section.
- Socials section shows the "Let's connect" heading, the tagline ("Find me at tech events..."), and the 5 social icons (GitHub, Medium, LinkedIn, X, Kaggle).
- Hover on each social icon still does the rotate-360 + color swap.
- Resize window to ~400px wide — socials section still readable, icons wrap if needed.
- DevTools Console: no errors.

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "Move socials from hero into dedicated section, add hero CTA buttons"
```

---

## Task 3: Add Projects section

Adds a new `#projects` section between the hero and the socials section, with two initial cards (agentdid and Agentlyze) and supporting CSS for project icons and tags.

**Files:**
- Modify: `index.html` — add new CSS rules inside `<style>` block (insert near existing `.project-*` rules, around line 338), insert new `<section id="projects">` between hero and socials (hero closes around line 542, socials starts just before footer from Task 2).

- [ ] **Step 1: Add the new CSS rules for project icon and tags**

In `index.html`, find the existing `.project-description` rule inside the `<style>` block (currently around line 382). Immediately after the `.project-description` rule and before the `.project-links` rule, insert three new rules:

```css
        .project-icon {
            width: 48px;
            height: 48px;
            border-radius: 12px;
            background: linear-gradient(135deg, var(--primary), var(--accent));
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.5rem;
            margin-bottom: 1rem;
        }

        .project-tags {
            display: flex;
            flex-wrap: wrap;
            gap: 0.4rem;
            margin-bottom: 1rem;
        }

        .tag {
            font-size: 0.72rem;
            padding: 0.2rem 0.55rem;
            border: 1px solid rgba(99, 102, 241, 0.35);
            color: var(--secondary);
            border-radius: 999px;
        }

```

The existing `.project-card`, `.project-card::before`, `.project-card:hover`, `.project-title`, `.project-description`, `.project-links`, `.project-link`, `.project-link:hover`, and `.projects-grid` rules stay as they are.

- [ ] **Step 2: Add the Projects section markup**

In `index.html`, find the closing `</section>` of the hero (currently around line 542). Immediately after that closing tag, insert the new Projects section.

Insert this block:

```html

    <!-- Projects Section -->
    <section id="projects" class="fade-in">
        <h2 class="section-title">Projects</h2>
        <div class="projects-grid">
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
                    <a class="project-link" href="https://github.com/Mr-Perfection/agentdid" target="_blank" rel="noopener">GitHub →</a>
                    <a class="project-link" href="https://pypi.org/project/agentdid/" target="_blank" rel="noopener">PyPI →</a>
                </div>
            </article>

            <article class="project-card">
                <div class="project-icon">🛒</div>
                <h3 class="project-title">Agentlyze</h3>
                <p class="project-description">Free diagnostic tool that scores how ready an e-commerce site is for AI shopping agents (ChatGPT, Perplexity).</p>
                <div class="project-tags">
                    <span class="tag">Next.js</span>
                    <span class="tag">AI Agents</span>
                    <span class="tag">SEO</span>
                </div>
                <div class="project-links">
                    <a class="project-link" href="https://agent-ready-score.vercel.app/" target="_blank" rel="noopener">Live demo →</a>
                </div>
            </article>
        </div>
    </section>
```

- [ ] **Step 3: Manual verification**

Reload `index.html` in the browser. Check:

- Below the hero, a "Projects" section heading appears, followed by two cards in a grid.
- Each card shows: gradient icon tile at top-left (🔐 / 🛒), project title, description, three tag pills, and one or two link arrows at the bottom.
- Hover over a card: card lifts slightly and a gradient bar appears across the top (existing `.project-card::before` animation).
- Hover over a tag: no change (tags don't have a hover rule, as intended — they're passive).
- Click the hero "View projects" button: smooth-scrolls to the projects grid.
- Scroll so the Projects section enters the viewport: it fades in (`.fade-in.visible` applied by the IntersectionObserver).
- Resize window to ~600px: grid becomes 1-up with cards stacked.
- DevTools Console: no errors. External links have `target="_blank" rel="noopener"`.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Add Projects section with agentdid and Agentlyze cards"
```

---

## Task 4: Add Writing section

Adds a new `#writing` section between the Projects and Socials sections, with two Medium post cards and a "Read more on Medium" link.

**Files:**
- Modify: `index.html` — add new CSS rules inside `<style>` block (insert after the new `.tag` rule from Task 3), insert new `<section id="writing">` between Projects and Socials sections.

- [ ] **Step 1: Add the new CSS rules for writing cards**

In `index.html`, find the `.tag` rule added in Task 3. Immediately after the `.tag` rule (and before `.projects-grid` or whichever rule currently follows), insert the writing-section rules:

```css
        .writing-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 2rem;
        }

        .writing-card {
            display: block;
            text-decoration: none;
            color: inherit;
            background: var(--card-bg);
            border: 1px solid rgba(99, 102, 241, 0.2);
            border-radius: 15px;
            padding: 2rem;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .writing-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 3px;
            background: linear-gradient(90deg, var(--primary) 0%, var(--accent) 100%);
            transform: scaleX(0);
            transition: transform 0.3s ease;
        }

        .writing-card:hover::before {
            transform: scaleX(1);
        }

        .writing-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 30px rgba(99, 102, 241, 0.2);
        }

        .writing-label {
            display: block;
            font-size: 0.7rem;
            text-transform: uppercase;
            letter-spacing: 0.08em;
            color: var(--accent);
            margin-bottom: 0.75rem;
        }

        .writing-title {
            font-size: 1.15rem;
            color: var(--text-light);
            margin-bottom: 0.75rem;
            line-height: 1.4;
        }

        .writing-excerpt {
            color: var(--text-dim);
            font-size: 0.95rem;
            line-height: 1.6;
            margin-bottom: 1rem;
        }

        .writing-date {
            display: block;
            font-size: 0.8rem;
            color: var(--text-dim);
        }

        .writing-more {
            text-align: center;
            margin-top: 2rem;
        }

        .writing-more a {
            color: var(--primary);
            text-decoration: none;
            transition: color 0.3s ease;
        }

        .writing-more a:hover {
            color: var(--accent);
        }

```

These mirror `.project-card`'s visual language (background, border, radius, hover lift, top gradient bar) but apply them directly to the `<a>` element — the whole card is one link.

- [ ] **Step 2: Add the Writing section markup**

In `index.html`, find the closing `</section>` of the Projects section (the one you added in Task 3). Immediately after that closing tag, insert the new Writing section.

Insert this block:

```html

    <!-- Writing Section -->
    <section id="writing" class="fade-in">
        <h2 class="section-title">Writing</h2>
        <div class="writing-grid">
            <a class="writing-card" href="https://medium.com/towards-artificial-intelligence/openclaw-post-onboarding-skills-apple-integrations-and-google-calendar-the-secure-way-4b4b4e49dfa8?sk=3e0ba8f2273e6f6f671b62f76e11734a" target="_blank" rel="noopener">
                <span class="writing-label">Towards AI</span>
                <h3 class="writing-title">OpenClaw Post-Onboarding: Skills, Apple Integrations, and Google Calendar — The Secure Way</h3>
                <p class="writing-excerpt">Configuring an AI agent with security-focused skills and integrations while maintaining privilege separation between admin and standard user accounts on macOS.</p>
                <span class="writing-date">Feb 2026</span>
            </a>

            <a class="writing-card" href="https://medium.com/@stephenslee/build-your-own-personal-voice-ai-assistant-like-alexa-with-openai-gpt3-openai-whisper-and-coqui-6fe4468828e5?sk=5948fe45d41c8f714681f6ca11736f29" target="_blank" rel="noopener">
                <span class="writing-label">Medium</span>
                <h3 class="writing-title">Build your own personal voice AI assistant like Alexa with OpenAI GPT-3, Whisper, and Coqui TTS in 5 minutes</h3>
                <p class="writing-excerpt">Combining GPT-3, Whisper, and Coqui TTS into a working voice assistant in Python.</p>
                <span class="writing-date">Mar 2023</span>
            </a>
        </div>
        <p class="writing-more">
            <a href="https://medium.com/@stephenslee" target="_blank" rel="noopener">Read more on Medium →</a>
        </p>
    </section>
```

- [ ] **Step 3: Manual verification**

Reload `index.html` in the browser. Check:

- Below the Projects section, a "Writing" heading appears, followed by two cards side-by-side (or stacked on narrow viewports).
- Each card shows: small uppercase accent-colored label (TOWARDS AI / MEDIUM), full post title, one-sentence excerpt, publication date.
- The OpenClaw card is on the left (first / newer), Voice Assistant card on the right (second / older).
- Hover over a card: card lifts, top gradient bar animates in — same treatment as project cards.
- Click a card: opens the Medium post in a new tab.
- Below the grid, a centered "Read more on Medium →" link. Hover changes its color from primary to accent. Click opens your Medium author page in a new tab.
- Resize to ~600px: cards stack to 1-up. "Read more" stays centered.
- DevTools Console: no errors.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Add Writing section with two Medium posts"
```

---

## Task 5: Full manual verification pass

Final check that all pieces work together, navigation flows are clean, and nothing regressed.

**Files:** none modified — verification only.

- [ ] **Step 1: Full-page walkthrough**

Reload `index.html` in the browser (Cmd+Shift+R for hard reload). Walk through the page top-to-bottom and verify all of the following:

**Hero:**
- Profile photo renders, has the gradient border glow
- Name "Hello, I'm Steph." renders with gradient text
- Bio paragraph present and readable
- Two buttons: "View projects" (primary, filled) and "Get in touch" (outline)

**Navigation (CTA behavior):**
- Click "View projects" → smooth-scrolls to Projects section
- Click "Get in touch" → smooth-scrolls to Socials section

**Projects section:**
- Heading "Projects" renders centered with gradient text
- Two cards visible: agentdid (🔐) and Agentlyze (🛒)
- Each card shows icon, title, description, tags, links
- Hover on each card: lift + gradient top bar animates in

**Writing section:**
- Heading "Writing" renders
- Two cards: OpenClaw (Towards AI, Feb 2026) then Voice Assistant (Medium, Mar 2023)
- Hover on each card: same lift animation
- Click each card opens the correct Medium URL in a new tab
- "Read more on Medium →" link below, centered

**Socials section:**
- Heading "Let's connect" renders
- Tagline: "Find me at tech events, tennis courts, or in the middle of the ocean."
- Five social icons: GitHub, Medium, LinkedIn, X (🐦), Kaggle (text)
- Hover on each icon: rotate-360 + background/color swap
- Each icon opens the correct external URL in a new tab

**Footer:**
- © 2025 Stephen S. Lee copyright line renders

**Fade-in animation:**
- Scroll from top to bottom slowly. Each of `#projects`, `#writing`, `#socials` should fade-in (opacity 0 → 1, translateY 30px → 0) as it crosses ~90% of the viewport.

**DevTools console:**
- Open DevTools (Cmd+Opt+I), click Console. Reload the page. No red errors should appear.
- Scroll the page from top to bottom. No errors should appear during scroll either (confirms Task 1's fix).

- [ ] **Step 2: Responsive check**

Use DevTools device toolbar (Cmd+Shift+M in Chrome). Test three widths:

- **1200px (desktop):** Projects and Writing grids both 2-up (or 3-up if the viewport is wide enough — depends on the user's actual screen). Hero buttons side-by-side.
- **768px (tablet):** Grids reflow. Layout still reads cleanly.
- **400px (mobile):** Grids are 1-up (single column). Hero buttons stack vertically (existing `.hero-buttons` mobile rule). Social icons row wraps if needed. No horizontal scrollbar at the bottom of the viewport.

- [ ] **Step 3: Link sanity check**

Click each external link on the page (open in new tabs so you don't lose your place):

- Projects: GitHub link → github.com/Mr-Perfection/agentdid; PyPI link → pypi.org/project/agentdid/; Live demo link → agent-ready-score.vercel.app/
- Writing: OpenClaw card → Medium Towards AI article; Voice Assistant card → Medium article; "Read more" → medium.com/@stephenslee
- Socials: GitHub, Medium, LinkedIn, X, Kaggle — all 5 open their respective profiles

If any link 404s or goes to the wrong place, fix it in `index.html` and re-verify.

- [ ] **Step 4: Check against the spec**

Open `docs/superpowers/specs/2026-04-23-portfolio-sections-design.md` and skim every section heading. For each item, confirm it's present on the page:

- Hero revisions: socials removed from hero ✓, CTA buttons added ✓
- Projects section: markup ✓, grid behavior ✓, new CSS rules ✓, both initial cards ✓
- Writing section: markup ✓, new CSS rules ✓, both initial cards ✓, "Read more" link ✓
- Socials section: markup ✓, tagline ✓, 5 icons ✓
- JS cleanup: navbar listener gone ✓, skill-card loop gone ✓
- All sections have `class="fade-in"` ✓

If anything is missing, go back and add it before committing.

- [ ] **Step 5: Deploy (push to main)**

GitHub Pages auto-deploys from `main`. Since tasks have been committing to `main` directly, the only step is pushing:

```bash
git push origin main
```

Wait ~30-60 seconds for GitHub Pages to rebuild. Then open the live URL (e.g. `https://mr-perfection.github.io/`) in an incognito window and repeat Step 1's walkthrough to confirm the deployed version matches local.

---

## Self-Review Results

**Spec coverage check — every section of the spec maps to a task:**

- Architecture (single file, inline CSS, hardcoded content, palette preserved) → baseline of all tasks
- Page structure (Hero → Projects → Writing → Socials → Footer) → Tasks 2, 3, 4 (sections added in page order, and Task 5 verifies final order)
- Hero revisions (remove socials, add CTAs) → Task 2 Steps 2, 4
- Projects section (markup, grid, card structure, new CSS, initial content) → Task 3
- Writing section (markup, card structure, new CSS, initial content) → Task 4
- Socials section (markup, tagline, social-links) → Task 2 Step 3
- JS changes (remove navbar listener, remove orphaned skill-card handler) → Task 1
- Testing/verification (manual walkthrough) → Task 5
- Delivery (single commit … actually multiple commits, one per task, all to main) → each task ends with a commit; Task 5 pushes

One minor divergence from the spec: spec said "one commit" for delivery; plan uses one commit per task (five total) for safer incremental review. The net outcome on `main` is identical. Noting here, not changing.

**Placeholder scan:** no TBDs, no "implement later", no "add appropriate error handling", no "similar to Task N". Every code block contains literal code or literal markup.

**Consistency check:**
- Class names used in markup (`.project-icon`, `.project-tags`, `.tag`, `.writing-grid`, `.writing-card`, `.writing-label`, `.writing-title`, `.writing-excerpt`, `.writing-date`, `.writing-more`, `.socials-tagline`) all match the CSS rules that define them.
- Anchor targets (`#projects`, `#socials`, `#writing`) match the section IDs.
- CSS custom properties referenced (`--primary`, `--accent`, `--secondary`, `--text-light`, `--text-dim`, `--card-bg`) are all defined in the existing `:root` block (lines 14–24 of `index.html`).
- URLs in the plan match the URLs in the spec.

---

## Execution Handoff

Plan complete and saved to `docs/superpowers/plans/2026-04-23-portfolio-sections.md`. Two execution options:

**1. Subagent-Driven (recommended)** — I dispatch a fresh subagent per task, review between tasks, fast iteration.

**2. Inline Execution** — Execute tasks in this session using executing-plans, batch execution with checkpoints.

Which approach?
