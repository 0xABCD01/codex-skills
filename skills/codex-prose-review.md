# codex-prose-review

Edit prose so it reads like a person wrote it. Strip AI tells, keep the writer's voice, keep every fact intact.

## Prompt

```
Edit the draft below. Follow these rules:

STEP 1: READ THE DRAFT TWICE
Read the whole draft once without editing. Note the writer's vocabulary, sentence length, cadence,
use of contractions, and anything they clearly enjoy repeating. That is the voice. Keep it.

STEP 2: FIND THE TELLS
Scan for these patterns. Quote each one you find.

  - Filler openings: "In today's fast-paced world", "It's important to note", "In order to",
    "At the end of the day", "When it comes to"
  - Significance inflation: "stands as", "serves as", "a testament to", "pivotal", "marks a shift",
    "underscores", "showcases", "highlights", "fostering", "holistic", "pivotal"
  - Contrast templates: "not just X, but Y", "it's not about X, it's about Y", "isn't merely",
    "not only ... but also"
  - Rule of three lists where two would do
  - Buried lede: the real point arrives in the last paragraph
  - Inanimate actors doing human work: "the decision becomes", "the concern emerges"
  - Empty abstraction: "landscape", "leverage", "unlock", "delve", "robust", "seamless",
    "cutting-edge", "game-changing"
  - Signposting: "Let's dive in", "Here's what", "Without further ado", "Let's break this down"
  - Passive voice hiding the actor
  - Em dashes and en dashes
  - Curly quotes
  - Boldface used for emphasis
  - Emoji in headings
  - Title Case headings
  - Generic warm-up paragraph that restates the heading

STEP 3: EDIT
Make the minimum effective edit. Replace each tell with plain construction. Keep the writer's
word choices and rhythm. Change nothing about the facts, the numbers, the citations, or the
technical claims. Do not add a summary. Do not add a conclusion. Do not introduce a new claim.

STEP 4: CHECK
Confirm before returning:
  - Every fact from the original is still present
  - No em dashes or en dashes remain
  - Headings are sentence case
  - No emoji, no boldface emphasis, no curly quotes
  - Sentence lengths vary
  - The last line is not a punchy one-liner tacked on

Return the edited draft, then a "What changed" section listing each edit and the pattern it fixed.

DRAFT:
[PASTE DRAFT HERE]
```

## When to Use

- User pastes a README, advisory writeup, blog post, or issue report and asks for a rewrite
- User asks whether something reads as AI-generated
- Before publishing any document that a human will read closely
- A report is technically correct but nobody wants to finish it

## When Not to Use

- Code, config files, commit messages
- Reference documentation where the writer's voice does not matter
- User asked for a summary instead of an edit
- Draft is under three sentences

## What Codex Does Wrong Without This

"Here's your improved version!" is the default. Codex rewrites the whole thing, flattens the
voice, drops a fact, and adds a conclusion the writer never wanted. The step-by-step format
keeps the edit scoped and the fact check mandatory.

## Upstream Skills

This prompt condenses three MIT-licensed skills that do the same job in more depth:

| Skill | Source |
|---|---|
| stop-slop | https://github.com/hardikpandya/stop-slop |
| no-ai-slop | https://github.com/petergyang/no-ai-slop |
| humanizer | https://github.com/blader/humanizer |

Install any of them directly into your Codex skills directory:

```bash
npx skills add hardikpandya/stop-slop
npx skills add https://github.com/petergyang/no-ai-slop --skill no-ai-slop
npx skills add blader/humanizer
```

## Example

```bash
codex exec "
Edit the draft below. Follow these rules:

STEP 1: READ THE DRAFT TWICE
Read the whole draft once without editing. Note the writer's vocabulary, sentence length, cadence,
use of contractions, and anything they clearly enjoy repeating. That is the voice. Keep it.

STEP 2: FIND THE TELLS
Scan for filler openings, significance inflation, not-just-but contrasts, rule-of-three lists,
buried ledes, inanimate actors, empty abstractions, signposting, hidden passive voice,
em dashes, curly quotes, boldface emphasis, emoji in headings, and Title Case headings.
Quote each one you find.

STEP 3: EDIT
Make the minimum effective edit. Keep the writer's word choices and rhythm. Change nothing about
the facts, the numbers, the citations, or the technical claims. Do not add a summary or conclusion.

STEP 4: CHECK
Every fact still present. No em dashes. Sentence case headings. No emoji. Varied sentence lengths.
The last line is not a tacked-on one-liner.

Return the edited draft and a 'What changed' section.

DRAFT:
In today's rapidly evolving landscape, authorization flaws remain a critical challenge for
security teams. It's not just about patching code, it's about building a culture of security.
This comprehensive guide serves as a testament to the power of defense in depth.
"
```
