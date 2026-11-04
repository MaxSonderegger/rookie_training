# Switch to the SystemCore

In Parts 1 to 3 you learned motors, real robots, localization, and
auton on a **RoboRIO**. The competition robot runs on the new
[SystemCore](https://docs.wpilib.org/en/2027/) controller, so Part 4
moves everything over to it, and the rest of the guide stays there. Most of what you know carries over. This
page covers what's different, gets your laptop and fork ready, and moves
your motor study to the SystemCore. Then you'll
[put it all together on the demo bot](19_DEMO_BOT_SYSTEMCORE.md).

## What changes

* **New controller.** The SystemCore replaces the RoboRIO. It's a small
  Linux computer you can log in to with `ssh`.
* **New branch.** SystemCore code lives on the **`2027`** branch of
  `Team100/all26`. You've been on `main` until now. From here on, every
  link, branch, and PR in this guide uses `2027`.
* **New WPILib.** The 2027 WPILib only works with the SystemCore, and
  the 2026 WPILib only with the RoboRIO. Keep both installed.

### RoboRIO → SystemCore cheat sheet

Older docs, forum posts, and videos talk about the RoboRIO. Translate
like this:

| RoboRIO | SystemCore |
| --- | --- |
| USB-B cable to the RoboRIO | USB-C cable to the SystemCore |
| USB address `172.22.11.2` | `172.26.0.1` (Windows), `172.27.0.1` (Mac/Linux) |
| `roboRIO-100-FRC.local` | `robot.local` |
| Robot address `10.1.0.2` | Still `10.1.0.2` on the robot network |
| ssh user `lvuser` / `admin` | ssh user `systemcore`, password `systemcore` |
| Deploy folder `/home/lvuser/deploy` | `/home/systemcore/deploy` |
| Serial number in the console | See [Robot identity](18_IDENTITY.md) |
| One CAN bus | Five CAN ports, `can_s0` … `can_s4`; in code, `new CanBusId(0)` … |
| `edu.wpi.first.…` packages | `org.wpilib.…` packages |
| "2026 WPILib VS Code" | "2027 WPILib VS Code" |
| Mac: Glass from `~/wpilib/2026/tools` | Mac: Glass from `~/wpilib/2027/tools` ([how](../part2/10_GLASS_POWER.md#open-glass-and-connect)) |
| Reset button reboots it | TODO: how to reboot or clear an e-stop |

The SystemCore also has a web dashboard at http://robot.local, and its
screen shows its current IP address.

## 1. Install 2027 WPILib

Team laptops already have it; on your own laptop, follow the
[2027 WPILib installation guide](https://docs.wpilib.org/en/2027/docs/zero-to-robot/step-2/wpilib-setup.html).
It installs **"2027 WPILib VS Code"** next to your 2026 one. Use the 2027
one for everything from here on.

## 2. Get the `2027` branch

If you unchecked **"Copy the `main` branch only"** when you forked (see
[Setup](../part1/01_SETUP.md#fork-the-team-repository)), your fork already has it.
In GitHub Desktop, switch to the **`2027`** branch. On the command line:

```
git fetch upstream
git checkout 2027
```

If your fork only has `main`, run this once instead:

```
git fetch upstream 2027
git checkout -b 2027 upstream/2027
git push -u origin 2027
```

From now on, do the [fork workflow](../part1/02_FORK_WORKFLOW.md) with **`2027`**
wherever it says `main`: sync `2027`, branch from `2027`, and open PRs
against `2027`.

Open `studies/discus` in **2027 WPILib VS Code** and build it.

**Checkpoint:** `BUILD SUCCESSFUL` on the `2027` branch.

## 3. Deploy to a SystemCore

1. Ask a mentor for a test board with a SystemCore, and for help powering
   it up. Wait for its screen to show that it has booted.
1. Connect your laptop to the SystemCore with a **USB-C** cable.
   * You can also use Ethernet through the radio or a switch; see
     [Connect to the robot](17_ROBOT_NETWORK.md).

   > 📷 **TODO photo:** `docs/img/systemcore_usb_c.jpg`: the SystemCore
   > USB-C port with the cable plugged in.

1. `Ctrl+Shift+P` → **WPILib: Set Team Number** → `100`.
1. `Ctrl+Shift+P` → **WPILib: Deploy Robot Code**.

   ![deploy](../img/wpilib_deploy.png)

   > 🖥️ **Advanced:** in a terminal, run `./gradlew deploy` in **2027** WPILib VS Code's terminal ([how](../part1/01_SETUP.md#advanced-run-commands-from-a-terminal)).

1. Watch the terminal for `BUILD SUCCESSFUL`, then open the Driver
   Station console.

   ![console](../img/rio_log.png)

   > 📷 **TODO screenshot:** replace the RoboRIO console above with the
   > SystemCore one.

If it fails: check the cable, wait for the SystemCore to finish booting,
and try `ping robot.local` (on a Mac, `ping -c 4 robot.local`).

**Checkpoint:** Communications and Robot Code are both green in the
Driver Station.

## 4. CAN on the SystemCore

The RoboRIO has one CAN bus. The SystemCore has **five CAN ports**,
`can_s0` to `can_s4`, so the code has to say which port each motor is on.

1. Plug your Kraken's CAN wires into **`can_s0`**.

   > 📷 **TODO photo:** `docs/img/systemcore_can_ports.jpg`: close-up
   > of the SystemCore CAN ports, with `can_s0` marked.

   > 📷 **TODO photo:** `docs/img/kraken_can_to_systemcore.jpg`: CAN
   > from the Kraken going into `can_s0`.

1. [SSH in](#6-ssh-into-the-systemcore) and check that the SystemCore
   sees CAN traffic. Run this twice; **RX packets** should go up:
   ```
   ip -s link show can_s0
   ```
1. Phoenix Tuner X only sees the SystemCore's CAN ports while robot code
   that calls
   [`CTREStartup.start()`](https://github.com/Team100/all26/blob/2027/lib/src/main/java/org/team100/lib/motor/ctre/CTREStartup.java)
   is running. Point Tuner X at `robot.local` instead of the RoboRIO, and
   set its SSH login back to `systemcore` / `systemcore` (you changed it
   to `admin` for the RoboRIO).
   > **TODO:** check this on a fresh SystemCore. Does Tuner X see the
   > buses without our code running?

## 5. Move your motor study to the SystemCore

Your study from [Control a motor from code](../part1/08_MOTOR_CODE.md) was built for
the RoboRIO, on `main`. Rebuild it on `2027`.

1. On a new branch from `2027`, copy
   [`studies/template2027`](https://github.com/Team100/all26/tree/2027/studies/template2027)
   to a new study with your name, and set it up with `lib` the same way
   as in [Your first study](../part1/07_STUDY.md#include-the-lib-directory). Compare
   with
   [`test/single_roller`](https://github.com/Team100/all26/tree/2027/test/single_roller),
   which is set up this way on `2027`.
1. Bring over your `Robot.java` code, and fix what's different:
   * Imports: `edu.wpi.first.…` becomes `org.wpilib.…`. Delete the red
     imports and let VS Code's **Quick Fix** add the new ones.
   * Call `CTREStartup.start();` right after `Startup.start();`.
   * Your motor class (`KrakenX60Motor`, `KrakenX44Motor`,
     `Falcon500Motor`, or whichever one represents your motor) takes one
     more argument, the CAN port: `new CanBusId(0)`, right after the
     `CanId`. Open your class in
     [`lib/.../motor/ctre`](https://github.com/Team100/all26/tree/2027/lib/src/main/java/org/team100/lib/motor/ctre)
     on `2027` to see where it goes.

> **Check yourself:** why does the SystemCore need a `CanBusId` when the
> RoboRIO didn't?

**Checkpoint:** your Kraken follows the Xbox stick on a SystemCore board,
just like it did on the RoboRIO.

Send it in as a PR against **`2027`**.

## 6. SSH into the SystemCore

The SystemCore is a small Linux computer. When something on the robot
isn't working, the fastest way to find out why is to **log in and
look**. `ssh` ("secure shell") lets you type commands on it from your
laptop. It's built into Windows, Mac, and Linux; on Windows, open
**Command Prompt** or **PowerShell**.

```
ssh systemcore@robot.local
```

(or `ssh systemcore@10.1.0.2`, or the USB address). The first time, type
`yes` to trust the host. The password is `systemcore`. **Never change
it,** because deploys from VS Code stop working if you do.

### Things to check

| Question | Command | Good answer |
| --- | --- | --- |
| Which SystemCore is this? | `cat /sys/firmware/devicetree/base/serial-number; echo` | A serial that matches an entry in [Identity.java](https://github.com/Team100/all26/blob/main/lib/src/main/java/org/team100/lib/config/Identity.java) |
| What are its addresses? | `ip -brief addr` | `10.1.0.2` on the Ethernet interface |
| Can it see the radio? | `ping -c 3 10.1.0.1` | 3 replies |
| Are the CAN buses up? | `ip -brief link \| grep can` | `can_s0` … `can_s4` say `UP` |
| Is CAN traffic flowing? | `ip -s link show can_s0` | RX packets going up when run twice; few errors |
| Is our code deployed? | `ls -l /home/systemcore/deploy` | Files with a recent date |
| Is the disk full? | `df -h /` | `Use%` well under 90% |
| How long since reboot? | `uptime` | Rebooting by itself means a power problem |
| USB drive plugged in? | `ls /U` | Your files (USB drives mount at `/U`, `/V`, …) |

Robot program output (prints, stack traces) is easiest to read in the
**Driver Station console** or the **web dashboard** at http://robot.local.

Type `exit` to log out.

### Common problems

* **`ping` fails:** `ssh` will fail too. Fix the cable and the laptop's
  network first ([Connect to the robot](17_ROBOT_NETWORK.md#troubleshooting)).
* **`REMOTE HOST IDENTIFICATION HAS CHANGED`:** the SystemCore was
  reimaged, or a different device now has that address. Clear the old
  key and try again: `ssh-keygen -R robot.local` (or the address you
  used).
* **`Connection refused`:** it's still booting. Wait 30 seconds.
* **`Permission denied`:** wrong user or password. It's
  `systemcore@`, password `systemcore`.
* **`robot.local` not found:** name lookup doesn't always work. Use
  the IP address instead.

**Look, don't change.** Don't edit files on the SystemCore by hand. The
next deploy overwrites them, and nobody else will know what you did.

Next: [Connect to the robot (radios)](17_ROBOT_NETWORK.md)
