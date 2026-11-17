# Tollgate

See what your Claude sessions cost while you work, and find out whether your model's behavior changed.

Works with **Cowork**, **Claude Code in the desktop app**, and **Claude Code in the terminal** (or VS Code). Everything runs on your computer: it reads the logs those apps already keep and sends nothing anywhere.

## Two ways to install

| | **[Terminal](#install-from-the-terminal)** | **[Mac app](#install-the-mac-app)** |
|---|---|---|
| Best for | People comfortable with a shell | Everyone else |
| What you get | One file, [`tollgate.mjs`](https://github.com/JNKut/tollgate-download/releases/latest/download/tollgate.mjs), run with `node` | [`Tollgate-mac.zip`](https://github.com/JNKut/tollgate-download/releases/latest/download/Tollgate-mac.zip), an app you double-click |
| Needs | Node 20+ | Nothing; installs Node if missing |
| Platforms | macOS, Linux (Windows untested) | macOS |
| Starts at login | Your choice: `app` turns it on, plain `node ~/tollgate.mjs` doesn't | Yes (turn off in the window) |
| Status line in Claude Code | Yes | Yes |

Both run the same program and read the same data, so you can switch between them at any time.

**Regular chats aren't included.** A normal conversation in the Claude app (including research) runs on Anthropic's servers and leaves no log on your computer, so there is nothing for Tollgate to read. It sees work that runs on your computer: Cowork tasks and Claude Code sessions.

## What you get

A small window next to your work that shows, for whichever session you used last:

- **Context:** how big the conversation has grown. Every turn re-sends all of it.
- **Cache countdown:** how long until the cache expires. After that, the next message re-writes everything and costs much more.
- **Cost:** this prompt, this session, and any helper agents the session started.
- **Advice:** when to start a fresh session or compact, and a warning *before* the cache expires.
- **Plan limits** (Pro and Max): how much of your 5-hour and weekly limit you've used, when each resets, and a warning when you'll run out at your current pace. See [Plan limits](#plan-limits-pro-and-max).
- **Model hint** (optional): before a prompt is sent, a tip when Sonnet would likely do it for about half the cost, learned from your own past prompts. See [Model hint](#model-hint-optional).
- **Switch guard** (optional): when you `/model` mid-session, what the switch costs and after how many turns it pays off, right in Claude Code's "Switch model?" question. See [Switch guard](#switch-guard-optional).
- **Model health** (optional): whether the model's behavior changed. See [Model health checks](#model-health-checks-optional).

Costs are API-equivalent prices. On a Pro or Max subscription this is usage, not a bill, but it shows which sessions eat your limits.

There's **one Tollgate window for everything**: a new session appears after its first reply, labelled **Cowork**, **Desktop app**, **Terminal** or **VS Code**, and the window follows whichever one you used last. Use the menu at the top to switch.

## Install from the terminal

Needs Node 20+ (`node -v`; get it from [nodejs.org](https://nodejs.org) or your package manager).

```sh
curl -L -o ~/tollgate.mjs https://github.com/JNKut/tollgate-download/releases/latest/download/tollgate.mjs
node ~/tollgate.mjs          # starts the server and opens the window; Ctrl+C stops it
```

That's all. Nothing else gets installed, and nothing runs when you're not running it. If you'd rather have it always on:

```sh
node ~/tollgate.mjs app      # start at login (a LaunchAgent on macOS, an autostart entry on Linux), then open the window
```

**Commands:**

| Command | What it does |
|---|---|
| `node ~/tollgate.mjs` | Open the window. Starts the server first if it isn't already running. Options: `--port 7457`, `--no-open` |
| `node ~/tollgate.mjs app` | Start at login, make sure the running copy is this version, and open the window |
| `node ~/tollgate.mjs statusline` | Status line for Claude Code (see below) |
| `node ~/tollgate.mjs canary` | Run the model health checks once (see [below](#model-health-checks-optional)) |
| `node ~/tollgate.mjs analyze` | Print the drift report for your checks |
| `node ~/tollgate.mjs hint tip` | Turn on the [model hint](#model-hint-optional): a tip before sending when a cheaper model would do. `hint ask` holds the prompt once instead; `hint off` turns it off |
| `node ~/tollgate.mjs fit` | What your last week of prompts says about which model to use |
| `node ~/tollgate.mjs guard on` | Turn on the [switch guard](#switch-guard-optional): the cost of a mid-session `/model` switch, before you confirm it. `guard off` turns it off |
| `node ~/tollgate.mjs uninstall` | Stop it, and remove its login item, scheduled checks, model hint and switch guard |
| `node ~/tollgate.mjs feedback` | Open a GitHub issue to report a problem or suggest something |
| `node ~/tollgate.mjs help` | All of the above, with the exact paths on your machine |

**To update:** run the `curl` line again. If it's running in the background, follow with `node ~/tollgate.mjs app`, which replaces the running copy.

**Status line**, shown under every reply in Claude Code in the terminal. Add this to `~/.claude/settings.json`; `node ~/tollgate.mjs help` prints it with your path filled in:

```json
"statusLine": { "type": "command", "command": "node ~/tollgate.mjs statusline" }
```

It looks like:

```
Opus 5.5 · ctx 312k · session $16.40 · prompt $0.42 · next turn ≥$0.06 · cache till 14:32 · 5h 62% → 15:40 · wk 18%
⚠ ctx 312k: /compact or start fresh (each turn re-reads it)
```

The `5h` and `wk` parts are your plan limits; they appear on Pro and Max after the first reply.

**Let Claude Code install it:** in the desktop app or the terminal, paste:

> Install Tollgate for me from https://github.com/JNKut/tollgate-download: install Node 20+ if it's missing, download tollgate.mjs to my home folder, then run `node ~/tollgate.mjs app`. Show me each command before you run it.

Cowork can't do this: it runs in a sandbox separate from your computer. Use the Mac app, or run the two lines above in Terminal.

## Install the Mac app

1. **[Download Tollgate-mac.zip](https://github.com/JNKut/tollgate-download/releases/latest/download/Tollgate-mac.zip)** and open it. Drag **Tollgate** into your **Applications** folder.
2. **Double-click Tollgate.** The first time, macOS says it can't verify the app. That's because it isn't from the App Store or signed with a paid Apple developer account. To allow it, once:
   - Click **Done** in that message.
   - Open **System Settings → Privacy & Security** and scroll down to *"Tollgate" was blocked*.
   - Click **Open Anyway**, then confirm with your password or Touch ID.
3. **The Tollgate window opens.** The first time can take a minute if it needs to download Node.js, which Tollgate runs on; a notification tells you. Tollgate then starts by itself whenever you log in.

The app puts the same `~/tollgate.mjs` in your home folder, so every terminal command above works too.

| To... | Do this |
|---|---|
| Open the window | Double-click Tollgate, or press ⌘ Space and type *Tollgate* |
| Get desktop notifications | Click **Alerts** in the window |
| Stop it starting at login | Click **turn off** at the bottom of the window |
| Quit it | Click **quit Tollgate** at the bottom of the window |
| Update | Download the zip again, replace Tollgate in Applications, and open it. It replaces the running copy. |

## Plan limits (Pro and Max)

The window's **Plan limits** section shows how much of your 5-hour and weekly limits you've used and when each resets. If you keep going at your current pace and would run out before the reset, it tells you when: *"At this pace you'll hit it around 14:20."* With **Alerts** on, you get a notification at 80%, 95% and 100%.

Where the numbers come from (both are already on your computer; Tollgate asks no one):

- **Claude Code in the terminal**, with Tollgate's [status line](#install-from-the-terminal) set up: exact and live. Claude Code passes the limits to the status line after every reply, and Tollgate saves them so the window can show them too.
- **Otherwise, the Claude app**, which saves a reading now and then (often hours apart), so the window shows how old it is. This covers Cowork and the desktop app.

The limits are shared across Claude (chats, Cowork and Claude Code), so either reading covers everything you do. API-key users have no plan limits, and the section doesn't appear.

## Model hint (optional)

Picking the model is the biggest lever on cost and limits, but it has to happen *before* you send. Tollgate can check each prompt just before Claude Code sends it:

```
UserPromptSubmit says: Tollgate: Sonnet would likely do this for about half the cost
(14 of your 16 similar prompts were quick). Type /model sonnet to switch.
```

- **Tip** (`node ~/tollgate.mjs hint tip`, or **Tip before sending** in the window's Model fit section): the prompt goes through and the tip shows under it, so you can switch for the next ones.
- **Ask first** (`hint ask`): the prompt is held back once and put back in the box. Type `/model sonnet` first if you want, then press Enter. Pressing Enter sends it unchanged either way.
- **Off** (`hint off`).

How it decides:

- **From your own history:** it learns which kinds of prompts were quick for *you* (at most 3 steps, a short answer, no helper agents) from your Claude Code logs on this computer, and relearns every 6 hours. It only ever suggests a cheaper model. Haiku is suggested only when your history shows one-step answers for that kind of prompt.
- **Only when switching pays off:** each model has its own cache, so switching in the middle of a session makes the new model re-read the whole conversation at full price. Claude Code starts a session at about 35–70k tokens, so the hint mostly speaks up on a session's **first prompt** or after a long break, when switching costs nothing extra. Mid-session it stays quiet unless the switch pays for itself within a few turns.
- **It checks itself:** after each tip it looks at what the prompt turned into. If it's right less than 60% of the time for you, it goes quiet, and the window says so.

What it changes and saves:

- **Your Claude Code settings:** it adds one hook to `~/.claude/settings.json` and saves a backup next to it first (`settings.json.tollgate-backup`). It keeps your other settings and hooks.
- **Your prompts are read, never saved:** each prompt is read on your computer at the moment you send it. Saved are only a verb from a fixed list (like "rename" or "design"), a size bucket and yes/no flags, in `~/.tollgate/modelfit.json` and `hints.jsonl`.
- **It needs Claude Code:** tested in the terminal. Claude Code in the desktop app and VS Code reads the same settings, but that's untested. Cowork and regular chats don't run hooks.
- **It can't switch for you:** Claude Code doesn't let a hook change the model, so you type `/model`.
- **Turn on the [status line](#install-from-the-terminal) too:** it tells the hint which model each session is on, including on its first prompt.

## Switch guard (optional)

Switching to a cheaper model in the middle of a session usually costs more than it saves. Each model has its own prompt cache, so the new model has to write the whole conversation into its cache first. Claude Code already asks "Switch model?" when that happens, but it doesn't tell you what it costs. With the guard on, the question has the numbers:

```
Switch model?
A PreModelSwitch hook asked you to confirm
Tollgate: Switching to Sonnet now costs ~72¢ extra (API prices).
Sonnet has to re-cache all 190k tokens of this conversation.
Then each quick turn is ~6¢ cheaper: pays off after ~13 turns.
Cheaper: finish this task here, then /clear and switch.
❯ 1. Yes, switch to Sonnet 5.5
  2. No, go back
```

- **Turn on:** `node ~/tollgate.mjs guard on`, or **Turn on** next to Switch guard in the window's Model fit section. `guard off` turns it off.
- **Cheap switches go straight through:** when switching costs under 5¢ extra, or pays off within 3 turns, it skips the question.
- **Cache already expired:** switching then costs nothing extra. Claude Code doesn't ask, and neither does the guard.
- **Prices:** the numbers are at API list prices. On Pro and Max you don't pay per token, but the same re-caching comes out of your plan limits.
- **Where it works:** `/model` and the model picker in Claude Code in the terminal. Switches from VS Code, the desktop app or the SDK reach Claude Code another way, where it can't show a question (it would refuse the switch instead), so the guard leaves those alone.
- **What it changes and saves:** one hook in `~/.claude/settings.json`, backed up first. In `~/.tollgate/switches.jsonl` it notes each switch it answered: when, which models, context size and cost. Never your prompts.

## Model health checks (optional)

These send a fixed set of test prompts to a model twice a day and track whether its answers change: more refusals, broken formats, shorter or ungrounded answers. They need an Anthropic API key from [console.anthropic.com](https://console.anthropic.com) (API keys → Create key). They work the same whichever Claude app you use.

- **Which model:** Haiku costs about 0.2¢ a check. Opus about 7¢ (roughly $1 a week at two checks a day). Pick the one you work with if you want to know when *it* changes.
- **When checks run:** at 9:00 and 21:00. If your computer is asleep, a check runs once when it wakes (macOS).
- **Where the key goes:** only on your computer, in `~/.tollgate/.env`, readable only by you.

**Set up in the window** (either install): under *Set up model health checks*, paste the key, pick a model, and click **Start checks**. The key is checked with Anthropic first. The first check runs right away. Use **change** or **stop** under Model health later.

**Or set up from the terminal:**

```sh
# save the key without it landing in your shell history (typing is hidden)
(umask 077; mkdir -p ~/.tollgate; printf 'Anthropic API key: '; stty -echo; read -r k; stty echo; echo; printf 'ANTHROPIC_API_KEY=%s\n' "$k" > ~/.tollgate/.env)
node ~/tollgate.mjs canary                      # Haiku by default; add --model claude-opus-5-5 or claude-sonnet-5-5
(crontab -l 2>/dev/null; echo "0 9,21 * * * $(command -v node) $HOME/tollgate.mjs canary --min-gap-hours 5 >> $HOME/.tollgate/canary.log 2>&1") | crontab -
```

- Each run has a hard cap of $0.25 (`--budget` to change it).
- `--min-gap-hours 5` skips a run, at no cost, if the model was checked recently. So a cron line alongside checks set up in the window won't double-spend.
- Check the log: `tail ~/.tollgate/canary.log`.
- Remove the cron line: `crontab -l | grep -v tollgate.mjs | crontab -`.

After about 11 days of checks, the **Model health** section switches from "learning normal" to **no change** or **drift** for each test suite. `node ~/tollgate.mjs analyze` prints the same report. It's tuned to about one false alarm per 90 days. If the model that drifted is the one your current session uses, a red banner appears at the top.

## Notes

- **Where it reads from:** Claude Code (terminal, desktop app, VS Code) logs to `~/.claude/projects` (or `$CLAUDE_CONFIG_DIR/projects`). Cowork keeps each session in the Claude app's data folder (`~/Library/Application Support/Claude/local-agent-mode-sessions` on a Mac; override with `TOLLGATE_COWORK_DIR`). Tollgate only reads these; it never changes them. From Cowork's session files it uses only the session title.
- **What it writes:**
  - `~/tollgate.mjs`: the program.
  - `~/.tollgate`: your data and settings. Override the location with `TOLLGATE_HOME`. If the Mac app downloaded Node.js, it's here too.
  - Hooks in `~/.claude/settings.json`, only if you turned on the model hint or the switch guard (backup saved next to it).
  - Login items, only if you used `app`, the Mac app, or set up checks in the window. On macOS: `com.tollgate.app.popup` and `com.tollgate.app.canary` in `~/Library/LaunchAgents`. On Linux: `~/.config/autostart/tollgate.desktop` and a crontab line.
- **Uninstall:** run `node ~/tollgate.mjs uninstall`, then delete `~/.tollgate`, `~/tollgate.mjs`, and Tollgate in Applications if you have the app.
- **Network:** the window is served on `127.0.0.1` only. The only outbound requests are the model health checks to Anthropic, and the Mac app's one-time Node.js download from nodejs.org, checksum-verified. Tollgate collects nothing about you.
- **Platforms:** tested on macOS. Linux should work. Windows is untested and has no automatic checks yet.
- **Plan limits** are read from what Claude Code passes to the status line (saved in `~/.tollgate/limits.jsonl`, last 8 days) and from the Claude app's own `plan-usage-history.json`.
- This is early. If anything is confusing or breaks, please say so: click **Send feedback** at the bottom of the window, or run `node ~/tollgate.mjs feedback`. Either one opens a GitHub issue with your Tollgate version, system and which features are on (no file paths, names, prompts or costs). You can read and edit it, and nothing is sent unless you press Submit.
