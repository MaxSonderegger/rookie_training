# 🏆 Park on the X, again (SystemCore)

Same challenge as [Part 3](../part3/15_PARK_ON_THE_X.md), new stack: the
demo bot now runs on the **SystemCore**, with the `2027` branch and
2027 WPILib.

**The rules don't change.** Same course, same card, same three starts
and die rolls, same scoring (total cm error over 3 runs, lower is
better). Still no cameras: wheels only. Only the robot's insides are
different.

> **TODO (mentors):** which demo bot is converted to the SystemCore, and
> whether its drive is set up the same way as in Part 3.

## Step 1: Move your auton over

Bring your Park on the X code from `main` to the `2027` branch, the same
way you moved your [shooter study](19_DEMO_BOT_SYSTEMCORE.md#step-1-move-your-shooter-study-to-2027).

**Checkpoint:** it builds, and it runs in the **simulator** on `2027`
before it touches the robot.

> **Check yourself:** what did you have to change? Make a list. Was any
> of it about the auton itself, or was it all about the new stack?

## Step 2: Check the pieces

Before you run the challenge, check each piece on its own. A problem is
much easier to find one piece at a time.

| Check | How | Good answer |
| --- | --- | --- |
| Wheels count the right way | on blocks, drive forward gently | both `position (m)` lines go **up** ([Experiment 1](../part3/13_LOCALIZATION.md#experiment-1-drive-without-moving)) |
| Distance is right | drive until Glass says x = 1.00 | within a few cm on the tape measure ([Experiment 4](../part3/13_LOCALIZATION.md#experiment-4-one-meter-for-real)) |
| Heading is right | the spin test, 4 turns | within a few degrees ([Experiment 5](../part3/13_LOCALIZATION.md#experiment-5-the-spin-test)) |

## Step 3: Run the challenge

Exactly the [Part 3 rules](../part3/15_PARK_ON_THE_X.md#scoring). Fill in
the "two kinds of miss" table for every run and post it, with your
score and a video, to **`#rookie_training`**.

### Records (SystemCore)

| Mode | Total error (3 runs) | Robot | Who | Date | Slack proof |
| --- | --- | --- | --- | --- | --- |
| Easy | — | | | | |
| Hard | — | | | | |

> **Check yourself:** compare with your Part 3 score.
> 1. Better, worse, or the same?
> 2. Look at your "two kinds of miss" tables. Did the **localization**
>    misses change, or the **tracking** misses?
> 3. If something got worse, was it the new controller, the new code, or
>    something else (a different robot, a different floor, a battery)?
>    How could you tell?

Next: [Part 5: SSH into the Pis](../part5/21_SSH_AND_CHECKS.md)
