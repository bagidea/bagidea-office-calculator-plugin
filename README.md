# 🧮 BagIdea Office — Calculator Plugin

A real, installable [BagIdea Office](https://github.com/bagidea/bagidea-office)
plugin and a **worked example** for plugin authors. It's a scientific
calculator with a safe math engine (no `eval`/`Function`) shared by a panel UI
and an agent command.

- Basic arithmetic `+ - * / % ^` with correct precedence and unary minus
- Functions: `sin cos tan asin acos atan sinh cosh tanh sqrt cbrt exp ln log log2 abs floor ceil round sign deg rad`, factorial `!`
- Constants: `pi e tau phi`
- Lives in a panel **and** an agent command (`calc`) — same engine for both

> Built on the [BagIdea Office plugin template](https://github.com/bagidea/bagidea-office-template).

---

## Install

```bash
bagidea plugin install https://github.com/bagidea/bagidea-office-calculator-plugin
```

(or paste the URL into the 🧩 **Plugins** panel → **Install**). Remove with
`bagidea plugin remove calculator`.

---

## What to learn from it

| File | The pattern it shows |
|---|---|
| `plugin.json` | A minimal manifest with one agent `command` (`calc`). |
| `index.js` | A plugin that needs **no `ctx`** — pure logic exposed as both an `onCommand` and a custom GET `route` (`/plugin/calculator/eval`). Safe parsing via tokenizer → shunting-yard → RPN. |
| `panel.html` | A flex-column layout that **fills the panel height with no scrollbar**, the office dark theme, and a single `BASE` path that the UI calls. |

Use it for agents:

```bash
curl -s -X POST http://127.0.0.1:8787/plugin/calculator/cmd \
  -H "content-type: application/json" -d '{"cmd":"calc","args":"2*(3+4)^2"}'
# → {"ok":true,"expr":"2*(3+4)^2","result":98,"msg":"2*(3+4)^2 = 98"}
```

---

## Build your own

Fork this (or the [template](https://github.com/bagidea/bagidea-office-template)),
edit the three files, and reload while developing:

```bash
curl -s -X POST http://127.0.0.1:8787/plugins/reload -H "x-bagidea-ui: 1"
```

Full spec — `ctx` API, routes, streaming, agent integration — is in the office
repo: **[docs/guide/plugins.md](https://github.com/bagidea/bagidea-office/blob/main/docs/guide/plugins.md)**.

## License

MIT — see [LICENSE](LICENSE).
