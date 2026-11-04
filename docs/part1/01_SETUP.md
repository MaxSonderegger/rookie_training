# Set up your laptop and your fork

By the end of this page you will have your own copy of the team code on
GitHub (your *fork*), a copy of it on your laptop, and a successful build.

## The tools, in one paragraph

FRC robots are programmed in [Java](https://dev.java/) using
[WPILib](https://wpilib.org/), the robotics library from
[WPI](https://www.wpi.edu/). You write code in
[VS Code](https://code.visualstudio.com/), and
[Gradle](https://gradle.org/) builds it. [Git](https://git-scm.com/)
tracks every change, and [GitHub](https://github.com/) is where the team
shares code. If VS Code is new to you, do its
[getting started tutorial](https://code.visualstudio.com/docs/getstarted/getting-started).

Git can be a little hard:

![git-comic](../img/git_comic.webp)

If Git is new to you, read GitHub's
[getting started with Git](https://docs.github.com/en/get-started/learning-to-code/getting-started-with-git)
and do the [hello-world](https://docs.github.com/en/get-started/start-your-journey/hello-world)
exercise. It teaches *repository*, *branch*, *commit*, and *pull request*,
which you'll use every day.

## Why a fork?

`Team100/all26` is the team's repository. Students don't push to it
directly. Instead:

```
Team100/all26  ──fork──▶  <you>/all26  ──clone──▶  your laptop
      ▲                        ▲                       │
      └──── pull request ──────┴──────── push ─────────┘
```

* **upstream** = `Team100/all26`, the team's copy
* **origin** = `<you>/all26`, your fork; you can push anything here
* A **pull request (PR)** asks a mentor to merge your changes from your
  fork into upstream

You can't break the team code by pushing to your fork, so experiment
freely.

## Get a laptop

* **Team laptop:** take one of the Windows laptops from the locked cabinet
  and plug it in. The password is written on it. These already have
  WPILib, GitHub Desktop and Git installed, so skip to
  "Make a GitHub account".
* **Your own laptop:** you'll install the tools below. To also work
  from home (Slack, Claude, and so on), see
  [Work from home on your own laptop](03_HOME_LAPTOP.md).

## Install the tools (own laptop only)

1. **WPILib 2026.** Follow the
   [WPILib installation guide](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-2/wpilib-setup.html).
   It installs "2026 WPILib VS Code", Java, and the WPILib tools. Use this
   VS Code for robot code, not a different VS Code you already have.
   (You'll add 2027 WPILib later, when you
   [switch to the SystemCore](../part4/16_SYSTEMCORE.md).)
1. **Git.** Windows: install [Git for Windows](https://git-scm.com/download/win)
   and accept the defaults. Mac: run `xcode-select --install`.
1. **GitHub Desktop** (optional but recommended):
   https://desktop.github.com/
1. **Tell Git who you are.** In a terminal:
   ```
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```
   Use the same email as your GitHub account.

## Make a GitHub account

Go to https://github.com/signup. Pick a username you're happy for mentors
to see on every PR.

## Fork the team repository

1. Sign in to GitHub and go to https://github.com/Team100/all26
1. Click **Fork** (upper right).
1. **Uncheck "Copy the `main` branch only".** You'll start on `main`,
   but you'll need the `2027` branch later for the SystemCore.
1. Click **Create fork**.

You now have `https://github.com/<you>/all26`.

### Turn on GitHub Actions for your fork

GitHub turns off automatic tests on new forks. Go to the **Actions** tab
of your fork and click the button to enable workflows. Now the tests run
every time you push.

## Clone your fork

### With GitHub Desktop

1. **File → Options → Accounts**: sign out of anyone else's account, then
   sign in as you.
1. **File → Clone repository…** and pick `<you>/all26`.
1. Save it in a folder with **your name** in it (e.g.
   `Desktop\alice\all26`), so nobody else uses it by mistake.
1. When it asks "How are you planning to use this fork?", choose
   **To contribute to the parent project**. This sets up `upstream` for you.
1. Check that the branch menu at the top says **`main`**.

### Or with the command line

```
git clone https://github.com/<you>/all26.git
cd all26
git remote add upstream https://github.com/Team100/all26.git
```

### Check

```
git remote -v
```

should show `origin` pointing at **your** fork and `upstream` pointing at
`Team100/all26`.

## What you just downloaded

`all26` holds *all* of Team 100's code, in one repository (a
"[monorepo](https://en.wikipedia.org/wiki/Monorepo)"):

* **`comp/`**: the competition robot code
* **`lib/`**: our library, shared by every project and reused every year
* **`studies/`**: small experimental projects. **This is where you'll
  work.**
* **`test/`**: small projects for testing one mechanism at a time
* **`raspberry_pi/`**: the Python vision code for the Pi cameras
* **`console/`**: the drivers' button board

## First build

1. **Start the right VS Code: 2026 WPILib VS Code.** There may be
   several VS Codes on your laptop: a plain one, and one for each WPILib
   year. Only the **2026 WPILib** one builds Parts 1 to 3.
   * **Windows:** the **2026 WPILib VS Code** icon on the desktop. Not
     Windows search, which may find another one.
   * **Mac:** not Spotlight, and not the Dock, which may find another one.
     In Finder, **Go → Go to Folder…** (`Shift+Cmd+G`), type
     `~/wpilib/2026/vscode`, and open **VSCode FRC 2026**. Once it's
     running, right-click its Dock icon → **Options** → **Keep in Dock**.

   **Check:** there's a red **W** icon in the top right, and in the
   **Extensions** pane, the **WPILib** extension's version starts with
   **2026**. No W, or a version starting with 2025 or 2027? Close it and
   start the right one.
1. **Open discus as a workspace:** **File → Open Workspace from File…**,
   go to `all26/studies/discus`, and open **`discus.code-workspace`**.
   It opens the study **and** `lib`, which the study needs. (Every
   project in `all26` that uses `lib` has a `.code-workspace` file; always
   open that.)
   * If you opened the folder instead and VS Code offers to **Open
     Workspace**, say yes.

     <img src="../img/openworkspace.png" width=350/>

   > [!WARNING]
   > **‼️ VS Code asks to "import" or "update" the project to a new
   > year? Say NO, and close the window.** It means you're in the wrong
   > year's VS Code. Importing makes a **copy** of the project somewhere
   > else, with a new `build.gradle` that can't find `lib`, and you'll get
   > hundreds of errors. Go back to step 1 and start 2026 WPILib VS Code.
   >
   > Already imported? Delete the copy, and open the original
   > `all26/studies/discus/discus.code-workspace` in the right VS Code.
   > More in the [Build errors FAQ](../reference/BUILD_ERRORS.md#the-project-was-imported).
1. Turn on **File → Auto Save**.
1. Wait. The first build downloads a lot and can take many minutes.
1. Click the WPILib icon (the red "W" in the top right) and choose
   **Build Robot Code**.

   > 🖥️ **Advanced:** in a terminal, run `./gradlew build` ([how](01_SETUP.md#advanced-run-commands-from-a-terminal)).

**Checkpoint:** the terminal at the bottom ends with `BUILD SUCCESSFUL`.

> [!IMPORTANT]
> **‼️ Not compiling? Seeing errors, or `lib` underlined in red?** Go
> through the [Build errors FAQ](../reference/BUILD_ERRORS.md), in order.
> It's almost always the wrong VS Code, an import, or opening the folder
> instead of the workspace. For deploy and robot problems, see
> [Troubleshooting](../reference/TROUBLESHOOTING.md).

### Advanced: run commands from a terminal

Every WPILib button (Build, Deploy, Simulate, Test) just runs a
**Gradle** command for you. You can type the command yourself instead.
It's the same thing, but when something goes wrong, the terminal shows
you **everything**, which makes problems much easier to find and to
paste into Slack. Look for the 🖥️ **Advanced** boxes in this guide.

1. In 2026 WPILib VS Code, open **Terminal → New Terminal**. Use this
   terminal: it's already set up with WPILib's Java.
   > **TODO:** check whether a plain Windows/Mac terminal also finds
   > WPILib's Java, or whether it says `JAVA_HOME` is not set.
1. Go to your **project folder**, the one with `build.gradle` and
   `gradlew` in it (e.g. `cd studies/discus`). VS Code's terminal
   usually starts there already.
1. Run a command. `./gradlew` works in both PowerShell (Windows) and the
   Mac terminal.

| WPILib button | Terminal command |
| --- | --- |
| Build Robot Code | `./gradlew build` (also runs the unit tests) |
| Test Robot Code | `./gradlew test` |
| Deploy Robot Code | `./gradlew deploy` |
| Simulate Robot Code | `./gradlew simulateJava` |
| Set Team Number | Open `.wpilib/wpilib_preferences.json` and check `"teamNumber": 100` |

When a command fails:

* Scroll **up** to the **first** line that says `error:` or `FAILED`.
  That's the one that matters; the rest is usually a result of it.
* Add `--stacktrace` (e.g. `./gradlew deploy --stacktrace`) for more
  detail, and paste the output into Slack when you ask for help.
* `deploy` lists every address it tried to reach the robot. If they
  all fail, it's a connection problem, not a code problem.

Next: [Day-to-day workflow with your fork](02_FORK_WORKFLOW.md)
