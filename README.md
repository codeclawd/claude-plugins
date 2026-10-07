# claude-plugins for T3 Code

A fork of [centminmod/claude-plugins](https://github.com/centminmod/claude-plugins) set up for the
[T3 Code](https://github.com/pingdotgg/t3code) desktop app on macOS. Its
[`desktop-statusline`](plugins/desktop-statusline) mod draws a status band above the prompt (git
state, context meter, session cost, 5-hour and weekly limit meters, last-turn stats) in T3 Code,
which needs [t3-mods](https://github.com/codeclawd/t3-mods) to draw Claude Code plugin UI.

**Set it up with one prompt.** Give your coding agent this repo and say:

> Set up desktop-statusline in T3 Code by following AGENTS.md in https://github.com/codeclawd/claude-plugins

[AGENTS.md](AGENTS.md) installs t3-mods, adds this marketplace (`t3-desktop`) and the plugin, and
then asks you to restart T3 Code yourself.

By hand:

```sh
git clone https://github.com/codeclawd/t3-mods.git ~/t3-mods && ~/t3-mods/bin/t3-mods install
claude plugin marketplace add codeclawd/claude-plugins
claude plugin install desktop-statusline@t3-desktop --config cache_ttl=1h
```

Then quit and reopen T3 Code. The plugins are George Liu's, unchanged; this fork adds the T3 setup
and renames the marketplace to `t3-desktop` so it doesn't clash with the original `centminmod`.
The upstream README follows.

---

# centminmod / claude-plugins

- Site: <https://ai.georgeliu.com/p/claude-plugins>
- Article: <https://ai.georgeliu.com/p/my-claude-code-plugin-marketplace>

A personal Claude Code plugin marketplace. Each plugin is an independent, standalone unit that can be installed into any Claude Code project with a single command.

> Claude Code's plugin system supports multiple plugins per marketplace and handles install + update lifecycle automatically. See the [official plugin-marketplaces docs](https://code.claude.com/docs/en/plugin-marketplaces) for the broader ecosystem.

## Install the marketplace

Add this marketplace once, then install any plugin from it:

> **Run `/plugin` commands inside the Claude Code terminal CLI**
> (`claude` in your shell). They are not recognised in the desktop
> app, claude.ai/code, or IDE extensions — you'll see *"/plugin isn't
> a recognized command here"* if you try. Installed plugins then work
> from every surface; only the install step requires the CLI.

```
/plugin marketplace add centminmod/claude-plugins
```

## Available plugins

| Plugin | Description | Install |
|--------|-------------|---------|
| [`session-metrics`](plugins/session-metrics) | Per-turn token, cost, and cache metrics for Claude Code sessions. Multi-format export (text/JSON/CSV/MD/HTML) with 5-hour session blocks, weekly roll-up, hour-of-day punchcard, and pluggable chart libraries. | `/plugin install session-metrics@centminmod` |
| [`desktop-statusline`](plugins/desktop-statusline) | A mod that draws a status band above the prompt in the Claude Desktop app's Code tab, where the CLI `statusLine` doesn't run. Git state, context meter, session cost, 5-hour and weekly usage limits with reset countdowns, last-turn stats, and running agents. Needs Claude Code v2.1.287+. | `/plugin install desktop-statusline@centminmod` |

More plugins coming.

### Further reading — `session-metrics`

Background articles by the author on what the skill does and what it
surfaces in practice:

- [My Claude Code Plugin Marketplace Is Now Public. Install Session Metrics Skill Plugin](https://ai.georgeliu.com/p/my-claude-code-plugin-marketplace).
- [I built a token-cost analyzer skill for Claude Code](https://ai.georgeliu.com/p/i-built-a-token-cost-analyzer-skill) — how the skill was designed and what it reports.
- [I ran two Claude Opus 4.7 5-hour sessions](https://ai.georgeliu.com/p/i-ran-two-claude-opus-47-5hr-sessions) — real-world session-metrics output from two back-to-back Opus 4.7 sessions, with cache-hit, cost, and token-usage analysis.

## How the skill gets triggered

Plugin skills are namespaced as `plugin-name:skill-name` — for example
`/session-metrics:session-metrics`. In practice you rarely type that form:
each skill declares natural-language triggers in its `SKILL.md`, so Claude
Code auto-invokes the right one when you ask something like *"how much has
this session cost?"*.

## Licences

- Marketplace scaffold: MIT (see [`LICENSE`](LICENSE)).
- Each plugin under `plugins/*/` carries its own `LICENSE` and may bundle
  third-party assets under their upstream licences. For `session-metrics`
  specifically, the default Highcharts renderer ships under a
  non-commercial-free licence — commercial use requires a paid Highsoft
  licence. MIT-licensed alternatives (uPlot, Chart.js) are selectable via
  the `--chart-lib` flag. See
  [`plugins/session-metrics/skills/session-metrics/scripts/vendor/charts/README.md`](plugins/session-metrics/skills/session-metrics/scripts/vendor/charts/README.md)
  for per-library LICENSE.txt files.

## Contributing

This is a personal marketplace — issues and pull requests are welcome but
plugin additions are curated. Feel free to open an issue to discuss
bundling something new.

## Related

- [centminmod/my-claude-code-setup](https://github.com/centminmod/my-claude-code-setup) — personal Claude Code config template (bundles the same skills for direct copy)
