# Put it together on the demo bot

You'll take everything from Parts 1 to 3 and
make it work on a **SystemCore**: move your shooter study from
[hard mode](../part2/12_DEMO_BOT.md) over, get it running on the demo
bot with its new controller, and check every system you've learned.

Do this as a **team of two or three**, with a mentor.

## Step 1: Move your shooter study to `2027`

You did this once already with your motor study, in
[Switch to the SystemCore](16_SYSTEMCORE.md#5-move-your-motor-study-to-the-systemcore).
Do the same for your shooter study: new branch from `2027`, new imports,
`CTREStartup` if you use any CTRE motors, and a `CanBusId` for every
motor.

**Checkpoint:** it builds on `2027`. Errors? See the
[Build errors FAQ](../reference/BUILD_ERRORS.md).

> **Check yourself:** the SystemCore has five CAN ports. Which one is
> each of the demo bot's motors plugged into? How do you find out
> without guessing?

## Step 2: Bring up the robot

Work through the checklist, one system at a time. Don't move on until
each one passes.

| ✔ | System | How you check it | Chapter |
| --- | --- | --- | --- |
| ☐ | Radios in **SystemCore Mode**, laptop gets `10.1.0.x` | `ping 10.1.0.2` | [Connect to the robot](17_ROBOT_NETWORK.md) |
| ☐ | You can log in to the SystemCore | `ssh systemcore@robot.local` | [SSH](16_SYSTEMCORE.md#6-ssh-into-the-systemcore) |
| ☐ | The robot knows **which robot** it is | serial number matches | [Identity](18_IDENTITY.md) |
| ☐ | CAN traffic on the right ports | `ip -s link show can_s0` … | [CAN](16_SYSTEMCORE.md#4-can-on-the-systemcore) |
| ☐ | Your study deploys | `./gradlew deploy` | [Deploy](16_SYSTEMCORE.md#3-deploy-to-a-systemcore) |
| ☐ | Shooter drums spin the right way, under 0.2 | on blocks, safety glasses | [Hard mode](../part2/12_DEMO_BOT.md#step-4-spin-both-drums-the-right-way) |
| ☐ | Shooter feeds one ball at a time | aimed at a net | [Hard mode](../part2/12_DEMO_BOT.md#step-6-feed-one-ball) |
| ☐ | Glass graphs: drum speeds, currents, battery | Glass connected to `robot.local` | [Glass](../part2/10_GLASS_POWER.md) |

**Checkpoint:** every box ticked, signed off by a mentor.

## Step 3: Show it off

Run a short demo for the team: drive, shoot, and explain one thing that
went wrong during bring-up and how you found it.

Next: [🏆 Park on the X, again (SystemCore)](20_PARK_ON_THE_X_SYSTEMCORE.md)
