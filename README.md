# Bookmarks (soksak-plugin-bookmarks)

A plugin that shows the browser bookmark list in a right sidebar panel (★).
Zero new backend code — it only composes commands already provided by the app.

- List: `bookmark.list`
- Row click → open: `browser.open {url}`
- Row ✕ → remove: `bookmark.remove {url}`
- Live updates: subscribes to the `bookmarks.changed` event (additions and removals reflected immediately)
- When there are no bookmarks, displays "No bookmarks" and a one-line hint on how to add one
- Command failures are shown as small error text at the top of the panel (no silent failures)

## Permission Rationale

| Permission | Usage |
|------|--------|
| `ui` | Register the sidebar view (`soksak-plugin-bookmarks.list`) |
| `commands` | Execute `bookmark.list`, `browser.open` |
| `commands:destructive` | Execute `bookmark.remove` (item removal — destructive action). Highlighted on the consent screen |

Only declared permissions are used. No own command registration, storage, file, or network access.

## Installation

```sh
# If distributed as a git repo
sok plugin.install '{"url":"https://github.com/<you>/soksak-plugin-bookmarks"}'

# To use the example from this repo directly: copy to ~/.soksak/plugins/
cp -r examples/plugins/soksak-plugin-bookmarks ~/.soksak/plugins/soksak-plugin-bookmarks
```

After installation, activate (consent) the plugin in the app's plugin settings. Remote activation
is rejected with `CONSENT_REQUIRED` if no recorded consent exists (spec §0-5 — activation consent
is human-only).

## Usage

1. Click ★ in the right sidebar icon rail → the bookmarks panel opens.
2. Click a row to open that URL in the browser panel.
3. Press ✕ on the right side of a row to remove it from bookmarks (re-add to undo).
4. When bookmarks change elsewhere (browser ★ button, `sok bookmark.add/remove`), the
   panel updates immediately.

## DOM Exposure (Structural Addresses)

Elements exposed to the outside (address clicks/measurements, E2E) are declared by kind in the
manifest `contributes.nodes` and have a `data-node` attribute on the actual element.
Undeclared or unattributed elements are not accessible.

Global address: `.../view/soksak-plugin-bookmarks.list/node/<data-node>`

| Node | data-node | Description |
|------|-----------|------|
| `row` | `row/<url>` | Bookmark row (click → open in browser) |
| `remove` | `remove/<url>` | Row ✕ remove button |

`<url>` is the bookmark URL normalised as a stable key — lowercased, then characters outside
the path segment rule (`^[a-z0-9][a-z0-9.-]*$`) (`:/?#&`, etc.) replaced with `-`.
The URL itself is the stable identifier so a counter index is not used (address stays constant
across list reorders).
