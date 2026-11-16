# Tollgate

See what your Claude sessions cost while you work, and find out whether your model's behavior changed.

**[⬇ Download tollgate.mjs](https://github.com/JNKut/tollgate-download/releases/latest/download/tollgate.mjs)** (one file, about 900 KB, needs Node 20+)

Works with **Cowork**, **Claude Code in the desktop app**, and **Claude Code in the terminal** (or VS Code). Everything runs on your computer: it reads the logs those apps already keep and sends nothing anywhere.

**Regular chats aren't included.** A normal conversation in the Claude app (including research) runs on Anthropic's servers and leaves no log on your computer, so there is nothing for Tollgate to read. It sees work that runs on your computer: Cowork tasks and Claude Code sessions.

**There's one Tollgate window for everything.** You don't open a new one per session: a new session appears in the window after its first reply, and the window follows whichever session you used last. Use the menu at the top to switch between them.

## What you get

A small window next to your work that shows, for whichever session you used last:

- **Context:** how big the conversation has grown. Every turn re-sends all of it.
- **Cache countdown:** how long until the cache expires. After that, the next message re-writes everything and costs much more.
- **Cost:** this prompt, this session, and any helper agents the session started.
- **Advice:** when to start a fresh session or compact, and a warning *before* the cache expires.
- **Model health** (optional): whether the model's behavior changed. See [Model health checks](#model-health-checks-optional).

Costs are API-equivalent prices. On a Pro or Max subscription this is usage, not a bill, but it shows which sessions eat your limits.

## Setup

Pick the app you use. If you use more than one, any setup works: the popup shows sessions from all of them, labelled **Cowork**, **Desktop app**, **Terminal** or **VS Code**.

### Cowork

Cowork runs in its own sandbox and can't install things on your Mac, so this needs Terminal once. You don't need to know Terminal: copy and paste each line, then press Return.

1. **Install Node** (skip if you have it): download the LTS version from [nodejs.org](https://nodejs.org) and run the installer.
2. **Open Terminal** (press ⌘ Space, type *Terminal*, press Return) and paste:
   ```
   curl -L -o ~/tollgate.mjs https://github.com/JNKut/tollgate-download/releases/latest/download/tollgate.mjs
   ```
3. **Start it in the background**, so it keeps running after you close Terminal:
   ```
   nohup node ~/tollgate.mjs > /dev/null 2>&1 &
   ```
   The Tollgate window opens. You can close Terminal now.
4. **Use Cowork as usual.** A task appears in the popup after its first reply. Tasks the app runs on your computer may be labelled **Cowork** or **Desktop app**, depending on your app version. A task that runs as a regular chat won't appear (see above).

### Claude Code in the desktop app

The easy way: **paste this into a Claude Code session in the desktop app** and let Claude do the setup.

> Install Tollgate for me from https://github.com/JNKut/tollgate-download: install Node 20+ if it's missing, download tollgate.mjs to my home folder, and start it in the background with `nohup node ~/tollgate.mjs > /dev/null 2>&1 &`. Show me each command before you run it.

Or do it yourself: follow steps 1–3 under [Cowork](#cowork). They're the same.

The popup is how you see Tollgate here. Desktop-app sessions show up labelled **Desktop app**.

### Claude Code in the terminal (or VS Code)

```
curl -L -o ~/tollgate.mjs https://github.com/JNKut/tollgate-download/releases/latest/download/tollgate.mjs
node ~/tollgate.mjs
```

`node -v` must say 20 or higher ([nodejs.org](https://nodejs.org) if not). `node ~/tollgate.mjs` keeps the popup running while that terminal is open; use the `nohup` line from [Cowork](#cowork) step 3 to run it in the background instead.

**Optional status line**, shown under every reply in the terminal. Run `node ~/tollgate.mjs help` and add the `statusLine` line it prints to `~/.claude/settings.json`. It looks like:

```
Opus 5.5 · ctx 312k · session $16.40 · prompt $0.42 · next turn ≥$0.06 · cache till 14:32
⚠ ctx 312k: /compact or start fresh (each turn re-reads it)
```

## Everyday use

| To... | Do this |
|---|---|
| Reopen the window after closing it | Run `node ~/tollgate.mjs` again. If Tollgate is already running, this just opens a new window. |
| Start it after restarting your Mac | Run the `nohup` line from [Cowork](#cowork) step 3 again |
| Get desktop notifications | Click **Alerts** in the popup |
| Stop it | `pkill -f tollgate.mjs` |
| Update to the newest version | Run the `curl` line again, then stop and start it |

## Model health checks (optional)

These send a fixed set of test prompts to a model twice a day and track whether its answers change: more refusals, broken formats, shorter or ungrounded answers. They need an Anthropic API key from [console.anthropic.com](https://console.anthropic.com). This works the same whichever app you use, because it calls the API directly.

1. Create the key file and open it:
   ```
   mkdir -p ~/.tollgate && touch ~/.tollgate/.env && open -e ~/.tollgate/.env
   ```
   Type `ANTHROPIC_API_KEY=` followed by your key, then save.
2. Run one check:
   ```
   node ~/tollgate.mjs canary
   ```
   The default model is Haiku, at about 0.2¢ per run. To check the model you work with, add `--model claude-opus-5-5` (about 7¢ per run) or `--model claude-sonnet-5-5`.
3. Run it automatically at 9am and 9pm:
   ```
   (crontab -l 2>/dev/null; echo "0 9,21 * * * $(command -v node) $HOME/tollgate.mjs canary --min-gap-hours 5 >> $HOME/.tollgate/canary.log 2>&1") | crontab -
   ```
   Add the same `--model ...` after `canary` if you used one in step 2. Runs are skipped while your Mac is asleep.

After about 11 days of runs, the popup's **Model health** section switches from "learning normal" to **no change** or **drift** for each test suite. It's tuned to about one false alarm per 90 days. If the model that drifted is the one your current session uses, a red banner appears at the top.

- Check the log: `tail ~/.tollgate/canary.log`
- Stop the automatic runs: `crontab -l | grep -v tollgate.mjs | crontab -`

## Notes

- **Where it reads from:** Claude Code (terminal, desktop app, VS Code) logs to `~/.claude/projects`. Cowork keeps each session in the Claude app's data folder (`~/Library/Application Support/Claude/local-agent-mode-sessions` on a Mac). Tollgate only reads these; it never changes them. From Cowork's session files it uses only the session title.
- **Your data** (canary results and your API key) lives in `~/.tollgate`. To remove everything, run `pkill -f tollgate.mjs`, then delete `~/.tollgate` and `~/tollgate.mjs`.
- **Platforms:** tested on macOS. Linux should work. Windows is untested.
- This is early. If anything is confusing or breaks, please say so: that's the most useful thing you can tell me.
