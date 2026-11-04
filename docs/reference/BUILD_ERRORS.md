# Build errors FAQ

Your code won't build, or VS Code is full of red? Start here. Almost
every build problem a rookie hits is one of the few below, and most of
them are about **how the project was opened or made**, not about your
code.

## Debug it in this order

Do these **in order**. Each one takes a minute, and the early ones catch
most problems.

1. **Find the first error.** Build (WPILib → **Build Robot Code**),
   then scroll **up** in the terminal to the **first** line that says
   `error:` or `What went wrong`. Read that one; most of the rest are
   caused by it. Copy it: you'll paste it into Slack if you ask for help.
1. **Right VS Code?** [Check it](#wrong-vs-code-or-wrong-year).
1. **Opened as a workspace?** [Check it](#opened-the-folder-instead-of-the-workspace).
1. **Imported?** [Check it](#the-project-was-imported).
1. **Right year in `build.gradle`?** [Check it](#wrong-year-in-buildgradle).
1. **Study in the right place, with `lib` added?** [Check it](#lib-isnt-set-up).
1. **Vendordeps right?** [Check them](#vendordeps-missing-or-duplicated).
1. **Build passes, but VS Code is still red?** [Reset VS Code's Java](#build-passes-but-vs-code-shows-red).
1. **Still stuck?** Compare your study with
   [`test/single_roller`](https://github.com/Team100/all26/tree/main/test/single_roller)
   (on `main`) or
   [`studies/template2027`](https://github.com/Team100/all26/tree/2027/studies/template2027)
   (on `2027`), which are set up correctly. Then ask in **`#rookie_training`**
   with: the first error, your branch, your VS Code's year, and the top 10
   lines of your `build.gradle`.

**What counts is the build.** If WPILib → **Build Robot Code** ends with
`BUILD SUCCESSFUL`, your project is fine, whatever the editor shows.

## Which year goes with what

| Parts | Controller | Branch | VS Code | Top of `build.gradle` |
| --- | --- | --- | --- | --- |
| 1 to 3 | RoboRIO | `main` | **2026** WPILib VS Code | `id "edu.wpi.first.GradleRIO" version "2026.2.1"` |
| 4 to 7 | SystemCore | `2027` | **2027** WPILib VS Code | `id "org.wpilib.GradleRIO" version "2027.…"` |

Everything in one row has to match. Mixing rows is the #1 cause of
"hundreds of errors".

> **TODO (mentors):** keep the version numbers here in step with
> `studies/discus/build.gradle` on each branch.

## Wrong VS Code, or wrong year

**Looks like:** no red **W** icon in the top right; or WPILib commands
are missing; or VS Code offers to **import** or **update** the project;
or hundreds of errors in code that builds for everyone else.

Your laptop may have several VS Codes: a plain one, and one for each
WPILib year. Only the right year's **WPILib** VS Code works.

**Check:** in the **Extensions** pane, the **WPILib** extension's
version starts with the year from [the table](#which-year-goes-with-what).

**Fix:** close it, and start the right one:

* **Windows:** the **2026 WPILib VS Code** (or **2027**) icon on the
  desktop, not Windows search. Or find it in File Explorer: paste
  `C:\Users\Public\wpilib\2026\vscode` (or the `2027…` folder) into the
  address bar and double-click **Code.exe**. Not there? Try
  `C:\Users\<you>\wpilib\2026\vscode` (WPILib installed for one user
  only). To see which year a desktop icon opens: right-click it →
  **Properties**, and read the **Target**.
* **Mac:** not Spotlight or the Dock. In Finder, **Go → Go to Folder…**
  (`Shift+Cmd+G`), type `~/wpilib/2026/vscode` (or `2027`), and open
  **VSCode FRC 2026** (or 2027). Then right-click its Dock icon →
  **Options** → **Keep in Dock**.
  > **TODO (mentors):** check the app name in `~/wpilib/2027/vscode`.

### "Unsupported class file major version 69"

```
BUG! exception in phase 'semantic analysis' in source unit 'BuildScript'
Unsupported class file major version 69
```

with `wpilib\2027…\jdk` in the last line of the terminal, means
exactly this: a **2026** project (branch `main`) is being built by
**2027** WPILib VS Code. 2027 WPILib uses Java 25 (that's what "69"
means), and the 2026 projects' Gradle can't run on it. Nothing is wrong
with your code. Close VS Code and open the project in **2026** WPILib VS
Code.

(The opposite mix, a `2027` project in 2026 VS Code, fails too, with a
different error.)

## Opened the folder instead of the workspace

**Looks like:** everything from `lib` (`org.team100.lib…`) is underlined
in red, but the build might still pass.

Our projects use `lib`, which lives in another folder. A
**`.code-workspace`** file tells VS Code to open both together.

**Fix:** **File → Open Workspace from File…**, and open the
`.code-workspace` file in your project's folder (for example
`all26/studies/discus/discus.code-workspace`). Your own study needs one
too ([how](../part1/07_STUDY.md#1-make-a-workspace)).

## The project was imported

**Looks like:** your project is somewhere other than `all26/studies/`
(Desktop, Documents, a folder WPILib picked), and nothing from `lib` is
found.

**WPILib: Import a WPILib project** (and the "import or update this
project?" pop-up you get in the wrong year's VS Code) makes a **copy** of
the project in a new folder, with a **new `build.gradle`**. The copy
loses the lines that include `lib`, and is too far from `lib` to find it
anyway.

**Fix:** never import a project in `all26`. Delete the copy.

* For an existing project, like `studies/discus`: open its original
  `.code-workspace` file in the right VS Code.
* For your own study: make it with **WPILib: Create a new project**
  ([how](../part1/07_STUDY.md#create-the-study)), and add `lib`
  ([how](../part1/07_STUDY.md#include-the-lib-directory)).

## Wrong year in `build.gradle`

**Looks like:** errors about `edu.wpi.first…` or `org.wpilib…` packages
not existing, all over.

2027 WPILib renamed the `edu.wpi.first` packages to `org.wpilib`. So a
2027 project can't build `lib` on `main`, and a 2026 project can't build
it on `2027`.

**Check:** the top of `build.gradle`, against
[the table](#which-year-goes-with-what).

**Fix:** remake the study in the right year's VS Code. If it's an
**older** year (2025) on `main`, you can fix it in place: change the
version to `2026.2.1`, and set `"projectYear": "2026"` in
`.wpilib/wpilib_preferences.json`. Otherwise deploy fails with
"Invalid RoboRIO Image Version".

## `lib` isn't set up

**Looks like:** `package org.team100.lib… does not exist`, in the
build output (not just in red underlines).

**Check all three**, from
[Include the `lib` directory](../part1/07_STUDY.md#include-the-lib-directory):

1. **Your study is in `all26/studies/`.** The path to `lib` is
   `../../lib`, which only works from exactly two folders below `all26`.
1. **`build.gradle` has the `sourceSets` block**, right after
   `plugins { … }`:
   ```gradle
   sourceSets {
       main {
           java {
               srcDir "../../lib/src/main/java"
           }
       }
   }
   ```
1. **The workspace** opens both `.` and `../../lib`.

## Vendordeps missing or duplicated

**Looks like:** `package com.ctre…` or `com.revrobotics…` does not
exist; or the build warns **"Duplicate Vendordeps detected"**.

**Check:** your study's `vendordeps` folder has exactly the same files
as `lib/vendordeps`, and **nothing else**.

**Fix:** copy in any missing ones from `lib/vendordeps`. Delete any file
with `copy` in its name (like `WPILibNewCommands copy.json`): that's what
pasting makes when it doesn't replace.

## Build passes, but VS Code shows red

VS Code's Java helper got confused. `Ctrl+Shift+P` (`Cmd+Shift+P` on a
Mac) → **Java: Clean Java Language Server Workspace** → **Restart and
delete**. Wait for it to finish loading (bottom bar).

## It builds, but…

These aren't build errors, but they come up right after:

* **Glass shows no logs, or the motor's speed never changes:** your
  `robotPeriodic()` is missing the four library calls. See
  [Keep the library ticking](../part1/08_MOTOR_CODE.md#step-2-keep-the-library-ticking).
* **Deploy fails, or the robot does nothing:** see
  [Troubleshooting](TROUBLESHOOTING.md).

## Found a new one?

Add it here, with what it looked like and what fixed it, and send a
pull request. The next rookie will thank you.
