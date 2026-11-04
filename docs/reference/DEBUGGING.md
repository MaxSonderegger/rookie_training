# Debugging robot code

Robot code is harder to debug than most code. It runs 50 times a second,
on another computer, while things move. Here are the tools, from easiest
to most powerful.

## 1. Read the error

When the code crashes, the console shows a **stack trace**. Read it from
the **top**, and find the first line that mentions one of **your** files.
That's usually where to look.

* What line is it?
* What could be wrong *on that line*? A `NullPointerException` means
  something on that line is `null`. When was it supposed to be set?

## 2. Print

`System.out.printf(...)` shows up in the Driver Station console. It's
quick, but remember `robotPeriodic` runs 50 times a second, so print
only while you're debugging, and take it out afterwards.

## 3. Log and graph

Our loggers (`LoggerFactory`) send values to NetworkTables. Look at them
live in **Glass** ([how to connect](../part2/10_GLASS_POWER.md#open-glass-and-connect))
or **AdvantageScope**, and graph them over time. A
graph of "what I asked for" next to "what happened" answers most
questions about motors and controllers.

## 4. Use the debugger, in simulation

The VS Code debugger can stop your code on any line and show every
variable. It works best in the [simulator](../part1/09_SIMULATOR.md).

1. Click in the margin to the left of a line number to set a
   **breakpoint** (a red dot). Try the first line of `robotPeriodic()`.
1. `Ctrl+Shift+P` → **WPILib: Simulate Robot Code**. (Use the button
   here, not `./gradlew simulateJava`: only the button connects the
   debugger, so the terminal version won't stop at breakpoints.)
1. The code stops at your breakpoint. Hover over variables to see their
   values, and use the **Step Over** button to go one line at a time.

Since `robotPeriodic` runs constantly, you'll hit that breakpoint right
away. That's how you know the debugger works.

Read [VS Code debugging](https://code.visualstudio.com/docs/editor/debugging)
for more.

> Don't use breakpoints on a real robot that is enabled. Stopping the
> code while motors are running isn't safe.

## 5. Write a test

If you can pull the logic into a plain method ("given this stick value,
what duty cycle?"), you can test it with a **unit test** in seconds,
without a robot or the simulator. Look in `lib/src/test` for examples.

## General advice

* **Change one thing at a time.**
* **Make a guess, then test it.** "I think the CAN ID is wrong, so the
  motor's log should show errors."
* **Cut the problem in half.** Does it work in sim? With a simpler study?
  With one motor?
* **Check the hardware too:** CAN ID, wiring, battery, enabled? See
  [SSH into the SystemCore](../part4/16_SYSTEMCORE.md#6-ssh-into-the-systemcore)
  or [the Pis](../part5/21_SSH_AND_CHECKS.md).
