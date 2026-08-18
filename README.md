<p align="center">
  <img src="img/LogoHorizontalDark.png" width="320" alt="peek">
</p>

<p align="center">
  <strong>A native markdown viewer for your terminal workflow</strong>
</p>

---

**peek** sits beside your editor. You write markdown, save, and peek updates instantly. No
browser tabs. No Electron. Just a native window that stays out of your way.

```
peek notes.md
```

That's it. A window opens. Your markdown renders beautifully. Save the file, and it
reloads — scroll position preserved, focus untouched.

---

## There is no download

This repository contains no source code and no binaries, and never will. It is a
[Commons Format](https://commonsformat.dev/) module: peek distributed as **verifiable
intent** — a prose specification, a threat model, and a conformance eval suite. You don't
install peek; you **generate your own** and verify it against the evals.

Why ship a spec instead of code? Shared code is a shared attack surface: one discovered
exploit amortizes across every machine running the same bytes. When each consumer generates a
divergent implementation from the same contract, that economy collapses. The full argument is
in two essays:

- [OSS has moved to a Dark Forest](https://hayshutton.tech/essays/oss-dark-forest)
- [Save the OSS Commons with a Format](https://hayshutton.tech/essays/save-the-commons)

## Generate your own peek

Point a capable code generator at this module:

1. Feed it `commonsformat.md` — the intent, constraints, interface, and threat model.
   `references/visual-spec.md` carries the complete visual design.
2. Generate an implementation in your language of choice. The reference target is macOS
   (Cocoa + WKWebView); the constraints pin the platform deliberately.
3. Verify against `evals.toml`. All functional cases passing is tier **D0**; adversarial
   cases plus full constraint coverage is tier **D1** — the tier this module targets.

Your peek will not be byte-identical to mine. That's the point. It will behave identically
where it matters, because the contract — not the code — is what's shared.

## What peek does

- **Instant** — native WebKit rendering, first paint within 100 ms.
- **Passive** — reloads never steal focus. Glance at it; it doesn't interrupt.
- **Self-contained** — a single small binary, all assets embedded, zero network access.
- **Beautiful** — warm, readable typography. One carefully considered theme.

### View a file

```bash
peek README.md
```

Edit the file in vim, save, and peek reloads automatically — same pixel scroll offset, and
vim keeps focus.

### Pipe from stdin

```bash
cat doc.md | peek
pbpaste | peek
curl -s https://example.com/README.md | peek
```

Stdin content is static — no file to watch.

### As an agent hook

The workflow peek was really built for: wire it into your coding agent so every markdown
document the agent writes opens beside your terminal, already rendered, in case you want to
read it. With Claude Code, a `PostToolUse` hook on `Write` that runs `peek <file> &` for
`*.md` paths is all it takes. Because peek never steals focus and each invocation gets its
own window, generated documents pile up quietly at the side of your screen instead of
interrupting the session.

### Keyboard

| Key     | Action                |
|---------|-----------------------|
| `j`/`k` | Scroll down / up      |
| `gg`    | Jump to top           |
| `G`     | Jump to bottom        |
| `Space` | Page down             |
| `q`     | Quit                  |
| `Cmd+W` | Quit                  |

### Links

- **External URLs** → open in your default browser
- **Relative `.md` files** → open in peek, replacing the current file
- **Anchor links** → smooth scroll to the heading
- **Code blocks** → hover for a copy button; click, get a checkmark, paste elsewhere

## Configuration

Config lives at `~/.config/peek/config.toml`, created automatically on first run. Window
size, fonts, sizes, and every color are overridable; the full schema is in the
`<interface>` section of [`commonsformat.md`](commonsformat.md).

The defaults are intentional. Warm cream background (`#faf8f5`) reduces eye strain. Generous
line-height (1.75) makes long documents scannable. The [IBM Plex](https://www.ibm.com/plex/)
family provides harmonious prose and code faces.

## Design philosophy

- **Companion, not destination.** peek sits beside vim. A natural extension of your
  workspace, not an application demanding attention.
- **Warmth without softness.** Calm and comfortable for long reading sessions, with the
  precision expected of technical documentation.
- **One good answer.** No dark mode toggle. No theme picker. One carefully considered design
  that works.

Deliberately excluded: dark mode, tabs, search, export, printing, editing. These aren't
oversights — peek does one thing well.

## Module contents

| File                        | Purpose                                             |
|-----------------------------|-----------------------------------------------------|
| `commonsformat.toml`        | Module metadata                                     |
| `commonsformat.md`          | Intent, constraints, interface, threat model        |
| `evals.toml`                | Conformance suite (functional + adversarial, D1)    |
| `references/visual-spec.md` | Complete visual design: palette, type scale, layout |
| `fixtures/`                 | Test documents the evals reference                  |

## License

MPL-2.0 — the license covers this specification and eval suite. Implementations you generate
from it are yours.

---

<p align="center">
  <sub>Built for people who live in the terminal.</sub>
</p>
