# AppADay 134 &#183; Anagram Alchemy

**Category C (Creative) &#183; AI-powered &#183; Shipped 2026-09-18**

**Live:** https://augustineiacopelli.github.io/appaday-134-anagram-alchemy/

**Portfolio:** https://augustineiacopelli.github.io/appaday/

Enter a name or phrase and Claude proposes anagram candidates, but only the ones that survive a local letter by letter audit ever reach the screen. Choose a survivor and the original letters physically travel into their new arrangement. Favorite the keepers or export a 1080 square share card.

## Why the verifier matters

Language models are bad at counting letters. Ask any of them for an anagram and a convincing fraction of the answers will be off by a letter or two, which is exactly the kind of error a reader will not catch by eye. This app treats every candidate as a claim to be tested rather than an answer to be trusted.

Each proposal is normalized to lowercase letters only, converted to a 26 slot frequency vector built from `charCodeAt(i) - 97`, and compared slot by slot against the source vector. A candidate is accepted only when every slot matches exactly. It is additionally rejected when it merely restates the source, when it duplicates a phrase already accepted, or when it contains any character outside ASCII. Every rejection is recorded with a reason code.

The verifier was written and proven against hand built pass and fail cases before any network code existed, and the same self test runs at page load and reports to the browser console.

## How a run works

One request goes out asking for roughly twelve candidates, stating the source phrase and spelling out the exact letter inventory as a count per letter. The reply is parsed as a bare JSON array, with a line by line fallback if the model wraps it in anything. Every candidate is verified locally.

If five or more survive, the app renders them and stops. If fewer survive, it fires exactly one more request that repeats the letter inventory, lists the accepted phrases so they are not repeated, and includes up to eight rejected candidates as negative examples. That batch is verified identically and merged. There is no third attempt. If nothing survives, the app says so plainly and leaves the input intact for editing.

## The morph

Selecting a survivor runs a FLIP animation in which the source letters are the same DOM nodes that end up in the new arrangement. Source spans are bucketed by letter, the target phrase is walked pulling one span per letter, first rectangles are measured, the DOM is reordered with fresh spacer spans, last rectangles are measured, each span is inverted with a transform, and a single reflow later the transforms are released on a staggered transition. Because the letter vectors already matched, the mapping can never fail. When the visitor prefers reduced motion, the final arrangement is written directly with no measuring and no transforms.

## Export

The share button composes a 1080 by 1080 card on an offscreen canvas that is never attached to the document: source phrase, a transformation arrow, the result as the hero sized by a fit loop that wraps to at most three lines, then the optional session name and the portfolio address. It exports through the native share sheet when the browser supports sharing files, and downloads as a PNG otherwise.

On browsers that report WebM support, a second button records about three seconds of the morph interpolating between the start and end letter positions and downloads the clip. Where that support is missing the button is never inserted, and the PNG path never depends on it.

## Setup

Open the gear in the header and paste an Anthropic API key. The key and the optional session name live in `localStorage` on this device only, never appear on the main screen, and are never committed to source. Requests go directly from the browser to the Anthropic Messages API. Charges land on your own account.

## Stack

One self-contained `index.html` with inline CSS and JavaScript. No frameworks, no build step, no dependencies beyond a Google Fonts link. Model string `claude-sonnet-5`.

## Storage keys

`appaday-134-settings` holds the API key and session name. `appaday-134-favorites` holds saved pairs of source and result with a timestamp. `appaday-134-last` holds the most recent source phrase so it is restored on reload. Every read and write is wrapped in `try/catch`.

---

*Part of [AppADay](https://augustineiacopelli.github.io/appaday/). Ship something every day. It compounds.*
