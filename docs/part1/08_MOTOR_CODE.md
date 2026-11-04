# Control a motor from code

Now you'll write real control code: make the motor spin from your study,
read its speed, ramp it up gently, and then drive it with an Xbox
controller.

This chapter doesn't hand you the code. Each step says **what to
build** and **where to look**, then has a **checkpoint**. If you're
stuck, open the hints **one at a time**, and really try before opening
the next one. Figuring it out is the point. It's also how you'll work
on the real robot code, where nobody has written the answer down.

You need:

* a [study](07_STUDY.md) with `lib` included, that builds and deploys
* the motor from [Your first motor](05_FIRST_MOTOR.md), and its CAN ID

Open your study's workspace and find `Robot.java`. For now, everything
goes in this one file.

## Working examples to learn from

When you're stuck, **look at how working code does it**. Reading
working code is a real skill: find the part you need and understand it,
rather than copying the whole thing. No single example matches this
chapter exactly, so each step points you at the right one:

| For | Look at | What to ignore |
| --- | --- | --- |
| Step 2: the every-cycle calls | [`test/single_roller` `Robot.java`](https://github.com/Team100/all26/blob/main/test/single_roller/src/main/java/frc/robot/Robot.java), its `robotPeriodic()` | Everything else. It hides its motor **inside** a `SingleRollerSubsystem`, so you won't find a motor class there. |
| Step 3: creating a motor | [`studies/music` `Robot.java`](https://github.com/Team100/all26/blob/main/studies/music/src/main/java/frc/robot/Robot.java), the `if (MOTOR)` block in its constructor | The music parts (`Player`, `Play`). It uses `KrakenX44Motor`; yours may be a different class. |
| Steps 4 to 7 | No example: this is where you figure it out | |

## The robot lifecycle

WPILib calls methods in your `Robot` class at certain times:

| Method | When it runs |
| --- | --- |
| `Robot()` (the constructor) | Once, when the code starts |
| `robotPeriodic()` | Every 20 ms (50 times a second), in every mode |
| `teleopInit()` | Once, when teleop is enabled |
| `teleopPeriodic()` | Every 20 ms while teleop is enabled |

Read [WPILib: What is "TimedRobot"?](https://docs.wpilib.org/en/stable/docs/software/vscode-overview/creating-robot-program.html)
to learn more. You'll use this table in almost every step.

## Step 1: Use the Team 100 robot base

**Goal:** your `Robot` class extends our version of `TimedRobot`, which
adds better logging.

**Look in:** `lib/.../framework`.

**Checkpoint:** it builds.

<details>
<summary>Hint 1</summary>

Our classes often have `100` in the name.
</details>

<details>
<summary>Hint 2</summary>

It's `TimedRobot100`. Change what your class `extends`. When VS Code
underlines it in red, hover over it and choose **Quick Fix** to add the
`import`.
</details>

## Step 2: Keep the library ticking

Our library needs a few calls **every cycle** to work: one moves our
clock forward, one clears old sensor readings, one runs commands, and
one sends logs to the dashboard.

**Goal:** your `robotPeriodic()` makes those four calls.

> [!WARNING]
> **Don't skip this step.** It's easy to miss, because your code still
> builds and deploys without it, and the motor still spins. But:
>
> * **Glass shows nothing.** Our motors don't send their logs by
>   themselves. They sign up with `LogPoller`, and nothing is sent until
>   `robotPeriodic()` calls `LogPoller.log()`, every cycle.
> * **Sensor readings get stuck.** Without `Cache.refresh()`, the speed
>   you read in Step 5 never changes.
>
> An **empty** `robotPeriodic()` (the one the WPILib template gives you)
> is the #1 reason rookies see no logs in Glass.

**Look in:** the `robotPeriodic()` of `test/single_roller`. Find the
four lines that do this. (It has a few extra lines you don't need yet.)

**Checkpoint:** it builds, and you've written a short comment above each
line saying what it does, in your own words. Open each class (`Takt`,
`Cache`, `CommandScheduler`, `LogPoller`) and read its comments to find
out.

<details>
<summary>Hint 1</summary>

Two of the class names are `Takt` and `Cache`.
</details>

<details>
<summary>Hint 2: which one sends the logs?</summary>

Open
[`LogPoller`](https://github.com/Team100/all26/blob/main/lib/src/main/java/org/team100/lib/logging/LogPoller.java).
It's tiny. What does `register` do? What does `log` do? Who calls
`log`? (If nobody does, what reaches Glass?)
</details>

<details>
<summary>Hint 3: what's a cache?</summary>

Asking a motor for data is slow, so we **cache** each value: we read it
once per cycle and reuse it. Then we throw the cache away every cycle,
or we'd see the same old value forever. Read about
[caching](https://en.wikipedia.org/wiki/Cache_(computing)).
</details>

## Step 3: Create the motor

Our motor classes are in [`lib/.../motor`](https://github.com/Team100/all26/tree/main/lib/src/main/java/org/team100/lib/motor).
CTRE motors are in the `ctre` folder, **one class for each kind of
motor**:

* Kraken X60: [`KrakenX60Motor`](https://github.com/Team100/all26/blob/main/lib/src/main/java/org/team100/lib/motor/ctre/KrakenX60Motor.java)
* Kraken X44: [`KrakenX44Motor`](https://github.com/Team100/all26/blob/main/lib/src/main/java/org/team100/lib/motor/ctre/KrakenX44Motor.java)
* Falcon 500: [`Falcon500Motor`](https://github.com/Team100/all26/blob/main/lib/src/main/java/org/team100/lib/motor/ctre/Falcon500Motor.java)

**Use the class that represents the motor you actually have**, or, if
yours isn't listed, look in the `ctre` folder for the class that matches
it. The classes differ in the motor's physics (how strong and how fast
it is), so the wrong one gives you wrong numbers.

> [!NOTE]
> **Using a Falcon 500?** It has the same controller inside (a Talon FX)
> as a Kraken, so this chapter works the same; just use
> `Falcon500Motor`. One catch: older Falcons may still have **Phoenix 5**
> firmware, but our code uses **Phoenix 6**. Look at the Falcon's card
> in Tuner X: if it says Phoenix 5, update it to Phoenix 6 firmware
> there first (it's free). Otherwise your code builds and deploys, but
> the motor never moves.
>
> **TODO:** add the exact Tuner X steps for switching a Falcon to
> Phoenix 6 firmware.

**Goal:** your `Robot` has a motor **field**, created in the
constructor. Print a message just before and just after you create it.

**Look in:** your motor class's **constructor**. It needs a lot of
arguments. Here's what each one means; working out *what to pass* is
your job:

| Argument | What it is |
| --- | --- |
| `LoggerFactory parent` | Where this motor's logs go |
| `TotalCurrentLog currentLog` | Adds up the current used by every motor |
| `CanId canId` | The motor's CAN ID (the one on its masking tape) |
| `NeutralMode100 neutral` | What the motor does at zero: coast or brake |
| `MotorPhase phase` | Which way is "forward" |
| `CurrentLimit limit` | Maximum current, in amps: a **stator** limit and a **supply** limit ([what's the difference?](06_HOW_MOTORS_WORK.md#stator-current-vs-supply-current)) |
| `Friction friction` | A model of friction; not used yet |
| `PIDConstants pid` | Feedback control settings; not used yet |

Use **safe** values on a test board: a **low current limit** (about 10
amps), and **coast**, so the motor spins down freely.

**Checkpoint:** before you build, you can say what every argument does.
Then build and deploy, and you see both of your messages in the Driver
Station console. (They may be mixed in with other output, so look
carefully.)

![](../img/motor_log_statements.png)

<details>
<summary>Hint 1: where do the logger and current log come from?</summary>

Look at the `if (MOTOR)` block in `studies/music`'s constructor.
Something called `RobotLog` gives you the `TotalCurrentLog`.
</details>

<details>
<summary>Hint 2: how do I make a CanId or a CurrentLimit?</summary>

Open `CanId`, `CurrentLimit`, `Friction`, and `PIDConstants` and read
their constructors and comments. `PIDConstants` has a handy way to say
"all zero"; can you find it?
</details>

<details>
<summary>Hint 3: the values</summary>

* logs: `log.name("motor")`
* CAN ID: `new CanId(…)` with **your** number
* neutral: `NeutralMode100.COAST`
* phase: `MotorPhase.FORWARD`
* limit: `new CurrentLimit(10, 10)` (stator, supply)
* friction: `new Friction(0, 0, 0, 0)`
* pid: `PIDConstants.zero()`

Also look at what `studies/music` calls at the very start of its
constructor, before anything else.
</details>

## Step 4: Turn the motor

**Goal:** the motor spins at **10%** while teleop is enabled.

**Look in:**
[`Motor.java`](https://github.com/Team100/all26/blob/main/lib/src/main/java/org/team100/lib/motor/Motor.java),
the interface every motor class follows. Find a method that takes a
number from **−1** (full reverse) to **1** (full forward). Then use the
lifecycle table: which method runs over and over during teleop?

Deploy, and choose **TeleOperated** in the Driver Station. Does the
motor turn? It shouldn't yet: the robot is **disabled**, and disabled
robots never move. That's a safety rule. Say **"Enabling!"**, keep a
finger on **Enter** (disable), and click **Enable**.

**Checkpoint:** the motor spins slowly, and stops when you disable.

<details>
<summary>Hint 1</summary>

"Duty cycle" means the fraction of full power, from −1 to 1.
</details>

<details>
<summary>Hint 2</summary>

Call `setDutyCycle(0.1)` on your motor, in `teleopPeriodic()`.
</details>

## Step 5: Read the speed

**Goal:** print the motor's speed while it spins.

**Look in:** your motor has an `encoder()` method. Open the type it
returns and find a method for **velocity**. What units does it use?

**Checkpoint:** you see the speed printed, and you can say how many
**rotations per second** that is.

> **Speed always 0, or stuck at one number?** Your `robotPeriodic()` is
> probably missing a call. Go back to
> [Step 2](#step-2-keep-the-library-ticking) and check all four are
> there.

> **Experiment:** comment out the `Cache` line from Step 2, deploy, and
> watch the printed speed. What happens? Why? Then put it back.

<details>
<summary>Hint 1</summary>

The method name ends with its units. Read about
[rad/s](https://en.wikipedia.org/wiki/Radian_per_second). How many
radians are in one rotation?
</details>

<details>
<summary>Hint 2</summary>

`m_motor.encoder().getVelocityRad_S()`. To print a number with two
decimals: `System.out.printf("speed %.2f%n", speed);`
</details>

## Step 6: Speed up slowly

Jumping straight to full speed is hard on the hardware.

**Goal:** when you enable, the motor starts at 0 and speeds up a little
every cycle, until it reaches **0.3** (30%), then stays there. Every
time you enable, it starts slow again.

**Checkpoint:** you can see and hear it ramp up, and disabling then
re-enabling ramps up again from zero.

> **Check yourself:** if you add 0.001 each cycle, how many seconds does
> it take to reach 0.3? (How often does `teleopPeriodic` run?)

<details>
<summary>Hint 1</summary>

You need to *remember* the current duty cycle from one cycle to the
next. Local variables are forgotten when the method ends. What isn't?
</details>

<details>
<summary>Hint 2</summary>

Make a field, like `private double m_dutyCycle`. Each cycle, add a
small amount to it, and use it. To stop at a limit, look up `Math.min`.
</details>

<details>
<summary>Hint 3</summary>

To start slow *every time you enable*, reset the field to zero in the
method that runs once when teleop starts. Check the lifecycle table.
</details>

## Step 7: Drive it with an Xbox controller

Running at a fixed speed is boring. Steer with a joystick.

**Goal:** the motor follows the controller's **left stick**, at no more
than **10%** power (so it can't go crazy on a test board).

**Look in:** our controller class,
[`DriverXboxControl`](https://github.com/Team100/all26/blob/main/lib/src/main/java/org/team100/lib/hid/DriverXboxControl.java).
What does its constructor need? Which method reads the left stick's
up/down?

Plug the controller into the **laptop** with USB. In the Driver Station,
the USB tab should show it in slot 0. If it says the HID isn't found,
some controllers have an X/D switch; try flipping it. When everything's
ready, all the Driver Station lights are green:

![](../img/driver_station.png)

**Checkpoint:** enable teleop, push the left stick, and the motor
follows it.

> **Check yourself:** print the stick value and push the stick **up**.
> Does the number go up or down? Look up `getLeftY` in the
> [WPILib docs](https://docs.wpilib.org/en/stable/) to find out why.
> How would you make "up" mean "forward"?

<details>
<summary>Hint 1</summary>

Create the controller once (where?) and keep it in a field. The `0` in
the Driver Station's USB tab is the controller's **port**.
</details>

<details>
<summary>Hint 2</summary>

`new DriverXboxControl(log, 0)` in the constructor. In `teleopPeriodic`,
read `leftY()`, make it smaller so it's never more than 0.1, and send it
to the motor. This replaces your ramp from Step 6.
</details>

## Send it in

Commit, push, and open a PR ([how](02_FORK_WORKFLOW.md)). In the
description, say what you tested and what you saw. A mentor will read
your code, so make sure your comments from Step 2 are in there.

> If the classes or methods named here don't match what you see in
> `lib`, the library has moved on. Check the examples above, and the
> motor class itself, and fix this page.

Next: [Use the simulator](09_SIMULATOR.md)
