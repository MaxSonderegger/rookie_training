# 🏆 The Vision Shooter Challenge

In the [Shooter Challenge](../part2/12_DEMO_BOT.md#-the-shooter-challenge)
you measured the distance with a tape measure and guessed the rest. Now
the robot measures the distance **itself**, with a camera, and picks
the right drum speed on its own.

Put an **AprilTag on the bucket**. A mentor puts the bucket somewhere
between **8 and 12 meters** away, and doesn't tell you where. Your robot
has to figure it out.

> **TODO (mentors):** the demo bot needs a Pi and a USB camera mounted,
> facing forward, and the tag family and size we print. Add both here.

> [!CAUTION]
> Same rules as the Shooter Challenge: safety glasses, nobody downrange,
> and the **0.2 power cap** stays. If the shooter can't reach 12 m, the
> mentor shrinks the range.

## Step 1: Can you trust the camera? (calibration)

Before the robot can use the camera's distance, check it against
something you **do** trust: a tape measure.

1. Put the bucket, with its tag, at **8, 9, 10, 11, and 12 m**, measured
   with a tape measure from the camera.
1. At each one, write down the distance the **camera** reports (in
   Glass, or printed by your code). Take 3 readings each.
1. Make this graph: **true distance** (tape measure) across the bottom,
   **camera distance** up the side, one point per reading. Draw the line
   where they'd be equal (camera = true).

> **Check yourself:**
> 1. Are your points on the "equal" line, or off to one side? By how
>    much, at 8 m and at 12 m?
> 2. Do the 3 readings at each distance agree with each other? Which is
>    worse, near or far? (Compare with your
>    [limits lab](25_BREAK_THE_CAMERAS.md#lab-2-measure-the-limits).)
> 3. If the camera is always off the **same way**, you can **correct** it:
>    write a rule like `true = a + b × camera`. That's **calibration**.
>    What are your `a` and `b`?

## Step 2: Build a shot table

Now find the right drum speed for each distance, with the tape measure
(no camera yet).

1. Put the bucket at **8 m**. Change your drum speed until shots land in
   the bucket reliably. Write down that speed.
1. Do the same at **9, 10, 11, and 12 m**.

| Distance (m) | Drum speed that works |
| --- | --- |
| 8 | |
| 9 | |
| 10 | |
| 11 | |
| 12 | |

> **Check yourself:** the bucket ends up at **10.5 m**. That's not in
> your table. What speed would you use? (Look up **interpolation**.)

## Step 3: Put it together

**Goal:** when you hold **B**, your shooter study:

1. reads the distance to the tag from the camera,
1. corrects it with your Step 1 rule,
1. looks up the drum speed in your Step 2 table (interpolating between
   rows),
1. spins up to that speed, and shoots, just like before.

**Checkpoint:** with the bucket at a distance you **didn't** use in your
table (like 9.5 m), the robot picks a sensible speed by itself, and
prints the distance and speed it chose.

<details>
<summary>Hint: where's the camera's data?</summary>

The Pi sends what it sees over **NetworkTables**, the same system Glass
shows you. Find the tag's values in Glass first. Then look up how to
read a NetworkTables value from Java in the
[WPILib docs](https://docs.wpilib.org/en/2027/docs/software/networktables/index.html),
or ask a mentor.

> **TODO (mentors):** add which NetworkTables topic has the tag
> distance.
</details>

<details>
<summary>⭐ Stretch: use the pivot too</summary>

The demo bot can also tilt its shooter (the **pivot**). Can you pick
**both** an angle and a speed for each distance? Is there more than one
combination that works? Which one is more forgiving if the distance is
a little wrong?
</details>

## The challenge

1. The team waits out of sight. A mentor puts the bucket at a **secret**
   distance between 8 and 12 m, with the tag facing the robot.
1. The robot starts in its taped spot, pointing at the bucket (aim by
   eye is fine).
1. Load **5 balls**, hold **B**. Nobody touches anything during the run.

**Score = balls in the bucket ÷ seconds**, same as the
[Shooter Challenge](../part2/12_DEMO_BOT.md#scoring). An official round
is **3 runs at 3 different secret distances**; add up your 3 scores.

Post your total, the distances the robot **thought** it saw, and a
video to **`#rookie_training`**.

### Records

| Total score (3 runs) | Who | Date | Slack proof |
| --- | --- | --- | --- |
| — | | | |

> **Check yourself:** compare your score here with your tape-measure
> Shooter Challenge score. Which was better? What did the camera fix,
> and what new problems did it bring?

Next: [🏆 Park on the X, from anywhere](27_PARK_FROM_ANYWHERE.md)
