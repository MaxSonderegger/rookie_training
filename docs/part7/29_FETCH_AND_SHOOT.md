# 🏆 Fetch and shoot

This is the final challenge. It puts **everything** together.

The demo bot starts on the floor with an empty hopper. By itself, with
nobody touching the controls, it has to:

1. **find** the yellow balls with its camera ([Part 6](../part6/28_OBJECT_DETECTION.md)),
1. **drive** to them and **pick them up** ([Part 3](../part3/14_AUTON.md)),
1. **drive to the X** on the floor and **know** it's there
   ([Park on the X, from anywhere](../part5/27_PARK_FROM_ANYWHERE.md)),
1. **shoot** them into the hopper ([Part 2](../part2/12_DEMO_BOT.md) and
   [Part 5](../part5/26_VISION_SHOOTER_CHALLENGE.md)).

It runs on the **SystemCore**, on the `2027` branch.

> 🚧 **Not ready yet.** Some pieces (sending ball positions to the
> robot, picking balls up) are still being built. Start with the pieces
> that work, and add the rest as they're ready.

> **TODO (mentors):** how the demo bot picks balls up off the floor (an
> intake?), what the "hopper" is and how high, and the camera(s) for
> tags and balls.

> [!CAUTION]
> The robot drives **and shoots** by itself. Safety glasses for
> everyone, nobody on the course or in front of the hopper, and one
> person on the Driver Station with a finger on **Space** (e-stop).

## The course

> 📷 **TODO photo:** docs/img/fetch_and_shoot_course.jpg: start area, the
> balls, the X, the hopper, and the wall tags.

* **Start:** a taped box. The robot starts inside it, facing any way.
* **Balls:** **5 yellow balls**, put down by a mentor in secret spots
  before each run. No decoys at first; add them once you're good.
* **The X:** taped on the floor, where the hopper is in range. Its
  position relative to the wall tags is on a card, like in Park on the X.
* **Hopper:** a target the balls have to land in.

## Build it one piece at a time

Don't write it all at once. Get each row working, and **show a mentor**,
before you start the next.

| # | Piece | Checkpoint | You learned it in |
| --- | --- | --- | --- |
| 1 | Shoot from the X | Robot placed on the X by hand shoots 5 balls; most go in | [Shooter Challenge](../part2/12_DEMO_BOT.md#-the-shooter-challenge) |
| 2 | Drive to the X | From a random start, ends within 20 cm | [Park on the X, from anywhere](../part5/27_PARK_FROM_ANYWHERE.md) |
| 3 | 1 + 2 together | Drive to the X, then shoot, all in auton | [Park and shoot](../part5/27_PARK_FROM_ANYWHERE.md#records) stretch |
| 4 | See a ball, in the robot code | The ball's position shows up in Glass, and it's about right (check with a tape measure) | [Spot the balls](../part6/28_OBJECT_DETECTION.md#coming-next) |
| 5 | Drive to one ball | Robot stops with the ball right in front of it | Part 3 + Part 6 |
| 6 | Pick up one ball | Ball ends up inside the robot | TODO (mentors) |
| 7 | Fetch all of them | Picks up balls until it can't see any more | — |
| 8 | **Everything** | Fetch, drive to the X, shoot | — |

<details>
<summary>Hint 1: the plan is a list of steps</summary>

Write the whole run as a **sequence** of commands, like the steps in
the table: "fetch until no balls are left, then drive to the X, then
shoot until empty". You used `sequence(...)` and `.until(...)` in the
[auton chapter](../part3/14_AUTON.md#commands-doing-things-in-order).
</details>

<details>
<summary>Hint 2: which ball first?</summary>

The closest one is the easy answer. Is it always the best? What if the
closest ball is the opposite way from the X?
</details>

<details>
<summary>Hint 3: what if it loses sight of the ball?</summary>

When the robot drives right up to a ball, the ball may drop out of the
camera's view. Remember where it **was** (in field coordinates, not
camera coordinates), and keep driving there.
</details>

## Scoring

* **Score = balls in the hopper ÷ seconds**, from auton start until the
  last shot (or the end of time). Same idea as the
  [Shooter Challenge](../part2/12_DEMO_BOT.md#scoring).
* **2 minutes** per run.
* Anyone touching the robot, or an e-stop, ends the run; balls already
  in still count.
* An official round is **3 runs** with the balls in different secret
  spots each time. Add up your 3 scores.

Post your total and a video of each run to **`#rookie_training`**.

### Records

| Total score (3 runs) | Robot | Who | Date | Slack proof |
| --- | --- | --- | --- | --- |
| — | | | | |

> **Check yourself:** when a run goes wrong, which piece failed? Seeing,
> driving, picking up, knowing where it is, or shooting? Use the table
> above to find it, then fix that one piece on its own.

That's the whole guide. Congratulations! Now go fix something in it
that confused you: [Help improve this guide](../../README.md#help-improve-this-guide).
