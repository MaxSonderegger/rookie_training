# Day-to-day workflow with your fork

Do this loop for every change, big or small:

1. Sync your fork with the team
1. Make a branch in your fork
1. Change, build, test
1. Commit and push to your fork
1. Open a pull request

> **Which branch?** In Part 1 of this guide (RoboRIO and motors) you
> work on **`main`**. Once you [switch to the SystemCore](../part4/16_SYSTEMCORE.md),
> use **`2027`** everywhere this page says `main`.

## 1. Sync your fork

The team code changes every day. Start every session by catching up, so
your PR doesn't conflict with other people's work.

**On GitHub:** open your fork, switch to the `main` branch, and click
**Sync fork → Update branch**. Then in GitHub Desktop, click
**Fetch origin**, then **Pull origin**.

**Or on the command line:**

```
git checkout main
git pull upstream main
git push origin main
```

## 2. Make a branch in your fork

Make one branch per change, named for what you're doing, e.g.
`alice/shooter-speed`. The branch lives in **your fork** only.

* GitHub Desktop: **Branch → New branch…**, based on `main`.
* Command line: `git checkout -b alice/shooter-speed`

Don't commit straight onto `main` in your fork. Keeping it identical to
the team's `main` makes syncing easy.

## 3. Change, build, test

Edit the code, then build and test.

* Java: WPILib icon → **Build Robot Code**, or **Test Robot Code**
  > 🖥️ **Advanced:** in a terminal, run `./gradlew build` ([how](01_SETUP.md#advanced-run-commands-from-a-terminal)).
* Python (Pi): see [Pi changes](../part5/22_PI_CHANGES.md)

## 4. Commit and push

Commit small steps with messages that say *why*, e.g.
"Lower shooter speed so the ball clears the hub."

* VS Code: open the Source Control pane, look at every change (is it what
  you meant?), click **+** to stage, write a message, and click
  **Commit**. Then in GitHub Desktop, click **Push origin**.
* Command line:
  ```
  git add <files>
  git commit -m "Lower shooter speed so the ball clears the hub"
  git push -u origin alice/shooter-speed
  ```

Don't commit build output, logs, or files you didn't mean to change.

## 5. Open a pull request

1. Go to your fork on github.com. There will be a banner offering to
   **Compare & pull request**.
1. Check the arrows: **base repository `Team100/all26`, base `main`** ←
   **head repository `<you>/all26`, compare `alice/shooter-speed`**.
1. Write what you changed, why, and how you tested it (in sim? on a test
   board? which robot?).
1. Click **Create pull request**.

A mentor will review it. If they ask for changes, make them on the same
branch and push again. The PR updates by itself. When it's merged, delete
the branch, sync your fork, and start the next change from step 1.

## Common problems

* **"Permission denied" pushing:** you're pushing to `upstream`, not
  `origin`. Run `git remote -v` and push to your fork.
* **PR shows lots of changes you didn't make:** the base is wrong (e.g.
  `2027` instead of `main`, or the other way around), or your branch
  started from an old fork. Sync
  your fork, then ask for help rebasing.
* **Merge conflict:** don't panic, and don't delete things to make it go
  away. Ask a mentor; it's a normal part of working on a team.

Next: [Work from home on your own laptop](03_HOME_LAPTOP.md)
