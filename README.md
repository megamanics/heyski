# heyski

> "tell me when it's done."
> "i will."

native macOS notifications from your terminal. one command. it taps you on the
shoulder when the long thing finishes, and clicking it jumps you back to where
you were.

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

prefer no homebrew? grab the latest `.zip` from
[releases](../../releases), unzip it into `/Applications`, and put the binary
at `HeySKi.app/Contents/MacOS/heyski` on your `PATH`.

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

---

## the rules

- it greets you by your name, because it knows who you are. it is not creepy
  about it.
- no subtitle unless you ask for one with `--from`. quiet by default.
- one binary. it does notifications. that is the whole job.

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
