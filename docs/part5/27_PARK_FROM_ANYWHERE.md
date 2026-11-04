# 🏆 Park on the X, from anywhere

In [Park on the X](../part3/15_PARK_ON_THE_X.md) the robot always
started on a taped spot, so it knew where it began. Real matches aren't
that tidy: robots get bumped, wheels slip, and drivers line up a little
wrong. Odometry can't fix any of that by itself
([remember the kidnapped robot?](../part3/13_LOCALIZATION.md#experiment-3-the-kidnapped-robot)).

Now the robot has a camera. In this challenge it starts **somewhere
random** and has to find itself by looking at an **AprilTag**, then
drive to the X.

Use the demo bot on the **SystemCore**, with the `2027` branch, like
the rest of Part 5. Start from your
[SystemCore Park on the X code](../part4/20_PARK_ON_THE_X_SYSTEMCORE.md).

> **TODO (mentors):** the demo bot needs a **forward camera (Pi + USB
> camera)** feeding localization, plus the tag family and size we print.
> Add the setup here.

> [!CAUTION]
> The robot drives by itself. Clear the floor, keep everyone **behind the
> start area**, and keep one person on the Driver Station with a finger
> on **Enter** (disable). Start with the drive speed turned **down**.

## How vision fixes the pose

From [How our vision works](23_VISION.md): when a camera sees an
AprilTag, and the robot knows where that tag is on the field, it can
work out where the robot must be. That doesn't drift, but it's
**jumpy** (every picture is a little different), **late** (about a
tenth of a second old), and only works when a tag is in view.

So the robot uses both:

* **Odometry** for smooth, instant updates, every cycle.
* **Vision** to **nudge** the estimate back toward the truth whenever a
  tag is seen. Far-away tags are less trustworthy, so they nudge less.
* Because a vision answer is late, the robot applies it to where it
  **was** when the picture was taken, then replays the wheel moves
  since then.

The team's notes on this are in the
[localization README](https://github.com/Team100/all26/blob/2027/lib/src/main/java/org/team100/lib/localization/README.md),
and the math is in
[MATH.md](https://github.com/Team100/all26/blob/2027/lib/src/main/java/org/team100/lib/localization/MATH.md).
Read the README; the math is optional.

> **Check yourself:**
> 1. Why not just use vision, and skip odometry?
> 2. A camera sees a tag 8 m away. Should that change the robot's pose a
>    lot or a little? Why?
> 3. Which of your [localization experiments](../part3/13_LOCALIZATION.md)
>    would vision have fixed?

**Try it:** with the robot still, watch its pose in Glass while you
cover the camera, then uncover it. Then carry the robot 2 m (the
kidnapped robot again) and let it see a tag. What does the pose do?

## The course

The same X as before, plus:

1. **One or two AprilTags on a wall**, at robot-camera height.
1. A card that says where the X is **relative to the tags**, measured
   with a tape measure. For example: *"The X is 3.2 m in front of tag 1
   and 0.8 m to its left. Park facing the tag."*

> 📷 **TODO photo:** docs/img/park_from_anywhere_course.jpg: the wall
> tags, the X, and the start area, with distances labeled.

## The challenge

Before each run, a mentor puts the robot somewhere in the start area,
**pointing a random way** (but able to see a tag). The robot doesn't
know where it is, and neither do you.

**Goal:** when auton starts, your robot:

1. looks at the tag and works out its **own pose**,
1. plans a path from **wherever it is** to the X,
1. drives there and stops, facing the right way.

<details>
<summary>Hint 1: where is the X, in field coordinates?</summary>

The card gives the X relative to a tag. The robot knows where every tag
is on the field (that's how vision works out the robot's pose). So:
**X pose = tag pose + the offset on the card**. Work it out on paper
first, with a drawing.
</details>

<details>
<summary>Hint 2: when do you plan the path?</summary>

In the auton chapter you planned the path when the command *started*,
from the robot's pose at that moment. That's exactly what you want here,
as long as the pose is already right. What happens if auton starts
before the camera has seen a tag?
</details>

<details>
<summary>Hint 3: the demo bot can't slide sideways</summary>

Same as before: turn toward the X first, or use a path that curves in,
and then turn to the final heading.
</details>

**Checkpoint:** from 3 different random starts, the robot ends within
**20 cm** of the X every time, without anyone touching it.

## Scoring

Same as [Park on the X](../part3/15_PARK_ON_THE_X.md#scoring): total cm
error over **3 different random starts**, lower is better, 15 seconds
per run, and a touch or a disable scores 200 cm.

Fill in the
[two kinds of miss](../part3/15_PARK_ON_THE_X.md#two-kinds-of-miss)
table for every run. A localization miss now means: check the camera
([Set up a new camera](24_NEW_CAMERA.md)).

Post your score, a video, and the table to **`#rookie_training`**.

### Records

| Total error (3 runs) | Robot | Who | Date | Slack proof |
| --- | --- | --- | --- | --- |
| — | | | | |

<details>
<summary>⭐ Stretch: park and shoot</summary>

Put the [bucket](26_VISION_SHOOTER_CHALLENGE.md) in front of the X,
with its tag. Park on the X from a random start, then shoot 5 balls into
it, all in auton. **Score = balls in ÷ seconds** for the whole run.
</details>

> **Check yourself:** compare with your taped-start score. Is "from
> anywhere" worse? Is it worse because of localization, or tracking?

Next: [Part 6: Spot the balls](../part6/28_OBJECT_DETECTION.md)
