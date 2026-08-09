# Diff Line Metadata over OSC 1717

A small terminal escape-sequence protocol by which a **diff renderer** (delta,
difftastic, diff-so-fancy, …) annotates each rendered line of a diff with the
patch-space identity it represents, so that a host program running the renderer
can map a screen row back to the exact line of the underlying diff.

**→ [Read the specification](diff-line-metadata-osc-1717.md)** — draft, v1.

## Why

A host that displays a rendered diff usually wants to act on the line the user is
pointing at: stage that hunk line, open an editor there, open the line in a
code-review view, navigate by hunk or by file, keep the selection anchored across
a re-render. Every one of those needs the same primitive: given a rendered row,
recover `(file, side, line)`.

Where the renderer preserves unified-diff structure, a host can parse that back
out of the text on screen. But delta's default mode and diff-so-fancy drop the
`+`/`-` markers and convey the side by color, and difftastic is token-granular
and side-by-side — in those, the unified-diff structure a parse relies on is
gone, and the renderer is the only component that still knows which file,
side and line each rendered cell belongs to. This protocol asks it to state that
inline, in a form the host can read back and that is harmless everywhere else.

The design in one breath: one OSC record before each rendered region, attached to
the cell that follows it the way an OSC-8 hyperlink is, gated behind an
environment-variable handshake — so all layout knowledge stays in the renderer,
and a renderer run outside a participating host (a raw terminal, `less`, `tmux`,
a CI log) emits nothing and behaves byte-for-byte as before.

## Status

**Draft, circulating for feedback.** The wire format, the handshake and the OSC
number itself are all open to revision — §9 of the spec lists the points where
feedback is most wanted, starting with the choice of `1717` (verified unused
across the terminals that matter, but there is no registry, so it is not
_allocated_). Nothing is finalized; please open an issue.

Prototype implementations are open as draft pull requests — emitters for
[delta](https://github.com/dandavison/delta/pull/2181),
[difftastic](https://github.com/Wilfred/difftastic/pull/1014) and
[diff-so-fancy](https://github.com/so-fancy/diff-so-fancy/pull/538), and the
consuming side in [lazygit](https://github.com/jesseduffield/lazygit/pull/5732);
see §10 of the spec.

The protocol grew out of [lazygit](https://github.com/jesseduffield/lazygit), but
nothing in it is lazygit-specific — "the host" means any program that runs a diff
renderer and consumes its output.

## License

[CC0 1.0 Universal](LICENSE) — public domain. Implement it, quote it, fork it; no
permission needed.
