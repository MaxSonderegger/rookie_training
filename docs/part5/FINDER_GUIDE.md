# Finder's guide

You're the **finder**. Something on the vision system is broken, and
you don't know what. Your job: find the cause, and **show the evidence**.
(Rules: [the game](25_BREAK_THE_CAMERAS.md#lab-1-the-breaker-vs-finder-game).
Don't read the breaker cards!)

This page doesn't tell you the answer. It gives you a **method**: check
one layer at a time, starting from the most basic. That's how
professionals debug, and it's how you'll find problems fast at a
competition, when nobody tells you what broke.

## The method

Vision data travels through a chain. If any link breaks, the robot gets
nothing:

```
power → Pi → network → our app → camera → picture → tags found → robot → right numbers?
```

**Start at the beginning of the chain and work forward.** At each
layer, run the check. If it passes, move on. If it fails, you've found
your layer: now find out **what** in it is wrong.

Why start at the beginning? Because if the Pi has no power, *every*
check after it fails too, and you'd waste time on them. Checking the
basics first is faster, even though it feels slow.

**Say out loud** what you're checking and why. It helps you think, and
your partner can see your method.

## Layer 1: Is the Pi powered on?

**Check:** look at the Pi. Is its red power LED on? Is the green LED
blinking now and then?

**Why:** no power, nothing else matters. And it's a ten-second check.

**If it fails:** check its power cable at both ends.

## Layer 2: Can you reach the Pi?

**Check:** from your laptop (on the robot network,
[how](21_SSH_AND_CHECKS.md#connect-your-laptop)):

```
ping 10.1.0.31
```

(Use the address on the Pi's case. On a Mac, add `-c 4`.)

**Why:** if your laptop can't reach the Pi, the robot probably can't
either. `ping` is the simplest possible network test.

**If it fails:** look at the Pi's Ethernet port: is the cable in, and
are its lights blinking? Does the cable go all the way to the switch?
If `ping` works **sometimes** but not always, write that down: it's an
important clue.

## Layer 3: Is the Pi healthy?

**Check:** log in (`ssh pi@<pi-ip>`, password `raspberry`) and run:

```
vcgencmd get_throttled
vcgencmd measure_temp
df -h /
```

**Why:** a Pi can be on and reachable but struggling: not enough power,
too hot, or out of disk space. Those cause strange, slow, or random
problems.

**If it fails:** `throttled` not `0x0` means a power or heat problem.
Over ~80 °C means too hot. `Use%` near 100% means the disk is full.

## Layer 4: Is our app running?

**Check:** open `http://<pi-ip>:9001` in a browser. Is `app` **RUNNING**?
Refresh a few times: does it stay running, or keep restarting?

**Why:** the Pi can be perfect, but if our program isn't running,
nobody is looking at the camera.

**If it fails:** click **stderr** (or `tail -n 50 app.stderr` over ssh)
and read the **first** error. Errors are clues: they usually say what
went wrong, and often where.

Even if it's running fine, also skim the app's **normal output**
(**stdout**), especially from when it started. Programs sometimes print
important warnings there, not as errors.

## Layer 5: Can the Pi see the camera?

**Check:** over ssh:

```
lsusb
```

Is there a line for the camera (Arducam)?

**Why:** the app can be running and the camera can still be missing.
`lsusb` asks the Pi "what's plugged into your USB ports?", which is
simpler than anything our app does.

**If it fails:** follow the camera's cable from the camera to the Pi.
Is it plugged in? In which port? Is that the port it's supposed to be
in?

## Layer 6: Is the picture good?

**Check:** open `http://<pi-ip>:1181`. Do you see a live picture? Hold
up an AprilTag close, then 2 m away.

**Why:** everything can be connected and running, and the camera still
sees nothing useful: blocked, dirty, or pointed the wrong way. Only
looking at the picture tells you.

**If it fails:** look at the camera itself. Is anything on the lens? Is
it clean? Is it pointing where it should?

## Layer 7: Does the robot get the data?

**Check:** in Glass on the robot side, are the vision values updating
when a tag is in view? And from the Pi (over ssh), does
`ping -c 3 10.1.0.2` reach the SystemCore?

**Why:** the Pi can be doing everything right, and the data still never
arrives, because the problem is on the robot's side of the network.

**If it fails:** check the path from the switch to the SystemCore, and the
radio.

> **TODO (mentors):** add which NetworkTables values in Glass show
> vision data arriving at the robot.

## Layer 8: Are the measurements right?

**Check:** put a tag at a distance you measure with a **tape measure**
(say 2 m and 4 m), in the center of the picture and near the edge. Does
the distance the camera reports match the tape?

**Why:** everything can be connected, running, and detecting tags, and
the **numbers** can still be wrong. A robot with wrong numbers misses
its shots and gets lost on the field, and nothing ever shows an error.
"Running" isn't the same as "right".

**If it fails:** wrong everywhere by about the same amount, or worse at
the edges? Think about what the camera needs to know about **itself** to
turn pixels into meters ([Set up a new camera](24_NEW_CAMERA.md)). Has
anything about this camera, or which Pi it's on, changed?

## When you think you've found it

Before you guess, have:

1. **The layer** where things first went wrong.
1. **The evidence:** what you saw, exactly (the command and its output,
   or what the page showed).
1. **The cause:** what you think broke, and why that explains
   **everything** you saw, not just one thing.

> **Check yourself:** if the camera were unplugged, which layers would
> pass and which would fail? Now do the same for "Ethernet unplugged".
> Notice how different causes fail at different layers. That's why the
> method works.

## Still stuck?

* Did you skip a layer because you were "sure" it was fine? Go back and
  check it.
* Is the problem **intermittent** (sometimes works)? That points to
  loose connections, power, or two devices fighting over one address.
* Ask for your **one hint**: the breaker tells you the layer.
