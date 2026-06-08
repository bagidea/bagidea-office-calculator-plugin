# Working on this plugin — notes for Claude

This repo is a **BagIdea Office plugin**: the Calculator. It's also meant as a
reference example. This file gives you the conventions; defer to the office's
`docs/guide/plugins.md` for the complete spec.

## This plugin in one breath
A safe scientific calculator. `index.js` has NO `eval`/`Function` — it
tokenizes, runs shunting-yard into RPN, and evaluates. The same `evaluate()`
backs two surfaces: the agent command `calc` (`onCommand`) and a GET route
`/plugin/calculator/eval?e=<expr>` that `panel.html` calls. It needs no `ctx`.

When extending: add functions/constants to `FUNCS`/`CONSTS`, operators to `OPS`
(set precedence `p` and associativity `a`). Keep it `eval`-free.

## Plugin shape (general)
A folder with `plugin.json` at its root, plus optional `index.js` and
`panel.html`. Installed at `plugins/<id>/`; loaded by the office daemon.
- **`plugin.json`** — `id`, `name`, `version`, `description`, optional `panel`,
  and `commands: [{name, args, desc}]` (surfaced to every agent). Don't set
  `"core": true` — that's reserved for the office's own bundled plugins.
- **`index.js`** — `module.exports = (ctx) => ({ onCommand?, routes? })`.
  - `onCommand(cmd, args, reply)` handles `POST /plugin/<id>/cmd {cmd,args}`;
    call `reply(obj)` exactly once.
  - `routes` = `{ name(req,res,{readBody,readBodyRaw}) {} }` at `/plugin/<id>/<name>`.
- **`panel.html`** — rendered in the overlay; fetch your routes with a single
  `BASE` const; keep the dark theme (`#0c1322` / `#5ec8ff`); a flex-column body
  that fills the panel height so there's no scrollbar.

## `ctx` (available if you need it)
`ctx.reg` (registry, READ), `ctx.saveReg()`, `ctx.feed(text, agentId?)`,
`ctx.broadcast(event)`, `ctx.runClaude(agentId, prompt, opts?)`, `ctx.dataDir`
(private storage), `ctx.pluginDir`, `ctx.daemonDir`, `ctx.manifest`,
`ctx.log(msg)`.

## Reload while developing
`curl -s -X POST http://127.0.0.1:8787/plugins/reload -H "x-bagidea-ui: 1"`

## Rules
- stdlib only (`fs`/`path`); no external npm packages.
- Validate `args`; one command → one `reply()`.
- Don't touch other plugins' folders or office files destructively.
