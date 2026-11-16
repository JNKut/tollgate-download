# Tollgate

See what your Claude Code sessions cost while you work, and find out whether your model's behavior changed.

**[⬇ Download tollgate.mjs](https://github.com/JNKut/tollgate-download/releases/latest/download/tollgate.mjs)** (one file, about 900 KB, Mac or Linux, needs Node 20+)

Everything runs on your computer. It reads Claude Code's own logs in `~/.claude/projects` and sends nothing anywhere.

## Setup (about 5 minutes)

1. **Check Node.** Run `node -v` in Terminal. If it's missing or below 20, install the LTS version from [nodejs.org](https://nodejs.org).

2. **Download** the file (the link above) and move it to your home folder, or do both in one step:
   ```
   curl -L -o ~/tollgate.mjs https://github.com/JNKut/tollgate-download/releases/latest/download/tollgate.mjs
   ```

3. **Open the popup:**
   ```
   node ~/tollgate.mjs
   ```
   A small window shows your current session's context size, what it has cost, when the cache expires, and when starting a fresh session would be cheaper. Turn on **Alerts** for desktop notifications.

4. **Optional: the Claude Code status line.** Run `node ~/tollgate.mjs help` and copy the `statusLine` line it prints into `~/.claude/settings.json`.

5. **Optional: model health checks.** These send a fixed set of test prompts to a model and track whether its answers change. They need an Anthropic API key:
   ```
   mkdir -p ~/.tollgate && touch ~/.tollgate/.env && open -e ~/.tollgate/.env
   ```
   Put `ANTHROPIC_API_KEY=sk-ant-...` in that file and save it. Then run one check:
   ```
   node ~/tollgate.mjs canary
   ```
   The default model is Haiku, at about 0.2¢ per run. To check the model you use in Claude Code, add `--model claude-opus-5-5` (about 7¢ per run).

6. **Run the checks automatically at 9am and 9pm:**
   ```
   (crontab -l 2>/dev/null; echo "0 9,21 * * * $(command -v node) $HOME/tollgate.mjs canary --min-gap-hours 5 >> $HOME/.tollgate/canary.log 2>&1") | crontab -
   ```
   After about 11 days of runs, the popup starts reporting **no change** or **drift** for each test suite. It is tuned to about one false alarm per 90 days.
   - Check the log: `tail ~/.tollgate/canary.log`
   - Stop: `crontab -l | grep -v tollgate.mjs | crontab -`

## Notes

- Your data lives in `~/.tollgate`. Delete that folder and `~/tollgate.mjs` to remove everything.
- This is early. If anything is confusing or breaks, please say so: that's the most useful thing you can tell me.
