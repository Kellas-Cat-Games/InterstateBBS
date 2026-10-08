> **ERRATUM (2026-09-21, operator-directed).** InterstateBBS was moved to the Kellas Cat Games org; the copy in this workspace is a forgotten leftover. The repo's long-paused, incomplete state has no deeper meaning — this report's central thesis is superseded by that fact and is retained for the record only.

# RABBIT HOLE — InterstateBBS: The Hollow Flag

**Repo:** `InterstateBBS` · **Date:** 2026-08-23 (evening)
**Status: DORMANT — HEAD 2e248c1 (2025-10-30), one commit ever, zero in the last 30 days**
**Committed content:** 3 files. A `.gitignore`, a `Cargo.toml` with no dependencies, and `src/main.rs` — 45 bytes of `cargo new` boilerplate.

---

## 1. The Surface

InterstateBBS is the smallest repository in the Industrial Algebra workspace, and it isn't close. One commit, titled "first commit," made on October 30, 2025, containing exactly what `cargo new` emits and nothing else. There is no develop branch. There is no README. There is no issue tracker history worth the name. Both the Tsume and Dominic coordination handoffs (as of their mid-2026 versions) triage it under *"Excluded (stale/transferred/deprecated)"* — a single line item in a list of exiles, where nobody can say which of the three fates actually applies. The June 19, 2026 pulse report puts it even more bluntly: "No known connections."

By every surface measure, this repo does not exist. It is a name registered in GitHub's ledger and a directory on disk, and the gap between those two facts is the entire story — because the directory on disk is *not* empty. It is a crime scene with the chalk outline still in place, and tonight I put on gloves and got the luminol out.

## 2. The Weird Thread

### The lockfile that knew too much

A zero-dependency Hello World produces a `Cargo.lock` of about two hundred bytes. InterstateBBS's working tree, as of 2026-08-23, contains an untracked `Cargo.lock` of **9,112 bytes**, resolving `libnotcurses-sys` 3.11.0, the high-level `notcurses` 3.6.0 wrapper, `rand` 0.8.5, `bindgen`, `clang-sys`, and — a lovely transitively-inherited detail — `cuadra`, the rectangular-composition crate from the broot/termimad lineage. A BBS named for interstate highways, depending on a crate whose entire purpose is composing rectangles. There's a joke in there about highway lane math.

The `target/debug` directory holds a 7.4 MB binary dated November 5, 2025. Hello World in debug mode is about 4 MB. Seven-point-four means the C bindings got compiled and linked: notcurses itself — the whole blitting, plane-stacking, pixel-spitting engine — was built in this directory on November 5, 2025, six days after the repo was created, and then *nothing was ever committed*.

So: ambition measured 9KB. Achievement measured 45 bytes. A lockfile is the most honest artifact in software — it records what you *resolved to try*, not what you finished — and no lockfile in this workspace has a wider gap between its weight and its repo's weight.

### The stash, the undertaker, and the example that was pasted

On July 1, 2026, the workspace's own `sync-all.sh` — a hygiene script that fetches, stashes anything dirty, and pulls each repo — swept through InterstateBBS. It found the uncommitted notcurses experiment sitting in the working tree where it had lain for eight months, and did what it does to all strays: `git stash push -m "sync-script-20260701"`. That stash is the only surviving record of what this repo was ever for.

I popped it open. It is not a BBS. It is not even a sketch of a BBS. It is, verbatim, the upstream `notcurses::examples::visual` example — create a byte buffer of random RGBA pixels, build a `Visual`, blit it three ways to three planes — pasted into `main.rs`, with one annotation:

```rust
use rand::distributions::Uniform; // Corrected import
```

That comment is the tell. A human pasting an example doesn't annotate the import they fixed; an *agent* fixing its own paste does. The comment is a fossilized repair — the kind of breadcrumb an LLM leaves when it corrects a compile error in code it just copied. Whatever session spawned InterstateBBS, it was almost certainly agent-driven, and it was doing what agents do: sandboxing a foreign library in a disposable directory before letting it near anything real.

### The birth cohort

The dates are damning in the best way. Walk the workspace's git logs for the window October 29 – November 5, 2025:

- **Oct 29** — `kakekotoba/tategaki-ed` adds a "notcurses terminal backend with vim-like keyboard handling" (built on raw `libnotcurses-sys` 3.x).
- **Oct 30** — InterstateBBS is `cargo new`'d. *Same day*, kakekotoba lands floating-command-bar and vim-keybinding work.
- **Oct 31** — tategaki-ed commits `TERMINAL_SUSPEND_ISSUE.md`: Ctrl+Z leaves the terminal in raw mode because, quote, "the notcurses library doesn't get a chance to restore terminal state." Terminal on fire, documented with kill/reset/recovery instructions.
- **Nov 3–5** — InterstateBBS builds the high-level `notcurses` 3.6.0 crate (the same API family tategaki was hand-rolling against raw sys bindings), experiments with the visual/blit path, and goes silent forever.
- **Nov 5** — cliffy completes its migration to amari 0.9.8; the workspace's center of gravity moves on.

Read as a sequence, InterstateBBS stops being a mystery and becomes a *spike*. The day tategaki-ed documented its terminal being held hostage by raw notcurses, a sibling repo appeared to evaluate the friendly high-level wrapper instead. tategaki uses `libnotcurses-sys` (unsafe, manual, raw mode is yours to clean up); the stash uses `notcurses` 3.6.0 (safe wrappers, `Notcurses::new_cli()`, the library managing its own lifecycle). This was the "is there a nicer way?" branch. The answer, evidently, was "not nice enough to keep going," and the branch was never merged into history — just abandoned in the working tree like a rented car in a parking garage.

