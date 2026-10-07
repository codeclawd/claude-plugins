# Setting up desktop-statusline in T3 Code (instructions for coding agents)

The user wants the `desktop-statusline` Claude Code mod in the T3 Code desktop app on their Mac. T3
Code doesn't draw Claude Code plugin UI by itself, so this takes two parts:

1. **t3-mods** (https://github.com/codeclawd/t3-mods) patches T3 Code so it draws plugin UI, and
   re-applies the patch after T3 updates.
2. **desktop-statusline** from this repo's marketplace (`t3-desktop`).

Follow the steps in order. Stop and report to the user if a check fails; don't work around it.

## 1. Check requirements

| Check | Command | Expect |
|---|---|---|
| macOS | `uname -s` | `Darwin` |
| Node.js 24+ | `node -v` | `v24` or higher. If lower, tell the user; don't upgrade Node for them |
| git | `git --version` | a version. If missing, the user runs `xcode-select --install` |
| Claude Code 2.1.287+ | `claude --version` | 2.1.287 or newer (mods need it) |
| T3 Code installed | `ls -d /Applications/T3\ Code*.app` | at least one app; if several, ask which one they use |

## 2. Install t3-mods

```sh
git clone https://github.com/codeclawd/t3-mods.git ~/t3-mods
~/t3-mods/bin/t3-mods install          # add --app "/Applications/T3 Code (Alpha).app" for another build
```

The first run clones T3 Code and builds the patch, which takes a few minutes. Success ends with
`Agent installed (com.t3-mods.agent)`. If `~/t3-mods` already exists, run `git -C ~/t3-mods pull`
and skip the clone.

If the output includes a `security set-key-partition-list` command, give it to the user to run in
their own Terminal (it asks for their Mac login password; never ask for the password yourself).
After they run it, run `~/t3-mods/bin/t3-mods sign-setup` and expect `Signing key ready`. Don't run
`codesign` yourself before that: macOS would ask once per signed file.

## 3. Install desktop-statusline

```sh
claude plugin marketplace add codeclawd/claude-plugins
claude plugin install desktop-statusline@t3-desktop --config cache_ttl=1h
```

`cache_ttl` is how long the user's prompt cache lives: `1h` on a Claude subscription within plan
usage, `5m` with an API key, a cloud provider, or usage credits. Ask if you can't tell.

If the user also runs another plugin that draws plan-usage meters above the prompt, ask whether to
keep both; two meter bands show the same numbers.

## 4. Load it

T3 Code must restart once to load the patch. **Don't quit or restart T3 yourself.** You are
probably running inside T3, and closing it ends your session and any other agent the user has
running. Tell the user:

> Everything is installed. Quit T3 Code (⌘Q) when it suits you and reopen it after about 15
> seconds; t3-mods applies the patch while it's closed. If macOS asks to let T3 read its keychain
> item or a folder, click Allow. Then send a prompt in a new thread: the status band appears above
> the prompt once that thread's Claude session starts.

Only if the user asks you to restart T3 for them, run `~/t3-mods/bin/t3-mods apply 20`, tell them
not to reopen T3 themselves, and end your turn.

## 5. Verify

After the user has restarted T3 and you have a fresh session:

```sh
~/t3-mods/bin/t3-mods status
claude plugin list | grep desktop-statusline
```

Expect `state: patched (...)`, `agent: loaded`, and the plugin listed as enabled. If `status`
shows `state: official` and `~/.t3-mods/agent.log` says "macOS refused to write", the user opens
System Settings > Privacy & Security > App Management, clicks +, presses Cmd+Shift+G, adds
`/bin/bash`, then quits and reopens T3 again.

## Undo

```sh
claude plugin uninstall desktop-statusline@t3-desktop
~/t3-mods/bin/t3-mods uninstall      # restores the official T3 app; restarts T3, so ask first
```
