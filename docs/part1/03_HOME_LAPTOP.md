# Work from home on your own laptop

Team laptops stay in the shop. To keep going between meetings, set up
your own laptop with the same tools. By the end of this page you can
talk to the team, get the code, build it, and test it in the simulator,
all from home.

Windows and Mac both work. Some vendor tools are Windows-only, but you
won't need them at home.

<!--
PHOTO CHECKLIST: save these in docs/img/ and replace each "TODO" note below.
  [ ] slack_channels.png          the team Slack with the main channels
  [ ] home_sim_running.png        simulator GUI running on a personal laptop
-->

## 1. Slack

The team talks on **Slack**: meeting changes, questions, and "can
someone review my PR?".

1. Ask a mentor for an invite to the team Slack.
1. Install Slack on your laptop and your phone: https://slack.com/downloads
1. Set your profile to your **real name**, so people know who you are.
1. Join the programming channel and say hi.
   > **TODO (mentors):** list the channels rookies should join.

   > 📷 **TODO screenshot:** `docs/img/slack_channels.png`.

**Ask questions in channels, not DMs**, so everyone learns from the
answer. Paste the *text* of an error, not a photo of your screen.

## 2. GitHub

You made a GitHub account and a fork in
[Set up your laptop and your fork](01_SETUP.md). At home:

1. Install **GitHub Desktop** (https://desktop.github.com/) and sign in
   as you.
1. Clone **your fork** of `all26`, choose **To contribute to the parent
   project**, just like in [SETUP](01_SETUP.md#clone-your-fork). You'll
   be on **`main`**; after you [switch to the SystemCore](../part4/16_SYSTEMCORE.md),
   you'll use `2027`.
1. Clone this guide too (`Team100/rookie_training`, or your fork of it)
   if you want to help improve it.

Your fork on GitHub connects the shop and home. Push at the end of a
meeting, then at home **pull** (or [sync](02_FORK_WORKFLOW.md)) to pick up
where you left off. The reverse works too.

## 3. VS Code, WPILib, and Git

Follow [Install the tools](01_SETUP.md#install-the-tools-own-laptop-only)
in the setup page: **WPILib 2026** (which brings its own VS Code and
Java), **Git**, and your Git name and email.

* Use **2026 WPILib VS Code** for robot code, even if you already have
  VS Code. When you reach the [SystemCore](../part4/16_SYSTEMCORE.md), add
  **WPILib 2027** too.
* For [Pi code](../part5/22_PI_CHANGES.md), you'll also want Python and its VS Code
  extensions.
* **Phoenix Tuner X** (for Krakens) is only useful in the shop, next to
  the hardware. Skip it at home. (It's free on Windows, but the Mac App
  Store charges for it, so don't buy it.)

## 4. Claude

AI assistants are a big help when you're stuck at home and no mentor
is around. We use **Claude**:

* **The Claude app** (https://claude.ai, plus desktop and phone apps):
  ask questions, paste an error, or have it quiz you on a chapter.
* **Claude Code** (https://claude.com/claude-code): runs in your
  terminal or in VS Code, inside the repo, so it can read our code to
  answer questions. Optional, for when you're comfortable with the
  basics.

> **TODO (mentors):** say which accounts students should use, and check
> the age requirement. Claude's consumer terms require users to be 18
> or older.

The rules are the same as in the shop (see [Using AI here](../../README.md#using-ai-here)):

* **In this guide:** use AI however you like.
* **In `all26`:** AI is a **mentor**, not a typist. It explains and asks
  questions; *you* write the code. Claude Code reads `all26`'s
  `AGENTS.md` and follows this automatically. In the chat app, tell it
  yourself: "Don't write the code for me, help me figure it out."

## What you can do at home

| Works at home | Needs the shop |
| --- | --- |
| Build the code | Deploy to a RoboRIO or SystemCore |
| Run the [simulator](09_SIMULATOR.md) and Glass against it | Spin real motors |
| Write and run unit tests | [Power-draw tests](../part2/10_GLASS_POWER.md) |
| Push, open PRs, review other people's PRs | SSH into the robot or the Pis |
| Run the [Pi code](../part5/22_PI_CHANGES.md) with a fake camera | Set CAN IDs |
| Read the code and the docs | |

## Checkpoint

On your own laptop, at home:

1. Open `studies/discus` in 2026 WPILib VS Code
   ([how](01_SETUP.md#first-build)) and get `BUILD SUCCESSFUL`. Errors?
   See the [Build errors FAQ](../reference/BUILD_ERRORS.md).
1. Run it in the [simulator](09_SIMULATOR.md).

   > 📷 **TODO screenshot:** `docs/img/home_sim_running.png`.

1. Post "set up at home ✅" in the team Slack.

Next: [Deploy to the RoboRIO](04_DEPLOY.md)
