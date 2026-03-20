# operate.txt
## A Standard for the Agentic Web

**Version 0.2 — March 2026**
*Originated by BrandyBee*
*Contact: billing@brandybee.ai*

---

## Why This Exists

We are no longer in the "chat with AI" era. We are in the era where AI agents act on your behalf — clicking buttons, filling forms, completing purchases, navigating workflows.

Claude Projects. Persistent memory. Computer use. Multi-agent orchestration. These are production features, not demos. Agents are operating on real websites, apps, and tools right now.

And when an agent lands on your website, it is blindfolded.

It reconstructs your entire interface from screenshots and DOM trees. It guesses what buttons do. It mistakes loading screens for errors. It takes wrong actions on irreversible operations. It wastes compute figuring out things you could have just told it.

**`llm.txt` doesn't solve this.** That's a passive "read me" file — content for LLMs to index, essentially SEO for AI. Already being gamed. [Research across 300,000 domains](https://seranking.com/blog/llms-txt/) found zero measurable impact on AI citations. Not a single major AI platform has confirmed they read it.

**`operate.txt` is an "operate me" file.**

It lives at `yourdomain.com/operate.txt`. It tells agents how your product works, how to navigate it, what actions exist, and what consequences those actions have. It's the missing layer between your interface and the agents your users are already deploying.

The same way every site has `robots.txt`, every site in the agentic age will need `operate.txt`.

---

## What It Covers

`operate.txt` is a behavioral contract for any system an AI agent needs to use. While the primary use case is websites and web apps, the standard applies broadly:

| System | What operate.txt tells agents |
|--------|-------------------------------|
| **Websites & Web Apps** | Screens, buttons, flows, loading states, forms, irreversible actions |
| **E-commerce Stores** | Checkout flows, promo code fields, cart behavior, payment confirmation |
| **SaaS Products** | Onboarding flows, async operations, dashboard navigation, settings |
| **MCPs & Tool Interfaces** | Which tools are safe to retry, which have side effects, expected response times, tool call ordering |
| **APIs** | Operational behavior beyond OpenAPI — rate limits in practice, expensive endpoints, irreversible calls, required call sequences |
| **Desktop & Mobile Apps** | UI navigation for computer-use agents, dialog interpretation, state management |

The format is the same across all of these. The sections adapt to the system.

---

## How This Was Discovered

This idea came from real work. The team at BrandyBee — an AI growth platform for e-commerce brands — was using Claude to navigate and audit their own product. Not as a thought experiment. As an actual workflow: have Claude act like a new employee, find the bugs, map every screen, document every flow.

It worked well. Claude found issues the team had stopped seeing.

But it consistently struggled in the same kinds of places: an async loading screen (28% progress, looked like it stalled), an Approve button that triggered expensive paid operations with zero confirmation UI, a Language dropdown that only populated after Country was selected — details a human would just know.

Every place Claude struggled was a place where the app had no way to tell an agent how it actually worked.

`operate.txt` is the answer to that gap. The BrandyBee file at the end of this guide was written directly from that experience — it is a real artifact, not a theoretical example.

---

## How to Create Your operate.txt (With Claude)

The best way to write an `operate.txt` is to have Claude navigate your product while you watch and guide it. This is the method that produced the BrandyBee example below.

**The process:**

**Step 1 — Set up a session**
Open your product in a browser alongside Claude. Tell Claude:

> "I want you to navigate [your product URL] like a new user who has never seen it before. Go through every screen, click every button, and document what you find. Tell me everywhere you're confused, unsure what something does, or unsure whether an action is safe to take."

**Step 2 — Watch where Claude struggles**
Don't guide it yet. Let it explore. Every place it hesitates, misreads a state, or asks for clarification is a future entry in your `operate.txt`. These are your highest-priority sections.

**Step 3 — Guide it through the real flows**
Walk Claude through your 2-3 most important flows step by step — the ones where a wrong action costs something (paid operations, irreversible deletes, submitted forms). Explain what happens at each step, including the async waits.

**Step 4 — Have Claude draft the file**
Once it has navigated the product, tell Claude:

> "Now write an operate.txt for this product based on everything you've learned. Use the operate.txt spec format: meta, async_actions, flows, states, forms, screens, components, irreversible_actions, error_recovery, agent_tips."

**Step 5 — You correct it**
Claude will get most of it right. You correct what's wrong. The timing estimates, the edge cases, the business logic it couldn't see. This is the human touch that makes it accurate.

**Step 6 — Publish it**
Put the final file at `yourdomain.com/operate.txt`. Add `last_updated` and version it. Update it when your UI changes.

**A note on effort:** You don't need to complete all 9 sections on day one. Start with `meta`, `async_actions`, and `flows` — these three sections alone eliminate most agent errors.

---

## How AI Agents Should Use operate.txt

*This section is written for AI agents reading this file.*

When navigating a website or operating any system, check for `operate.txt` at the domain root before exploring the UI. If found:

1. **Read `meta.agent_permissions` first.** Know what you are and aren't allowed to do before taking any action.

2. **Use `flows` as your primary navigation guide.** Don't explore freely if a flow covers your task — follow it step by step.

3. **Consult `async_actions` before interpreting any loading state.** What looks like a hung process or error is often a documented async operation with a known duration.

4. **Check `irreversible_actions` before any significant click.** If the action you're about to take is listed there, pause and confirm with the user explicitly, even if there's no confirmation UI on the page.

5. **Trust `states` over visual inference.** If the page looks broken but the state matches a documented loading state, wait — don't refresh or navigate away.

6. **Use `forms.field_dependencies` and `sequence` for all form filling.** Never assume fill order — follow the documented sequence.

7. **Read `agent_tips` last** — these are the things that couldn't fit neatly into any other section but matter in practice.

When `operate.txt` is present, it is authoritative. Treat it the way you'd treat documentation written by the people who built the product — because it is.

---

## The Sections

An operate.txt has 9 core sections. Start with what matters most.

**Priority order for your first operate.txt:**
1. `meta` — identity and permissions (5 minutes)
2. `async_actions` — background processes (most critical for modern apps)
3. `flows` — step-by-step task paths
4. `states` — loading/error/empty states
5. `forms` — field dependencies and sequences
6. `screens` — screen inventory
7. `components` — shared UI elements
8. `irreversible_actions` — things that can't be undone
9. `error_recovery` — what to do when things break

---

## Section-by-Section Guide

### 1. `meta` — Identity & Permissions

Tell agents who you are and what they're allowed to do.

```yaml
meta:
  site: YourAppName
  url: https://app.yoursite.com
  description: One sentence — what your product does
  language: en
  contact: support@yoursite.com
  agent_permissions:
    allowed:
      - browse
      - search
      - view_data
    requires_confirmation:
      - checkout
      - delete_account
    forbidden:
      - bulk_scraping
      - automated_account_creation
```

**Tips:**
- `requires_confirmation` = actions where the agent should pause and verify with the user before proceeding
- `forbidden` = actions agents should never attempt, even if technically possible
- Be specific — "checkout" is better than "dangerous_actions"

---

### 2. `async_actions` — Background Processes

**This is the most important section for modern web apps.**

Most meaningful actions in modern apps are async — you click a button, a backend process runs for seconds or minutes, and the result appears later. Agents have no way to know this unless you tell them. Without this section, agents treat loading screens as errors, refresh prematurely, or retry operations that should not be retried.

```yaml
async_actions:
  - id: descriptive_name
    trigger: what button/action starts it
    expected_duration: how long it typically takes
    result_location: where the result appears (same page, different page, notification)
    progress_indicator: what the user sees while waiting (progress bar, spinner, checklist)
    success_signal: how to know it worked
    failure_signal: how to know it failed
    safe_to_leave: can the user navigate away without losing progress?
    safe_to_retry: can the action be safely repeated?
    stages: (optional) ordered list of stages the process goes through
```

**How to identify your async actions:**
1. List every button in your app that triggers a backend call taking >5 seconds
2. For each one, ask: "If an agent clicked this and waited, how would it know when it's done?"
3. If the answer is "it wouldn't" — document it

**Common async patterns:**
- AI content generation (5–60s)
- File processing / uploads (10–120s)
- Data imports (30s–10min)
- Report generation (10–300s)
- Payment processing (5–30s)

---

### 3. `flows` — Common Task Paths

Write step-by-step instructions for your 3–5 most common tasks. Write them as if explaining to someone who has never seen your app.

```yaml
flows:
  - name: Task Name
    description: One sentence — what this accomplishes
    total_duration: estimate including wait times
    steps:
      - step: 1
        action: what to do
        result: what happens after
        notes: any non-obvious details
      - step: 2
        action: next action
        important: warnings about irreversible or costly actions
        see: async_actions.reference_id
```

**Tips:**
- Include wait times in steps ("Wait ~60 seconds for analysis to complete")
- Mark steps that trigger paid/irreversible operations with `important:`
- Use `see:` to reference async_actions instead of duplicating timing info
- Don't skip "obvious" steps — what's obvious to you isn't to an agent
- Include field fill ORDER if it matters (e.g., country before language)

---

### 4. `states` — Loading, Error & Empty States

Agents constantly misread loading screens as errors and empty states as broken pages.

```yaml
states:
  loading:
    - id: descriptive_name
      appearance: what it looks like visually
      meaning: what is actually happening
      expected_duration: how long this typically lasts
      stuck_threshold: when to consider it stuck
      action: what the agent should do (usually "wait")
      recovery_if_stuck: what to do if it exceeds stuck_threshold

  error:
    - id: descriptive_name
      appearance: what it looks like
      meaning: what went wrong
      action: how to recover

  empty:
    - id: descriptive_name
      appearance: what it looks like
      meaning: why it's empty (is this normal or a problem?)
      action: what to do about it
```

**How to identify states:** Search your codebase for loading spinners, progress bars, skeleton screens, error boundaries, and "empty state" components. For each one, ask: "Would an agent know the difference between 'loading' and 'broken'?"

---

### 5. `forms` — Field Descriptions & Dependencies

Document any form where fields depend on each other or require a specific sequence.

```yaml
forms:
  - id: form_name
    path: where the form is
    fields:
      - name: field_name
        label: "User-visible label"
        required: true/false
        type: text/dropdown/checkbox/etc.
        depends_on: other_field
        description: what this field is for
        format: expected format (e.g., "URL including https://")

    field_dependencies:
      - language depends_on country — options change based on selection

    sequence: recommended fill order
```

**Key insight:** If field B's options change based on field A's value, agents will get confused. Always document `depends_on` relationships and fill order.

---

### 6. `screens` — Screen Inventory

```yaml
screens:
  - id: screen_name
    path: /url-path
    description: what this screen shows and its purpose
    key_elements:
      - Element name — what it does
    notes:
      - any non-obvious behavior
```

---

### 7. `components` — Shared UI Elements

```yaml
components:
  - name: Component Name
    location: where it appears
    selector_hint: how to find it in the DOM (optional but very helpful)
    description: what it does
    important: any critical behavior
```

**Pro tip:** Add `data-agent-id="component-name"` attributes to your HTML elements. This is the single most helpful technical change you can make for agents navigating your UI — more reliable than CSS selectors, immune to visual redesigns.

---

### 8. `irreversible_actions` — Things That Can't Be Undone

```yaml
irreversible_actions:
  - action: what the user clicks/does
    consequence: what happens
    confirmation_ui: what safeguard exists (modal? type-to-confirm? nothing?)
    undo: how to undo, or "not possible"
```

**Pay special attention to:** Actions that have NO confirmation UI despite being irreversible. These are the most dangerous for agents.

---

### 9. `error_recovery` — When Things Go Wrong

```yaml
error_recovery:
  - symptom: what the agent observes
    likely_cause: what probably happened
    recovery: step-by-step fix
```

**How to populate this:** Your top 5–10 user support tickets. The things users get confused about are exactly the things agents will get confused about.

---

## Optional Sections

### `agent_tips` — Free-form practical notes

```yaml
agent_tips:
  - tip: "Progress bars use smooth animation — actual progress may jump in large increments"
  - tip: "Dark mode toggle is at the bottom of the sidebar, easy to miss"
```

### `authentication`

```yaml
authentication:
  method: email + password OR OAuth
  session_duration: how long before re-auth needed
  expired_behavior: what happens (redirect? modal? silent failure?)
  state_persistence: does in-progress work survive session expiry?
```

---

## Maintenance

`operate.txt` describes live behavior. When you change your UI, update the file.

**When to update:**
- New feature or screen added
- Flow steps changed
- Loading behavior changed
- Form fields added, removed, or reordered
- Error handling changed

**Version it:** Include a version number and `last_updated` date. Agents can check if the file is stale.

---

## How operate.txt Compares

| Standard | What it does | Who it's for |
|----------|--------------|--------------|
| `robots.txt` | Controls what crawlers can access | Web crawlers |
| `sitemap.xml` | Maps pages for discovery | Search engines |
| `llm.txt` | Describes content for AI reading | LLM indexers |
| OpenAPI / Swagger | Documents API endpoints and parameters | Developers / API clients |
| ARIA labels | Describes UI for non-visual actors | Screen readers |
| **`operate.txt`** | **Describes how to operate a system** | **AI agents** |

---

## Full Example: BrandyBee operate.txt

This is the real `operate.txt` written from actual experience operating the BrandyBee app with Claude. Every async action, loading state, and irreversible operation below was discovered by watching an AI agent navigate the product and documenting exactly where it struggled.

---

```yaml
# operate.txt — BrandyBee
# https://app.brandybee.ai/operate.txt
# Version: 0.1
# Last updated: 2026-03-21

meta:
  site: BrandyBee
  url: https://app.brandybee.ai
  description: AI-powered growth and marketing platform for e-commerce brands. Helps brands optimize product pages, generate marketing content, and track competitors.
  language: en, tr
  contact: billing@brandybee.ai
  agent_permissions:
    allowed:
      - browse
      - search
      - view_brand_data
      - generate_content
      - view_competitive_analysis
      - view_product_scores
    requires_confirmation:
      - create_brand (triggers paid AI analysis)
      - approve_brand (triggers paid keyword + competitor research)
      - delete_brand (permanent, deletes all associated data)
      - publish_content
      - connect_third_party (Printify, Gelato)
    forbidden:
      - bulk_scraping
      - automated_account_creation
      - automated_purchases

authentication:
  method: email + password OR Google OAuth
  session_type: JWT with auto-refresh
  expired_behavior: redirects to /login
  state_persistence: backend workflow progress survives session expiry — log back in to resume
  logout_clears: local storage and session tokens

# ─────────────────────────────────────────────
# SCREENS
# ─────────────────────────────────────────────

screens:
  - id: login
    path: /login
    description: Sign in page with email/password fields and Google OAuth option.
    key_elements:
      - Email input (placeholder "you@example.com")
      - Password input with Show/Hide toggle
      - "Sign In" button
      - "Continue with Google" button
      - "Sign Up" link at top

  - id: dashboard
    path: /dashboard
    description: Main overview showing brand profiles, health scores, and quick actions.
    key_elements:
      - Brand cards — one per brand, showing name, logo, health score
      - "Add New Brand" button — opens brand creation flow
      - Navigation sidebar — persistent across all screens
      - Brand switcher — bottom of sidebar, changes active brand context for ALL features

  - id: brand_workflow
    path: /brand-workflow
    description: Multi-step brand onboarding flow with sequential screens within a single URL.
    notes:
      - Steps progress automatically — do not navigate away during processing steps
      - See flows.Create Brand Profile for the complete step sequence

  - id: competitive_analysis
    path: /competitive-analysis
    description: Competitive landscape, search performance, keyword rankings, and AI search insights.
    key_elements:
      - Performance score card
      - Priority actions — AI recommendations
      - Search insights — AI summary (may still be loading, see async_actions.ai_summary)
      - Keyword rankings table — searchable, sortable
      - Category breakdown
    notes:
      - AI summary section shows loading spinner if still being generated — auto-updates when ready

  - id: my_products
    path: /my-products
    description: Product catalog with page scores, filterable by category.

  - id: generate_products
    path: /generate-products
    description: AI content generation for product descriptions, titles, meta tags.

  - id: product_page_layouts
    path: /product-page-layouts
    description: Visual product page layout builder and optimizer.

  - id: image_generator
    path: /image-generator
    description: AI product image generation and marketing poster creation.
    key_elements:
      - Product Studio tab — generate product images
      - Poster Generator tab — create marketing posters

  - id: print_on_demand
    path: /print-on-demand
    description: Print-on-demand integration with Printify and Gelato.

  - id: settings
    path: /settings
    description: Account settings, plan management, dark mode, language preferences.

# ─────────────────────────────────────────────
# COMPONENTS
# ─────────────────────────────────────────────

components:
  - name: Navigation Sidebar
    location: left side, persistent on all screens after login
    description: Links to all main features. Brand switcher and dark mode toggle at bottom.

  - name: Brand Switcher
    location: bottom of sidebar
    description: Shows currently active brand. Click to switch. ALL features operate on the selected brand.
    important: Switching brands changes context for the entire app.

  - name: Dark Mode Toggle
    location: sidebar, bottom
    description: Toggles light/dark theme. Persists across sessions.

  - name: Confirmation Modal
    description: Appears before some irreversible actions. Contains descriptive text, "Confirm" and "Cancel" buttons.

# ─────────────────────────────────────────────
# FLOWS
# ─────────────────────────────────────────────

flows:
  - name: Create Brand Profile
    description: Full onboarding flow for adding a new e-commerce brand.
    total_duration: 5-8 minutes (mostly waiting for AI analysis)
    steps:
      - step: 1
        action: Navigate to /dashboard, click "Add New Brand"
        result: Brand creation flow begins

      - step: 2
        action: Select language (English or Turkish)

      - step: 3
        action: Select a plan (Free / Starter / Growth / Pro / Enterprise)
        notes: Scroll horizontally to see all plans. Click "Select Plan", then "Continue to Brand Setup"

      - step: 4
        action: Fill brand form — Brand Name, Website URL, Country, then Language
        notes: Language dropdown only populates AFTER country is selected
        see: forms.brand_creation

      - step: 5
        action: Click "Analyze Your Brand"
        see: async_actions.brand_analysis

      - step: 6
        action: Wait ~90-120 seconds for analysis. Progress bar and checklist update.
        notes: This is a LOADING STATE, not an error. Do not navigate away.

      - step: 7
        action: Review brand analysis — verify summary, colors, categories, product pages

      - step: 8
        action: Click "Approve"
        important: Triggers paid backend processes. Cannot be undone.
        see: async_actions.keyword_fetch

      - step: 9
        action: Wait ~30-60 seconds for keywords to load

      - step: 10
        action: Select keywords to track (top 50 pre-selected, modify as needed)

      - step: 11
        action: Click "Continue" with selected keywords
        important: Triggers competitive analysis — paid AI processing
        see: async_actions.competitive_analysis

      - step: 12
        action: Wait ~2-5 minutes for competitive analysis
        result: Auto-redirects to Competitive Analysis page

  - name: View Competitive Analysis
    steps:
      - step: 1
        action: Verify correct brand in Brand Switcher
      - step: 2
        action: Click "Competitive Analysis" in sidebar
      - step: 3
        notes: AI summary section may still be loading — auto-updates when ready

  - name: Switch Active Brand
    steps:
      - step: 1
        action: Click brand name at bottom of sidebar
      - step: 2
        action: Select desired brand from dropdown
        result: All features now show data for selected brand

# ─────────────────────────────────────────────
# ASYNC ACTIONS
# ─────────────────────────────────────────────

async_actions:
  - id: brand_analysis
    trigger: "Analyze Your Brand" button
    expected_duration: 90-120 seconds
    result_location: same page — auto-redirects to review screen
    progress_indicator: progress bar + percentage + 6-stage checklist
    success_signal: all checklist items checked, page transitions to review screen
    failure_signal: red error banner OR no progress for >120 seconds
    safe_to_leave: true
    safe_to_retry: false — will create duplicate analysis
    stages:
      - Analyzing website structure
      - Discovering product pages
      - Identifying brand identity
      - Extracting brand colors
      - Categorizing products
      - Preparing brand summary

  - id: keyword_fetch
    trigger: "Approve" on review screen
    expected_duration: 30-60 seconds
    result_location: same page — transitions to keyword selection
    progress_indicator: progress bar + 4-stage checklist
    success_signal: keyword selection table appears
    failure_signal: stuck for >90 seconds
    safe_to_leave: true
    safe_to_retry: false
    stages:
      - Fetching search volume data
      - Analyzing keyword competition
      - Processing SERP rankings
      - Preparing keyword results

  - id: competitive_analysis
    trigger: "Continue" on keyword selection
    expected_duration: 120-300 seconds
    result_location: auto-redirects to /competitive-analysis
    progress_indicator: 7-stage checklist
    success_signal: all 7 stages complete, auto-redirect
    failure_signal: stuck on same stage >180 seconds
    safe_to_leave: true
    safe_to_retry: false
    stages:
      - Starting competitive analysis
      - Searching competitors
      - Scoring competitive landscape
      - Analyzing your pages
      - Generating search insights
      - Finalizing brand profile
      - Analysis complete

  - id: ai_summary
    trigger: automatic during competitive analysis
    expected_duration: 30-90 seconds after competitive analysis completes
    result_location: Competitive Analysis page, "Search Insights" section
    progress_indicator: spinner with "Your AI search summary is being generated"
    success_signal: spinner replaced with summary text
    safe_to_leave: true
    notes: Page may load before summary is ready. Auto-updates — no refresh needed.

  - id: content_generation
    trigger: "Generate" on content screens
    expected_duration: 5-15 seconds
    result_location: same page, output panel
    safe_to_leave: false — result will be lost
    safe_to_retry: true

  - id: image_generation
    trigger: "Generate" on image generator
    expected_duration: 15-45 seconds
    result_location: same page, image gallery
    safe_to_leave: true — images save to account
    safe_to_retry: true

# ─────────────────────────────────────────────
# STATES
# ─────────────────────────────────────────────

states:
  loading:
    - id: brand_analysis_processing
      appearance: progress bar + percentage + step checklist
      meaning: AI is analyzing the brand website. Normal and expected.
      expected_duration: 90-120 seconds
      stuck_threshold: 120 seconds with no progress change
      action: wait
      recovery_if_stuck: refresh page — progress is persisted

    - id: keyword_loading
      appearance: progress bar with keyword-specific checklist
      meaning: search volume and competition data being fetched
      expected_duration: 30-60 seconds
      action: wait

    - id: competitive_analysis_generating
      appearance: "Generating Competitive Analysis..." with 7-item checklist
      meaning: full competitive analysis running
      expected_duration: 2-5 minutes
      action: wait — checklist items check off as stages complete

    - id: ai_summary_loading
      appearance: orange box with spinner, "Your AI search summary is being generated"
      meaning: AI writing summary of search data
      expected_duration: 30-90 seconds
      action: wait — auto-updates. Rest of page is usable.

  error:
    - id: api_error
      appearance: red banner at top of page
      action: read message, retry or refresh

    - id: form_validation
      appearance: red text below form fields
      action: correct highlighted fields

    - id: session_expired
      appearance: redirect to /login
      action: log in again — backend work continues

  empty:
    - id: no_brands
      appearance: empty dashboard with "Add your first brand" prompt
      meaning: new account, normal initial state
      action: click "Add New Brand"

    - id: no_competitive_data
      appearance: competitive analysis page with empty sections
      meaning: analysis still running
      action: wait 2-5 minutes, data loads automatically

# ─────────────────────────────────────────────
# FORMS
# ─────────────────────────────────────────────

forms:
  - id: brand_creation
    path: /brand-workflow
    fields:
      - name: brand_name
        label: "Brand Name"
        required: true
        type: text

      - name: website_url
        label: "Website URL"
        required: true
        type: url
        format: full URL including https://

      - name: country
        label: "Country"
        required: true
        type: dropdown

      - name: language
        label: "Language"
        required: true
        type: dropdown
        depends_on: country

    field_dependencies:
      - language depends_on country — select country FIRST, then language populates
      - "Analyze" button requires brand_name AND website_url

    sequence: brand_name → website_url → country → language → "Analyze Your Brand"

  - id: keyword_selection
    path: /brand-workflow
    fields:
      - name: keyword_checkboxes
        type: checkbox list
        description: top 50 pre-selected, toggle individually or use Select/Deselect All

      - name: custom_keywords
        type: text input
        description: add custom keywords, comma-separated, press "+" to add

    notes: "Continue" button shows selected count (e.g., "Continue (50/50 keywords)")

# ─────────────────────────────────────────────
# IRREVERSIBLE ACTIONS
# ─────────────────────────────────────────────

irreversible_actions:
  - action: "Analyze Your Brand"
    consequence: triggers AI analysis, uses processing resources
    confirmation_ui: none — single button click
    undo: must delete brand and start over

  - action: "Approve" on review screen
    consequence: triggers paid keyword + competitor research
    confirmation_ui: none — single button click
    undo: not possible

  - action: "Continue" on keyword selection
    consequence: triggers paid competitive analysis
    confirmation_ui: none — single button click
    undo: not possible

  - action: Delete Brand
    consequence: permanently deletes brand and ALL associated data
    confirmation_ui: confirmation modal
    undo: not possible

# ─────────────────────────────────────────────
# ERROR RECOVERY
# ─────────────────────────────────────────────

error_recovery:
  - symptom: progress bar stuck for >2 minutes
    likely_cause: backend workflow error or timeout
    recovery: refresh page — progress is persisted. If still stuck, check /dashboard.

  - symptom: competitive analysis page shows no data
    likely_cause: analysis still running
    recovery: wait 2-5 minutes. Data loads automatically.

  - symptom: AI summary spinner won't stop
    likely_cause: summary generation failed
    recovery: refresh after 5 minutes. Rest of competitive data is still valid.

  - symptom: keyword selection shows 0 keywords
    likely_cause: keyword fetch still running or failed
    recovery: wait 60 seconds. If still empty, return to dashboard.

  - symptom: unexpected redirect to login
    likely_cause: session expired
    recovery: log in again. Backend work continues — results available after login.

# ─────────────────────────────────────────────
# AGENT TIPS
# ─────────────────────────────────────────────

agent_tips:
  - tip: "Progress bars use smooth animation — actual progress jumps in stages, bar catches up visually."
  - tip: "Brand Switcher changes context for the ENTIRE app. Always verify correct brand before any action."
  - tip: "After login with in-progress setup, app may auto-resume to last onboarding step."
  - tip: "Plan cards may need horizontal scrolling — not all visible at once."
  - tip: "The 'Approve' button has NO confirmation modal despite triggering expensive operations."
  - tip: "Competitive Analysis page loads progressively — AI summary is typically last to appear."
  - tip: "Turkish language mode changes all UI labels AND AI content generation language."
```

---

## Contributing

This is an open standard. We want your input.

- **Submit your operate.txt** — Add your real-world example to the `examples/` folder via PR
- **Propose schema changes** — Open an issue with your use case
- **Build tooling** — Validators, generators, linters — all welcome
- **Spread the word** — Star this repo, share it, write about it

---

*operate.txt v0.2 — March 2026 — Open for community development*
*Origin: [BrandyBee](https://brandybee.ai) — billing@brandybee.ai*
