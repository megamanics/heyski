# heyski

> "tell me when it's done."
> "i will."
> "and when the agent wants to nuke prod?"
> "...i'll ask you first."

native macOS notifications from your terminal. one command. it taps you on the
shoulder when the long thing finishes, clicking it jumps you back to where you
were — and when an AI agent needs a human, it asks *you*, on *your* mac, and
takes no for an answer.

no daemon. no menu bar clutter. no electron. it shows up, says its piece, and
gets out of your way.

---

## install

```bash
brew install --cask megamanics/tap/heyski
```

then prove it works:

```bash
heyski "it works"
```

a banner slides in. you feel powerful. that is the whole pitch.

prefer no homebrew? grab the latest `.zip` from [releases](../../releases),
unzip it into `/Applications`, and put `HeySKi.app/Contents/MacOS/heyski` on
your `PATH`.

---

## the menu

```bash
heyski "build finished"                 # the quick way: message as a word
heyski -m "deploy done"                 # explicit, if you like ceremony
echo "tests passed" | heyski            # from a pipe
heyski -m "pr merged" --from "CI"       # add a source label
heyski -m "open the dashboard" --open https://example.com   # click does something
```

| flag | what it does |
|------|--------------|
| `-m`, `--message` | the body. also takes a positional word or stdin. |
| `-t`, `--title` | override the "Hey &lt;you&gt;" greeting |
| `--from` | a source label, e.g. "CI" |
| `--sound [name]` | make noise. bare = default, or a name like Glass |
| `-g`, `--group` | stack related notifications together |
| `--open <url\|path>` | click opens a url or file |
| `--execute <cmd>` | click runs a command |
| `--no-activate` | click does nothing |
| `--actions <a,b,c>` | buttons; prints the label you click |
| `-r`, `--reply [text]` | a reply field; prints what you type |
| `--timeout <secs>` | auto-close an interactive alert |
| `--json` | print the result as a structured event object |
| `--approve` | Approve/Deny gate; prints a fail-closed approval receipt |
| `heyski mcp` | speak MCP over stdio, for AI agents |

typo a flag and it fixes it for you, then prints the corrected command so your
script can learn.

---

## real use cases

### 1. the long build you keep babysitting

you start a build. you switch to read the news. twenty minutes later you
remember the build exists.

```bash
make release && heyski "release built" || heyski "release broke"
```

now the build tells you. go read the news in peace.

### 2. the step that finishes whenever it feels like it

```bash
./deploy.sh; heyski -m "deploy exited $?" --from "deploy"
```

works whether it succeeds or fails. the exit code rides along.

### 3. the click that takes you back to work

```bash
long-thing; heyski -m "done — open the folder" --open ~/work
```

click the banner, the folder opens, you are back in it. notifications that
respect your time.

### 4. ask a question, act on the answer

heyski can do more than tell you things. it can ask. `--actions` gives you
buttons and prints the one you click; `--reply` gives you a text field and
prints what you type.

```bash
case "$(heyski -m "deploy to prod now?" --actions "Yes,No,Later" --timeout 30)" in
  Yes)   ./deploy.sh prod ;;
  Later) at now + 1 hour <<< './deploy.sh prod' ;;
  *)     echo "skipped" ;;
esac
```

the job doesn't just finish. it asks what to do next, and you answer from the
banner without touching the terminal.

### 5. the human gate for your AI agent

hand a terminal to an autonomous agent and eventually it tries something stupid.
a poisoned readme tells it to `rm -rf` the prod bucket and it says sure. the
agent's words can lie. its logs can lie. your thumb on a banner cannot.

```bash
$ heyski --approve -m "delete 14 prod buckets?" --policy delete-prod --timeout 60
{"decision":"denied","sentinel":"@TIMEOUT","type":"approval", ...}
```

nobody clicked, so the answer is no. deny, dismiss, time out — all of it means
no. the only `"approved"` comes from you, physically tapping **Approve**. every
receipt lands in `~/Library/Application Support/heyski/receipts.jsonl`, so "prove
a human approved that" is a folder of receipts, not a shrug.

and it speaks **MCP**, so any agent can call it. drop this into your agent's mcp
config — claude desktop, copilot, cursor all read the same shape:

```json
{
  "mcpServers": {
    "heyski": { "command": "heyski", "args": ["mcp"] }
  }
}
```

three tools show up: `notify_human` (tap me on the shoulder), `ask_human` (ask
me something, wait for the answer), and `request_approval` (the gate above,
returned as a receipt). the agent can be as autonomous as it likes. the human
gate is the one door it cannot pick.

---

## the rules

- it greets you by your name, because it knows who you are. it is not creepy
  about it.
- no subtitle unless you ask for one with `--from`. quiet by default.
- when it asks permission, silence means no. fail-closed, every time.
- one binary. notifications, questions, and approvals. that is the whole job.

---

## first run

macOS is cautious about apps it has not met. if the first launch gets a stern
look, open `HeySKi.app` from your Applications folder once (right‑click → Open),
say yes, and you will never think about it again.

if macOS still blocks it after unzip, clear quarantine recursively (the
attribute can sit on files inside the bundle, not just the `.app` folder):

```bash
xattr -dr com.apple.quarantine /Applications/HeySKi.app
```

requires macOS 11 (Big Sur) or later.