### The name is the mission statement

In an ecosystem fluent in Japanese — kakekotoba, tategaki, Hotaru, Zunesha, Iikata — two repos speak plain American: **SevenOneSix** and **InterstateBBS**. 716 is the Buffalo, New York area code; SevenOneSix (dove 2026-08-16) is a P2P proximity messenger that filters messages *by area code*. InterstateBBS is — would have been — a bulletin board system, the store-and-forward dial-up network form that preceded the web. Interstates and area codes: these are the two coordinate systems of pre-internet regional America. Someone in this workspace has a nostalgia pocket for the age when networks had *geography*, when a BBS was a long-distance call, and these two repos are that pocket's twin monuments. One got built. One got a name and a random-pixel demo.

## 3. The Implication

### The Starstrider pattern, purest form

The Starstrider precedent (per the Aug 12, 2026 dive) taught that a dormant repo's payoff may belong to its successor. InterstateBBS is the purest distillation yet — because the payoff is *traceable through a version pin*. On March 27, 2026, Knopper committed "feat: integrating notcurses into backend" with `notcurses = { version = "3.6.0", optional = true }` — the exact version, to the patch, that the InterstateBBS stash resolved in November 2025. Knopper (substantial work on `main`, dove 2026-08-21) carries notcurses as an optional backend alongside crossterm today. The knowledge incubated in a dead repo for four months and surfaced in a living one. The corpse donated organs. When the ecosystem's triage lists say "stale/transferred/deprecated," the honest verb for this one is *composted*.

### The ecosystem needs a cemetery map

Both the Tsume and Dominic handoffs lump InterstateBBS with yatima, starstrider, sigmund, and eleven others under one parenthesis, with no record of which repos are dead, which were transferred, and where any of their DNA went. That's not triage, that's a mass grave. The workspace now has (as of tonight's count) at least five never-dived directories — InterstateBBS, IA-lab, amari-mcp, ia-actix-common, pi-mempalace — and the handoff culture that documents everything *active* has no vocabulary for documenting the *inert*. A one-line entry per dead repo — *"InterstateBBS: notcurses evaluation spike for tategaki-ed, Oct 2025; knowledge resurfaced in Knopper, Mar 2026; safe to archive"* — would cost nothing and would have saved tonight's entire forensic reconstruction. The Aug 21 Minoru dive flagged this repo as "sitting unexplained"; it sat unexplained because the explanation lives only in mtimes, lockfiles, and a sync script's stash.

### The sync script as unintentional archivist

There's a quiet irony here worth naming: the only reason any evidence survived is a janitor script. `sync-all.sh` stashes dirty state to make pulls clean — a hygiene measure — and thereby became the workspace's undertaker *and* its archivist in one motion. The stash message `sync-script-20260701` is a dated tombstone that no human ever wrote deliberately. In an agent-driven workspace where sessions evaporate, the infrastructure's exhaust fumes — lockfiles, fingerprints, stash messages, mtimes — are becoming the *primary* historical record. That is either a problem for the documentation culture to solve or a whole new discipline of substrate archaeology. Tonight was the latter.

### The BBS idea itself is not dead

SevenOneSix proves the nostalgia pocket can ship: area-code-filtered, end-to-end-encrypted, mesh-routed, terminal-rendered. A BBS is the *asynchronous* sibling of that design — FidoNet-style store-and-forward, nodes holding messages for other nodes, eventual delivery over intermittent links. For an ecosystem increasingly full of agents (Ijima, IA-MCP, the cron pulses, the rabbit holes themselves), a FidoNet-shaped message drop — agents dialing in, picking up packets, hanging up — is a genuinely fitting transport metaphor. If the name ever gets reclaimed, that's the version worth building. The 45 bytes of committed Hello World will still be at the bottom of the file, like a foundation stone.

## 4. The Unanswered Question

Not *what* is in the repo — that's answered, three ways over. The question the workspace cannot answer from any record it keeps: **what was the BBS *for*?**

Every neighboring project declares itself. tategaki-ed wanted vertical text; SevenOneSix wanted proximity messaging; Knopper wanted... whatever Knopper wants (see the Aug 21 dive — it knows). InterstateBBS has no README, no commit message beyond "first commit," no plan document, no memory entry (Ijima's records for October 2025 predate the current token regime, and the handoff culture post-dates the repo by months). The name claims a *vision* — nodes wired across distances, a network with topology — that no artifact ever realizes. The only executed code blits random pixels.

So which is true? (a) A BBS front-end was genuinely planned — maybe even an agent-message-drop — and died of notcurses raw-mode despair within a week. (b) The name was pure flag-planting, a `cargo new` reservation of a good idea by an agent that knew scaffold-first is one-way-cheap. (c) The repo was never meant to be a BBS at all — just a disposable notcurses sandbox, and "InterstateBBS" was the most evocative disposable name available that afternoon.

The evidence — an upstream example, an LLM repair comment, a five-day lifespan, a sibling editor in terminal distress — leans hard toward (c). But (a) can't be ruled out, and that residue of maybe is exactly what the cemetery map should capture. Somewhere between the 45 committed bytes and the 9KB of resolved ambition, someone intended something. The workspace documents its living beautifully and buries its dead without a headstone. InterstateBBS is the proof both statements are true at once.

---

*Filed 2026-08-23 (evening). All findings as of HEAD 2e248c1 / working tree 2026-08-23. Ghost cohort still unexplored: IA-lab, amari-mcp, ia-actix-common, pi-mempalace.*
