# 🏆 Park on the X

So far your auton started from one taped spot, and drove a shape. Now
it has to drive to a **place**: an **X of tape on the floor**. It can
only win if it uses **both** things you learned in this part:

* **localization** ([Where am I?](13_LOCALIZATION.md)): the robot knows
  where it starts, and its odometry has to be **good**, and
* **auton** ([Work on auton](14_AUTON.md)): plan a path to the X and
  follow it.

Use one of the **demo bots ("A" or "B")**, on the **RoboRIO**, with the
`main` branch, the same as the rest of Part 3. No cameras yet: the robot
has only its wheels. (In [Part 5](../part5/27_PARK_FROM_ANYWHERE.md)
you'll add a camera and start from anywhere.)

> [!CAUTION]
> The robot drives by itself. Clear the floor, keep everyone **behind the
> start line**, and keep one person on the Driver Station with a finger
> on **Enter** (disable). Start with the drive speed turned **down**.

## Why it matters

In a match, auton starts the instant the field says go. If the robot
*thinks* it's 20 cm to the left of where it really is, every path after
that is 20 cm off too, and it misses the game piece, the goal, or both.
Robots that win auton know where they are **before** they move, and
don't drift on the way.

## The course

A mentor sets up:

1. **Three start spots**, marked with tape along two edges of the
   robot, labeled **1**, **2**, and **3**, a meter or two apart, some
   facing different ways.
1. **An X of tape on the floor**, 2 to 5 m from the starts, so that at
   least one start needs a **turn** to get there.
1. A card that says where the X is **from each start**, measured with a
   tape measure. For example: *"From start 2: the X is 3.2 m ahead and
   0.8 m to the left. Park facing the same way you started."*

That card is all you get.

> 📷 **TODO photo:** docs/img/park_on_the_x_course.jpg: the three start
> spots, the X, and the distances labeled.

## Easy mode: one start

The robot always starts on **start 1**.

This is just your [auton chapter](14_AUTON.md) with a target: plan a
path from the start to the X, run it, and measure.

**Checkpoint:** 3 runs in a row end within **20 cm** of the X.

> **Check yourself:** the mentor moves the X twice as far away. Does
> your error get bigger? Why? (Think about
> [drift](13_LOCALIZATION.md#experiment-6-drift).)

## Hard mode: roll for it

Just before each run, roll a die: **1–2** is start 1, **3–4** start 2,
**5–6** start 3. Put the robot on that start, pick the matching auton
in Glass, and go. Real matches work like this: you pick your start
spot just before the match.

**Goal:** when auton starts, your robot:

1. knows which start it's on (from the auton you picked),
1. plans a path from **that start** to the X,
1. drives there and stops, facing the right way.

<details>
<summary>Hint 1: where is the X, in field coordinates?</summary>

The card gives the X **from each start**. Your robot starts at (0, 0)
facing +x, whichever start it's on, so each start's auton can use the
card directly, as a transform. Draw it on paper first.

Better: pick one spot to be (0, 0), and work out each start's pose and
the X's pose from there. Then all three autons drive to the **same** X
pose. Which way is easier to check?
</details>

<details>
<summary>Hint 2: the demo bot can't slide sideways</summary>

The demo bot is a **tank drive**: it can drive forward, back, and turn,
but not sideways. A path that needs it to slide won't work. Think about
turning toward the X first, or a path that curves in, and then turning
to the final heading.
</details>

<details>
<summary>Hint 3: it always misses the same way</summary>

That's a **bias**. Go back to
[Experiment 4](13_LOCALIZATION.md#experiment-4-one-meter-for-real) and
[Experiment 5](13_LOCALIZATION.md#experiment-5-the-spin-test). Is the
wheel size or the track width in the code a little wrong for **this**
robot? Fix the number with a mentor, and measure again.
</details>

**Checkpoint:** from 3 rolls in a row, the robot ends within **20 cm**
of the X every time, without anyone touching it.

## Scoring

Measure the error from the **center of the robot** to the **center of
the X**, in **cm**. Write down the heading error in degrees too.

* **Score = total distance error, in cm, over 3 runs.** In hard mode,
  that's 3 rolls of the die. **Lower is better.**
* Heading error breaks ties.
* **15 seconds** per run. If the robot hasn't stopped by then, that run
  scores **200 cm**.
* Anyone touching the robot (or a disable) also scores 200 cm.

Use the [measuring steps](14_AUTON.md#how-to-measure) from the auton
chapter: tape marks, tape measure, no eyeballing.

### Two kinds of miss

For every run, also write down where the robot **thinks** it ended
(its pose in Glass) and compare it with the tape measure.

| What you see | What's wrong |
| --- | --- |
| The robot **thinks** it's on the X, but it isn't | **Localization:** it doesn't know where it is. Check the wheel size and track width ([Where am I?](13_LOCALIZATION.md#experiment-4-one-meter-for-real)) |
| The robot **knows** it missed the X | **Tracking:** it knows where it is, but didn't drive there well. Check the path and the controller |

This is the same split as in [Work out the error](14_AUTON.md#work-out-the-error).

Post your score, a video, and that table for each run to
**`#rookie_training`**.

### Records

| Mode | Total error (3 runs) | Robot | Who | Date | Slack proof |
| --- | --- | --- | --- | --- | --- |
| Easy | — | | | | |
| Hard | — | | | | |

<details>
<summary>⭐ Stretch: park and shoot</summary>

Put a bucket in front of the X, like in the
[Shooter Challenge](../part2/12_DEMO_BOT.md#-the-shooter-challenge).
Park on the X, then shoot 5 balls into it, all in auton.
**Score = balls in ÷ seconds** for the whole run, same as the Shooter
Challenge.
</details>

> **Check yourself:** keep your score. In Part 4 you'll run this exact
> challenge again on the SystemCore. Will it be better or worse? Why?

Next: [Part 4: Switch to the SystemCore](../part4/16_SYSTEMCORE.md)
