# Contributing to operate.txt

Thanks for your interest in making the agentic web work better. Here's how you can help.

## Submit Your operate.txt

The most valuable contribution is a real operate.txt from a real product. We want to build a library of examples that covers different types of systems.

**How to submit:**

1. Fork this repo
2. Add your file to `examples/` as `{yourproduct}-operate.txt`
3. Open a PR with a one-line description of what your product does

**Requirements:**
- Must be from a real product (no hypothetical examples)
- Must include at least `meta`, `flows`, and one other section
- Must be YAML format following the spec
- Should be written from actual experience operating the product with an AI agent

## Propose Schema Changes

The spec is v0.2. It will evolve. If you've written an operate.txt and found that the current schema doesn't cover something important, open an issue with:

- **What you needed to express** — the real situation
- **Why existing sections don't cover it** — what's missing
- **Your proposed addition** — a YAML example of how it would look

## Build Tooling

We need:
- **Validators** — does this operate.txt follow the spec?
- **Generators** — crawl a site and produce a draft operate.txt
- **Linters** — catch common mistakes (missing required fields, etc.)
- **IDE extensions** — syntax highlighting and autocomplete for operate.txt files
- **Integration libraries** — help AI agents read and parse operate.txt at runtime

If you're building any of these, open an issue to coordinate so we don't duplicate effort.

## Report Issues

Found a problem with the spec, the documentation, or an example? Open an issue. Be specific about what's wrong and what you'd suggest instead.

## Style Guide

- YAML for all operate.txt files and examples
- Plain language in descriptions — write for an agent that has never seen your product
- Comments with `#` for context that helps humans maintaining the file
- Use real durations, real button labels, real URLs — no lorem ipsum

## Code of Conduct

Be constructive. This is an open standard meant to help everyone build better products for the agentic web. Criticism of ideas is welcome. Criticism of people is not.

---

*Questions? Contact billing@brandybee.ai*
