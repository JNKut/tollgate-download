# Tollgate

See what your Claude sessions cost while you work, and find out whether your model's behavior changed.

**[⬇ Download for Mac](https://github.com/JNKut/tollgate-download/releases/latest/download/Tollgate-mac.zip)**: an app you double-click, no Terminal needed.

Linux, or prefer Terminal? Use the [single file](#without-the-app-terminal-or-linux) instead.

Works with **Cowork**, **Claude Code in the desktop app**, and **Claude Code in the terminal** (or VS Code). Everything runs on your computer: it reads the logs those apps already keep and sends nothing anywhere.

**Regular chats aren't included.** A normal conversation in the Claude app (including research) runs on Anthropic's servers and leaves no log on your computer, so there is nothing for Tollgate to read. It sees work that runs on your computer: Cowork tasks and Claude Code sessions.

## What you get

A small window next to your work that shows, for whichever session you used last:

- **Context:** how big the conversation has grown. Every turn re-sends all of it.
- **Cache countdown:** how long until the cache expires. After that, the next message re-writes everything and costs much more.
- **Cost:** this prompt, this session, and any helper agents the session started.
- **Advice:** when to start a fresh session or compact, and a warning *before* the cache expires.
- **Model health** (optional): whether the model's behavior changed. See [Model health checks](#model-health-checks-optional).

Costs are API-equivalent prices. On a Pro or Max subscription this is usage, not a bill, but it shows which sessions eat your limits.

There's **one Tollgate window for everything**: a new session appears after its first reply, labelled **Cowork**, **Desktop app**, **Terminal** or **VS Code**, and the window follows whichever one you used last. Use the menu at the top to switch.

## Setup on a Mac

1. **[Download Tollgate-mac.zip](https://github.com/JNKut/tollgate-download/releases/latest/download/Tollgate-mac.zip)** and open it. Drag **Tollgate** into your **Applications** folder.
2. **Double-click Tollgate.** The first time, macOS says it can't verify the app. That's because it isn't from the App Store or signed with a paid Apple developer account. To allow it, once:
   - Click **Done** in that message.
   - Open **System Settings → Privacy & Security** and scroll down to *"Tollgate" was blocked*.
   - Click **Open Anyway**, then confirm with your password or Touch ID.
3. **The Tollgate window opens.** The first time can take a minute if it needs to download Node.js, which Tollgate runs on; a notification tells you. Tollgate then starts by itself whenever you log in.

To open the window again later, double-click Tollgate, or press ⌘ Space and type *Tollgate*.

**Optional for Claude Code in the terminal:** a status line under every reply. Run `node ~/tollgate.mjs help` and add the `statusLine` line it prints to `~/.claude/settings.json`. It looks like:

```
Opus 5.5 · ctx 312k · session $16.40 · prompt $0.42 · next turn ≥$0.06 · cache till 14:32
⚠ ctx 312k: /compact or start fresh (each turn re-reads it)
```

## Everyday use

| To... | Do this |
|---|---|
| Open the window | Double-click Tollgate |
| Get desktop notifications | Click **Alerts** in the window |
| Stop it starting at login | Click **turn off** at the bottom of the window |
| Quit it | Click **quit Tollgate** at the bottom of the window |
| Update | Download the zip again, replace Tollgate in Applications, and open it. It replaces the running copy. |

## Model health checks (optional)

These send a fixed set of test prompts to a model twice a day and track whether its answers change: more refusals, broken formats, shorter or ungrounded answers. They need an Anthropic API key from [console.anthropic.com](https://console.anthropic.com) (API keys → Create key). They work the same whichever Claude app you use.

**In the Tollgate window:** under *Set up model health checks*, paste the key, pick a model, and click **Start checks**.

- **Which model:** Haiku costs about 0.2¢ a check. Opus about 7¢ (roughly $1 a week at two checks a day). Pick the one you work with if you want to know when *it* changes.
- **What happens:** the first check runs right away. Then checks run at 9:00 and 21:00. If your Mac is asleep, the check runs once when it wakes.
- **Your key** is checked with Anthropic, then saved only on your computer, in `~/.tollgate/.env`, readable only by you.

After about 11 days of checks, the **Model health** section switches from "learning normal" to **no change** or **drift** for each test suite. It's tuned to about one false alarm per 90 days. If the model that drifted is the one your current session uses, a red banner appears at the top.

To change the model or stop checks, use **change** or **stop** under Model health.

## Without the app (Terminal, or Linux)

Needs Node 20+ ([nodejs.org](https://nodejs.org)). Then:

```
curl -L -o ~/tollgate.mjs https://github.com/JNKut/tollgate-download/releases/latest/download/tollgate.mjs
node ~/tollgate.mjs app
```

`app` sets Tollgate to start at login and opens the window. On Linux it uses an autostart entry; model health checks set up in the window use a crontab line. Run `node ~/tollgate.mjs` any time to open the window again. To update, run the `curl` line again, then `node ~/tollgate.mjs app`.

**Claude Code in the desktop app** can also do this for you. Paste this into a session:

> Install Tollgate for me from https://github.com/JNKut/tollgate-download: install Node 20+ if it's missing, download tollgate.mjs to my home folder, then run `node ~/tollgate.mjs app`. Show me each command before you run it.

Cowork can't install it, because Cowork runs in a sandbox separate from your computer. Use the Mac app.

## Notes

- **Where it reads from:** Claude Code (terminal, desktop app, VS Code) logs to `~/.claude/projects`. Cowork keeps each session in the Claude app's data folder (`~/Library/Application Support/Claude/local-agent-mode-sessions` on a Mac). Tollgate only reads these; it never changes them. From Cowork's session files it uses only the session title.
- **What it installs:**
  - `~/tollgate.mjs`: the program.
  - `~/.tollgate`: your data and settings, plus Node.js if the app downloaded it.
  - Two login items in `~/Library/LaunchAgents`: one starts Tollgate, the other runs the checks.
- **Uninstall:** in Terminal, run `node ~/tollgate.mjs uninstall`. It stops Tollgate and removes the login items. Then delete Tollgate from Applications, plus `~/.tollgate` and `~/tollgate.mjs`.
- **Platforms:** tested on macOS. Linux should work. Windows is untested and has no automatic checks yet.
- This is early. If anything is confusing or breaks, please say so: that's the most useful thing you can tell me.
