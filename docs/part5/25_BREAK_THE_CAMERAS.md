# Break the cameras (on purpose)

At a competition, vision **will** break: a cable gets knocked loose, a
Pi browns out, a camera points at the stadium lights. The team that
finds the cause in two minutes keeps playing. The way to get that fast
is to **break things on purpose now**, on the bench, and learn what
each problem looks like.

What actually breaks at competitions?

| What goes wrong | Typical symptom |
| --- | --- |
| Ethernet or USB cables knocked loose by hits and vibration | Works in the pits, dies mid-match |
| The Pi browns out when the robot's battery sags | Pi reboots or slows down during hard driving |
| Lens smudged, covered, or knocked to a new angle | Sees nothing, **or** sees tags but reports the wrong position |
| Field lights or sun shining into the camera | Tags not detected on one side of the field |
| Two devices with the same IP address | Things work, then don't, then do |
| A bad deploy, or someone's test branch left on the robot | The app keeps crashing, or acts strangely |
| The field network blocks ports that work in the pits | "It worked in the pits!" |
| A Pi overheating in a closed case | Slows down after a few matches |

This chapter has two labs:

1. **The breaker vs. finder game:** in pairs, one person secretly breaks
   something, the other has to find out what. Then swap.
2. **Measure it:** find the limits of AprilTag detection: distance,
   angle, speed, and light.

You'll finish with a **symptom → cause table** that the whole team can
use.

> [!WARNING]
> **Do this on a bench Pi or a test robot, with a mentor. Never on the
> competition robot at an event.**
>
> * Shut the Pi down properly (`sudo shutdown -h now`, wait for the
>   green LED to stop) before pulling power. Pulling power while it's
>   running can corrupt the SD card.
> * Put everything back the way you found it, and check it works, before
>   you leave.

## Before you start: know what "healthy" looks like

You can't spot "broken" until you know "working". With a Pi connected to
your laptop or the robot ([how](21_SSH_AND_CHECKS.md#connect-your-laptop)),
write down what each of these shows when everything is fine:

| Where to look | What you see when healthy |
| --- | --- |
| `http://<pi-ip>:9001` (supervisor page) | `app` **RUNNING** |
| `http://<pi-ip>:1181` (camera view) | Live picture; a tag held up gets a box drawn on it |
| `app.stderr` (via `:9001` or `ssh`) | No repeating errors |
| `vcgencmd get_throttled` (via `ssh`) | `throttled=0x0` |
| `vcgencmd measure_temp` (via `ssh`) | The temperature, under ~80 °C |
| Glass, on the robot side | The vision values updating when a tag is in view |

> 📷 **TODO screenshot:** `docs/img/pi_healthy_1181.png`: the `:1181`
> camera view with a tag detected.

> **TODO (mentors):** add which NetworkTables values in Glass show
> vision data arriving at the robot.

## Lab 1: The breaker vs. finder game

At a competition, nobody tells you what broke. You see a symptom ("no
vision!") and have to work backwards to the cause, fast. This game
practices exactly that.

### Roles

* **Breaker:** picks a card from the
  [breaker cards](BREAKER_CARDS.md), secretly breaks one thing, and
  makes sure it's really broken. **Finders: don't read the breaker
  cards.** It spoils the game, and you won't learn anything.
* **Finder:** uses the [finder's guide](FINDER_GUIDE.md) to work out
  **what** broke and **show the evidence**.

### How a round works

1. **Both** of you check that everything is healthy (the table above).
   You can't find "broken" if it was already broken.
1. The finder **turns around** (or leaves the room) while the breaker
   breaks something, following the card's instructions.
1. The breaker writes the card number on a piece of paper and turns it
   over.
1. The finder starts a **10-minute** timer and follows the finder's
   guide. Say out loud what you're checking and why.
1. The finder names the cause **and shows the evidence** ("`lsusb` has
   no Arducam line, so the camera isn't connected").
1. Flip the paper. Then the **breaker** undoes the break, following the
   card's "undo" steps, and you **both** check everything is healthy
   again.
1. Write a row in your symptom → cause table. Swap roles.

### Scoring

* Each card has points: ⭐ easy (1), ⭐⭐ medium (2), ⭐⭐⭐ hard (3).
* The finder gets the points if they find the cause with evidence
  within 10 minutes.
* Stuck? The finder can ask for **one hint**: the breaker says which
  **layer** of the finder's guide it's in. A hint halves the points.
* The breaker gets 1 point if the finder runs out of time, **but only if
  the breaker can then show the evidence themselves.**

> [!IMPORTANT]
> A mentor approves which cards are in play before you start. Breakers:
> only use the cards. No "creative" breaks that aren't on a card: some
> things are hard to undo, and the point is learning, not winning.

### Checkpoint

You've each played both roles at least twice, and you have a symptom →
cause table like this:

| Card | What broke | First thing that looked wrong | Evidence | Time to find |
| --- | --- | --- | --- | --- |
| | | | | |

> **Check yourself:** for each break, did the **robot** know vision
> stopped, or did it keep using the last answer it got? Why does that
> matter for a robot that's driving?

## Lab 2: Measure the limits

Now find out how **good** detection really is. You'll need an AprilTag
printed at the right size, a tape measure, and a protractor (or your
phone's level app).

> **TODO (mentors):** add the tag family and print size we use.

For each test, use the `:1181` view to see whether the tag is detected.
Do each one **three times** and write down the results.

Our USB cameras are **Arducam OV9782**s. They have a **global shutter**:
the whole picture is taken at once, instead of line by line, which helps
with fast motion. Keep that in mind for the speed test.

| Test | How | Measure |
| --- | --- | --- |
| **Distance** | Hold the tag facing the camera, start close, and back away slowly | The farthest distance where it's still detected **every** time |
| **Angle** | At 2 m, turn the tag away from facing the camera | The biggest angle that's still detected |
| **Speed** | At 2 m, move the tag sideways, faster each time | The speed where it stops being detected (film it, or count seconds over a measured distance) |
| **Light** | Turn off the room lights, then shine a flashlight from behind the tag, then from the side | Which conditions work, and which don't |
| **Partly hidden** | Cover a corner of the tag with a finger, then two | How much can be covered before it's lost |

> **Check yourself:**
> 1. Why do you think fast movement makes detection fail? (Hint: what
>    happens to a photo of something moving fast?)
> 2. Our code trusts far-away tags **less** (see
>    [Where am I?](../part3/13_LOCALIZATION.md)). Does your distance
>    test agree that far tags are worse? How?
> 3. If the robot will see tags from 4 m away while driving at 2 m/s,
>    will it work, based on your measurements?

**Checkpoint:** your limits table, with the averages from your three
tries.

## Share what you learned

Post both tables in Slack, and add the useful rows to the
[Troubleshooting table in Change the Pi code](22_PI_CHANGES.md#troubleshooting)
with a pull request. The next person who sees "`:1181` is blank" at a
competition will thank you.

Next: [The Vision Shooter Challenge](26_VISION_SHOOTER_CHALLENGE.md)
