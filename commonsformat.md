# peek

A passive, native markdown companion for terminal workflows on macOS.

<intent>
peek is a markdown viewer that sits beside a terminal editor such as vim. The user runs
`peek notes.md` and a native macOS window opens showing the rendered document. When the user
saves the file, peek reloads the content in place, preserving the exact scroll position and
never taking focus away from the editor. Content may also be piped on stdin for one-shot
previews of clipboard text or command output.

peek is deliberately not a general-purpose application. It has no tabs, no file browser, no
export, no editing. It is a single window that renders one document beautifully and stays out
of the way. The target user lives in the terminal; peek exists so they never need a browser
tab to read markdown.

The rendered output is GitHub Flavored Markdown: headings, lists, tables, fenced code blocks
with syntax highlighting, task lists, blockquotes, inline images resolved relative to the
source file, and links. Typography is warm and book-like by default, with every visual
parameter overridable through a user configuration file.
</intent>

<constraints>
- macos-native-window: The viewer must present a native macOS Cocoa window hosting a system
  WebKit view. It must not launch a browser, embed a browser framework such as Electron or
  CEF, or depend on a cross-platform GUI toolkit.
- no-raw-html: Raw HTML present in the markdown source must be stripped before rendering. No
  author-supplied HTML, including script and style elements, may reach the rendering view.
- no-focus-steal: A content reload must never activate the window, raise it above other
  windows, or take keyboard focus from the frontmost application.
- scroll-preserve: After a reload triggered by a file change, the view must restore the exact
  pixel scroll offset that was current before the reload.
- no-watch-on-stdin: When content arrives on stdin there is no file to watch; the viewer must
  not start any file watching and the content is static for the life of the window.
- close-on-delete: If the watched file is deleted or renamed, the viewer must close its window
  and exit.
- link-routing: Clicked external URLs must open in the default browser. Clicked relative links
  ending in `.md` must open inside the viewer, replacing the current document. All other
  relative links must open in the default browser.
- single-binary: The viewer must ship as one self-contained binary with all assets (fonts,
  styles, parser, highlighter) embedded. It must make no network requests at runtime and must
  not load code or assets from outside the binary, other than the user's config file and the
  documents it is asked to render.
- render-latency: First render must appear within 100 milliseconds of invocation for a typical
  document.
- reload-latency: A saved change to the watched file must be visible in the window within
  100 milliseconds of the save completing.
- binary-size: The compiled binary must be 5 MB or smaller.
- memory-ceiling: Resident memory must stay at or under 50 MB for typical documents.
- visual-defaults: Default typography and color must match the peek identity: IBM Plex Serif
  for prose (Georgia fallback), IBM Plex Mono for code (Menlo fallback), warm cream `#faf8f5`
  background, content constrained to a centered 720 px column, horizontal scrolling inside
  code blocks. Every one of these must be a default only, overridable via the config file.
- config-file: Configuration must be read from `~/.config/peek/config.toml`. On first run,
  when no config exists, a default config file must be created there. Invalid TOML must fall
  back to defaults with a warning on stderr, never a crash.
</constraints>

<avoid>
- Embedded browser frameworks (Electron, CEF, Tauri-with-bundled-engine) or spawning an
  external browser for rendering. The whole point is a native window.
- Fetching anything over the network at runtime: fonts, parser libraries, highlight themes,
  update checks. All assets are embedded at build time.
- Polling-based file watching. Use the platform's native change notification mechanism
  (FSEvents or kqueue on macOS) so reloads are immediate and idle cost is near zero.
- Passing markdown-authored HTML through to the view, even sanitized. Stripping is the
  contract; sanitizer allowlists are a moving target.
- Interpreting the reload as navigation. Reload replaces content in place; it must not reset
  scroll, flash white, or animate.
- Editing features, multi-document UI, tabs, printing, export. Out of scope by design.
</avoid>

<interface>
Command line:

```
peek <file.md>    Render the file and watch it for changes.
command | peek    Render stdin content; no watching.
```

Exit behavior:

- Missing file: print `peek: <filename> not found` to stderr and exit with code 1.
- Watched file deleted or renamed: close the window and exit with code 0.
- User presses `q` or `Cmd+W` or closes the window: exit with code 0.

Window:

- Title is the basename of the file being viewed.
- Default size 900x1000, overridable via config.

Keyboard map (in the viewer window):

```
j / k     Scroll down / up
space     Scroll down one page
gg        Jump to top
G         Jump to bottom
q         Quit
Cmd+W     Quit
```

Code blocks show a copy button on hover; clicking it copies the block's text to the clipboard
and briefly shows a checkmark confirmation. Document text is selectable and copyable.

Configuration file at `~/.config/peek/config.toml`, TOML with four sections:

```toml
[window]
width = 900
height = 1000

[typography]
font_prose = "'IBM Plex Serif', Georgia, serif"
font_code = "'IBM Plex Mono', Menlo, monospace"
font_size = 17
line_height = 1.75
code_size = 14
content_width = 720

[colors]
background = "#faf8f5"
text = "#3d3835"
text_heading = "#2a2622"
text_muted = "#6a635d"
code_background = "#e8e2d9"
accent = "#4a6a7a"
accent_light = "#8aaaba"
accent_faint = "#c8d0d4"

[syntax]
keyword = "#7a4a30"
function = "#3a6040"
string = "#6a5030"
number = "#7a5010"
comment = "#8a837a"
```

Unknown keys are ignored. Missing keys take the defaults shown above.
</interface>

<threat-model>
The adversary is the document, not the network. peek renders markdown the user did not
necessarily author: files from cloned repositories, piped curl output, pasted clipboard
content. The rendering surface is a WebKit view, so the primary risk is that document content
becomes code.

Assumed attacker capabilities:

- Full control of the markdown source: raw HTML, script tags, event-handler attributes,
  javascript: and data: URLs, malformed or invalid UTF-8, and pathological structure (deeply
  nested lists or blockquotes, megabyte-scale documents, extremely long lines).
- Control of link targets and image paths inside the document, including relative paths that
  traverse upward (`../../...`) and URLs with arbitrary schemes.

Out of scope: a compromised operating system, a compromised WebKit, attacks on the user's
config file (it is user-authored and trusted), and network attackers (the viewer makes no
network requests).

Consequences the implementation must prevent:

- Script execution originating from document content, in any form.
- Document-triggered network requests or file reads outside the document's own directory
  tree (images resolve relative to the source file only).
- Denial of the desktop session: a pathological document may render slowly or scroll
  poorly, but must not hang the process unrecoverably or exhaust system memory.
- Invalid UTF-8 causing a crash; render the valid portion or show an error instead.
</threat-model>

<example name="view-file">
Input: `peek README.md` where README.md exists and contains a heading, a table, and a fenced
zig code block.

Output: a native window titled `README.md` opens showing the rendered document; the table has
borders and alternating alignment per GFM; the code block is syntax highlighted with a copy
button on hover. The terminal prompt does not return until the window closes.
</example>

<example name="pipe-stdin">
Input: `printf '# hello\n\n- a\n- b\n' | peek`

Output: a window opens showing the rendered heading and list. No file watching occurs; the
content never changes for the life of the window.
</example>

<example name="live-reload">
Input: `peek notes.md` is running, the user has scrolled 800 px down, then saves a change to
notes.md from vim.

Output: within 100 ms the window shows the new content, scrolled to exactly 800 px, and vim
remains the focused application throughout.
</example>

<example name="file-not-found">
Input: `peek missing.md` where missing.md does not exist.

Output: `peek: missing.md not found` on stderr, exit code 1, no window.
</example>
