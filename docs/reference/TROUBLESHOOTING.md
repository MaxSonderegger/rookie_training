# Troubleshooting

## Git and GitHub

**"git: command not found"**
: Git isn't installed, or the terminal was open before you installed it.
  Install Git, then close and reopen the terminal (or VS Code).

**"Repository not found"**
: Check the URL. Your fork is `https://github.com/<you>/all26`, and the
  team repo is `https://github.com/Team100/all26`.

**"Permission denied" / "Authentication failed" when pushing**
: You're probably pushing to `Team100/all26` instead of your fork. Run
  `git remote -v`, or in GitHub Desktop check that the repository is
  `<you>/all26`. Also make sure GitHub Desktop is signed in as **you**,
  not the last person who used the laptop.

**"Permission denied" writing files**
: You don't have permission for that folder. Clone into a folder in your
  user directory or on the desktop.

**The PR has lots of changes I didn't make**
: The base branch is wrong, or your fork is out of date. See
  [Fork workflow](../part1/02_FORK_WORKFLOW.md#common-problems).

## Building

**Build takes forever the first time**
: Normal. Gradle downloads a lot the first time. Wait a few minutes.

**Red squiggles everywhere, can't find `org.team100.lib...`, or
hundreds of errors**
: See the [Build errors FAQ](BUILD_ERRORS.md). Most often it's the
  wrong year's VS Code, a project that was imported instead of created,
  or a missing `lib` step.

## Deploying and running

**Deploy fails: "Invalid RoboRIO Image Version" / "GradleRIO-compatible
image versions: 2025_v2.\*"**
: Good news first: the network works (look for `admin @ 10.1.0.2:
  Connected.`). The problem is that your **project** is a different
  year from the **RoboRIO**. Our RoboRIOs run the **2026** image, so
  your project must use GradleRIO **2026**. Open `build.gradle` and set
  `id "edu.wpi.first.GradleRIO" version "2026.2.1"`, set
  `"projectYear": "2026"` in `.wpilib/wpilib_preferences.json`, and use
  **2026 WPILib VS Code** (not 2025). This happens when a study is made
  with the 2025 project wizard. See
  [Your first study](../part1/07_STUDY.md#make-sure-build-and-deploy-work).

**Auton is enabled, but the robot doesn't move**
: Nobody picked an auton, so it's running `NONE`. The Driver Station
  can't choose one. Connect Glass to team 100 and pick it in
  `SmartDashboard → Auton Command`, then enable from the Driver Station.
  See [Work on auton](../part3/14_AUTON.md#part-6-run-it-on-a-real-robot).

**RoboRIO's Comm light is off**
: That's normal without the Driver Station. Comm means "talking to the
  Driver Station", not "the USB cable is plugged in". Open the FRC
  Driver Station (team number **100**) and it turns red (no robot code)
  or green (code running). See the light table in
  [Your first motor](../part1/05_FIRST_MOTOR.md#connect-the-motor).

**Tuner X: "Failed to connect to diagnostics" / "No devices detected"**
: Tuner X reached the RoboRIO, but CTRE's diagnostic server isn't
  running on it. Our robot code normally starts it. Either click **RUN
  TEMPORARY DIAGNOSTICS** in Tuner X (lasts until the RoboRIO reboots),
  or deploy robot code that uses a CTRE motor. If it still finds no
  devices, check the CAN wiring and the motor's power.

**Tuner X: "Error retrieving SSH target platform"**
: Tuner X couldn't log into the RoboRIO to start the temporary
  diagnostic server. Most often it's pointed at the wrong address: over
  USB, type `172.22.11.2` in **Enter a Team # or IP**, not `100` (which
  means `10.1.0.2`). Also check Tuner X's SSH login: a RoboRIO needs
  `admin` with no password, but new Tuner X versions default to
  `systemcore`. Then check that `ssh admin@172.22.11.2` (blank
  password) works from PowerShell. See
  [Your first motor](../part1/05_FIRST_MOTOR.md#find-the-can-id).

**Tuner X: the PDP shows up, but not the Kraken**
: The RoboRIO and the CAN bus work, so look at the Kraken. Check its
  status light:
  * **Off:** no power. **Most likely: no breaker ("fuse") in its PDP
    slot.** Every motor slot needs one; this is exactly what happened
    to us on the first day. Also check its red/black wires at the PDP.
  * **Blinking red:** power is fine, but no CAN. CAN is a **chain**, not
    a hub: the yellow/green wires must run RoboRIO → Kraken → PDP (or
    RoboRIO → PDP → Kraken), with every joint in line, yellow to yellow
    and green to green. Tug each wire gently to check it's held.
  * **Orange or green:** it's on the bus. Click Tuner X's refresh button.

  Have another CTRE motor (like a Falcon)? Swap it in. If it shows up,
  the problem is the Kraken or its own wires; if not, it's the CAN chain.
  > **TODO:** check these status light colors against the
  > [Talon FX status light guide](https://v6.docs.ctr-electronics.com/en/stable/docs/hardware-reference/talonfx/index.html).

**Driver Station says "Emergency Stopped" and won't enable**
: The robot is **e-stopped** (emergency stopped). Unlike a normal
  disable, it stays stopped until the robot code restarts. Either:
  * **redeploy the code** (WPILib → **Deploy Robot Code**, or
    `./gradlew deploy`), or
  * press the **reset button** on the RoboRIO. Ask a mentor where it is,
    and keep clear of the robot while it reboots.
    > **TODO:** what's the SystemCore equivalent?

  Common causes:
  * Someone pressed the **space bar** in the Driver Station. **Space is
    e-stop, Enter is disable.** Keep your finger on Enter.
  * TODO: we've seen e-stops we can't explain. If you find out why, add
    it here.

See [Deploy to the RoboRIO](../part1/04_DEPLOY.md#if-the-deploy-fails),
[Deploy to a SystemCore](../part4/16_SYSTEMCORE.md#3-deploy-to-a-systemcore),
[SSH into the SystemCore](../part4/16_SYSTEMCORE.md#common-problems),
[SSH into the Pis](../part5/21_SSH_AND_CHECKS.md#common-problems), and
[Debugging](DEBUGGING.md).

Found a fix that isn't here? Add it!
