# Smart Lazy Git (҂◡_◡) ᕤ

Tiny experiment to explore Git and automate commits — locally or via GitHub Actions.  
It can make empty commits, set custom commit dates, and keep your contribution graph “green”.

If you liked my older project (Lazy-Git), this is the smarter version: it’s simpler to run and can fully automate commits on the server.

## What it does

- Makes empty commits (`--allow-empty`)
- Overwrites commit timestamps (author/committer) to a value you pick
- Runs locally (Python + Git) or on GitHub Actions (cron)
- Useful for learning how Git works, testing CI, or keeping a heartbeat in a repo

## How it works (quick peek)

- Local: `main.py` sets `GIT_AUTHOR_DATE` and `GIT_COMMITTER_DATE` then runs `git commit --allow-empty`.
- Server: `.github/workflows/auto-commit.yml` commits on a schedule and pushes back.

## Requirements

- Git installed
- Python 3.9+ (for local mode)
- A GitHub repo with Actions enabled (for server mode)

## Quick start

### 1) Local mode (Python)

- Configure your Git identity (once per machine):
  ```
  git config user.name "Your Name"
  git config user.email "you@example.com"
  ```
- Open `main.py` and change the `commit_time` if you want:
  ```py
  commit_time = datetime(2025,10,17,8,0,0)  # year, month, day, hour, minute, second
  ```
  The script will format it as `+07:00`. Adjust if your timezone is different.
- Run it from the repo root:
  ```
  python main.py
  ```
- Push if needed:
  ```
  git push
  ```

> [!TIP]
> Tip: want a “heartbeat” file instead of empty commits? Replace the command with something like:
```
echo %date% %time% >> heartbeat.txt
git add heartbeat.txt
git commit -m "Heartbeat"
```

### 2) Server mode (GitHub Actions)

This repo includes `.github/workflows/auto-commit.yml`.

- Push this repo to GitHub.
- In your repo: Settings → Actions → General → Workflow permissions → enable “Read and write permissions”.
- The workflow runs on a schedule:
  ```
  - cron: '0 1,9 * * *'    # 1:00 & 9:00 UTC = 08:00 & 16:00 Jakarta
  - cron: '0 4,5 * * *'    # test runs at 11:00 & 12:00 Jakarta
  ```
  Edit those `cron` lines to your taste. Cron uses UTC. The message uses `TZ=Asia/Jakarta` for readability.

Optional: if commits fail due to identity, add these before the commit step:
```
git config user.name "github-actions[bot]"
git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
```

## Customizing

- Commit message:
  - Local: edit the message in `main.py`
  - Actions: edit the message in `auto-commit.yml`
- Timezone:
  - Local: change the `+07:00` part in `strftime` or compute using your tz
  - Actions: change `TZ=Asia/Jakarta` to your region (e.g., `TZ=UTC`, `TZ=America/New_York`)
- Frequency:
  - Actions: tweak the `cron` entries
  - Local: change your Task Scheduler/cron settings

## Troubleshooting

- “nothing to commit” locally: this script uses `--allow-empty`, so it should still commit. Ensure you’re in a Git repo and on a branch with a remote set.
- Push errors: set upstream once: `git push -u origin <branch>`
- Actions not pushing:
  - Enable “Read and write permissions” for workflows
  - Check branch protection rules (they may block pushes)
  - Review the Actions logs for errors
- Time looks off: remember cron is UTC; message tz is set via `TZ=...`

## Related 🎯

- Older project: https://github.com/FarrelAD/Lazy-Git

## Note ✍🏻

This is for learning and personal automation. Don’t use it to mislead others about actual activity.