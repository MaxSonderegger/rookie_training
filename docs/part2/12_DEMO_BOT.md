# Hard mode: the shooter bot

The **demo bot** is a whole robot that **shoots foam balls**. In this
chapter you'll write **your own study** that makes the shooter work:
spin up the shooter drums, wait until they're fast enough, then feed in
one ball at a time.

You've got everything you need from Part 1 and the Discus. This time
there are more motors, a new kind of motor controller, and real safety
limits.

![The demo bot: LED panel, indexer, shooter drums, Minion motor, breaker, and a foam ball](../img/demo_bot.jpg)

| Part | What it does |
| --- | --- |
| **Shooter drums** | Two wheels spinning in opposite directions. The ball is squeezed between them and flung out |
| **Indexer** | A wheel that pushes one ball at a time into the drums |
| **Motors** | Small brushless motors on **REV SPARK** controllers (not Krakens) |

## Safety first

> [!CAUTION]
> **This robot shoots balls at face height.** Before anyone enables it:
>
> * Everyone nearby wears **safety glasses**.
> * Nobody stands in front of the shooter. Point it at a net or a wall.
> * One person keeps a finger on **Enter** (disable), and says
>   **"Enabling!"** before enabling.
> * **Shooter power is capped at 20% (duty cycle 0.2).** Your code must
>   never send the shooter more than that. Raising it needs a mentor.

Why a cap? A ball's danger to an eye depends on its **speed**. Our team
rule is a top speed of about **20 m/s**, because faster than that, a
foam ball carries enough energy to injure an eye. Keeping the shooter at
low power keeps the ball well under that.

## Connect to the demo bot

Two ways. Both work for deploying and for the Driver Station.

### A: USB cable

Plug a **USB-B cable** straight from your laptop into the demo bot's
RoboRIO, just like the test board in [Deploy](../part1/04_DEPLOY.md).

### B: Wi-Fi (`100_Gamma`)

The demo bot's radio has its own Wi-Fi network, so you don't need a
cable.

1. **Turn the robot on**, and wait about a minute for the radio to
   start.
1. **Join the Wi-Fi network `100_Gamma`** from your laptop's Wi-Fi list
   (Windows: the Wi-Fi icon by the clock; Mac: the Wi-Fi icon in the menu
   bar). **Ask a mentor for the password**; it isn't written here,
   because this guide is public.
1. **Check you can reach the robot.** In a terminal (Windows: Command
   Prompt; Mac: Terminal):
   ```
   ping 10.1.0.1
   ping 10.1.0.2
   ```
   (on a Mac, `ping -c 4 …`). `10.1.0.1` is the radio; `10.1.0.2` is the
   RoboRIO. Both should answer.
1. **Deploy your code** the usual way: `Ctrl+Shift+P` → **WPILib:
   Deploy Robot Code**. WPILib finds the robot over Wi-Fi by itself,
   because the team number is `100`.
1. **Open the Driver Station** (Windows only), team `100`.
   **Communications** and **Robot Code** should go green.
1. **Glass** connects the same way: **Team** → **100**
   ([how](10_GLASS_POWER.md#open-glass-and-connect)).

If it doesn't work:

* **`100_Gamma` isn't in the list:** the robot is off, still starting,
  or its radio is linked to an access point, which turns this Wi-Fi off.
  Ask a mentor.
* **Joined, but `ping 10.1.0.2` fails:** wait 30 seconds; the RoboRIO
  starts after the radio. Check its lights.
* **Deploy can't find the robot:** check `ping 10.1.0.2` first. If ping
  works, and the deploy still fails, see
  [Deploy to the RoboRIO](../part1/04_DEPLOY.md#if-the-deploy-fails).
* **Your laptop has no internet** while it's on `100_Gamma`. That's
  normal. Switch back to the school Wi-Fi to push to GitHub or use
  Slack.

> Tested 2026-11-04 from a Mac: on `100_Gamma`, the radio, the RoboRIO
> (by address and by name), the deploy port, and NetworkTables all
> answered, in about 7 ms.
>
> **TODO (mentors):** which demo bot ("A" or "B") `100_Gamma` is on.

### Whichever way you connect

Keep the robot **on blocks** (wheels off the floor): you're testing the
shooter, not driving. More about radios comes in
[Part 4](../part4/17_ROBOT_NETWORK.md).

## Step 1: The motors and their CAN IDs

Every motor is on a **REV SPARK MAX** controller, and each SPARK has a
**CAN ID**, the number your code uses to find it. These are the demo
bot's:

| Mechanism | CAN ID | Motor |
| --- | --- | --- |
| Shooter drum A | **39** | CTRE **Minion** |
| Shooter drum B | **8** | CTRE **Minion** |
| Indexer | **7** | CTRE **Minion** |
| Pivot (tilts the shooter) | **5** | REV **NEO 550** |
| Drive, left | **3** | REV **NEO 550** |
| Drive, right | **2** | REV **NEO 550** |

For this chapter you need the two **drums** and the **indexer**. Leave
the pivot and the drive alone. Which drum is "A"? You'll find out in
Step 3.

> [!IMPORTANT]
> **‼️ If these CAN IDs don't work, you may need to find them using the
> instructions in
> [Appendix: find the CAN IDs yourself](#appendix-find-the-can-ids-yourself).**
> Signs they're wrong: a motor doesn't move when your code tells it to,
> the wrong motor moves, or the Driver Station console shows errors
> about a SPARK with that ID. Someone may have rewired the robot, or
> you may be on a different demo bot.

> **TODO (mentors):** check this table on both demo bots ("A" and
> "B"), and put the CAN ID on masking tape on each SPARK.

**Checkpoint:** you can point to each of the three motors on the robot,
and say its CAN ID.

## Step 2: Your shooter study

**Goal:** a new study, with `lib` included, that creates **both
shooter drum motors** and the **indexer motor**. Low current limits.

### The motor's arguments

Each motor is a **`MinionSparkMotor`** (a Minion motor on a SPARK MAX),
in `lib`'s `motor/rev` folder. Can't find it? Your study's `lib` isn't
open: see [the FAQ](../reference/BUILD_ERRORS.md#opened-the-folder-instead-of-the-workspace).

Its constructor takes ten arguments. The first eight are the same as the
Kraken's in
[Control a motor from code](../part1/08_MOTOR_CODE.md#step-3-create-the-motor);
the last two are new. What each one means, and what to pass **for now**:

| # | Argument | What it is | For now |
| --- | --- | --- | --- |
| 1 | `LoggerFactory parent` | Where this motor's logs go in Glass. Give each motor its **own name**, or their logs land on top of each other | `log.name("drum A")`, `log.name("drum B")`, `log.name("indexer")` |
| 2 | `TotalCurrentLog currentLog` | Adds up the current used by every motor | the same one for all three, from `RobotLog`, as in Part 1 |
| 3 | `CanId canId` | Which SPARK to talk to | from the [Step 1 table](#step-1-the-motors-and-their-can-ids): `new CanId(39)`, … |
| 4 | `NeutralMode100 neutral` | What the motor does when you send it 0: **coast** (spins down freely) or **brake** (stops hard) | `COAST`. Braking heavy drums suddenly is hard on them |
| 5 | `MotorPhase motorPhase` | Which way counts as "forward" | `FORWARD` for all three. [Step 4](#step-4-spin-both-drums-the-right-way) is about changing this |
| 6 | `CurrentLimit limit` | The most current the motor may draw. Takes a **stator** and a **supply** number, but the SPARK only uses the **stator** one; nothing limits supply current here | `new CurrentLimit(10, 10)`: low, while you're testing |
| 7 | `Friction friction` | A model of friction, for fancier control | all zeros: not used yet |
| 8 | `PIDConstants pid` | Feedback control settings | `PIDConstants.zero()`: not used yet |
| 9 | `int averageDepth` | **New.** How many speed readings the SPARK averages | `0` (REV's default) |
| 10 | `int measurementPeriod` | **New.** Over how many milliseconds the SPARK measures each speed reading | `0` (REV's default) |

About the last two: the SPARK works out speed by watching how far the
motor turned over a stretch of time, and averaging. **Bigger numbers**
give a smoother speed reading, but a **later** one; **smaller** numbers
give a quicker but noisier one. `0` means "REV's default", which is fine
until [Step 5](#step-5-know-when-the-drums-are-ready), where you'll see
why it matters for a shooter.

> **Check yourself:** why does the table say "for now" for almost every
> row? Which ones will you change later in this chapter, and why?

**Checkpoint:** before you build, you can say what every argument does.
Then: it builds, deploys, and prints your "motors created" messages.

**Build errors?** See the [Build errors FAQ](../reference/BUILD_ERRORS.md).

## Step 3: Spin one drum

**Goal:** while you hold the **right bumper**, **one** shooter drum
spins at **10%**. Let go, and it stops.

Robot **on blocks**, safety glasses on, no balls anywhere near it.

**Checkpoint:** you know which drum it is, and which way it spins.

<details>
<summary>Hint 1: reading the bumper</summary>

You made a `DriverXboxControl` in
[Drive it with an Xbox controller](../part1/08_MOTOR_CODE.md#step-7-drive-it-with-an-xbox-controller).
It has one method for each button. Find the one for the right bumper.
What type does it return? Why that type, and not a `double` like the
sticks?
</details>

<details>
<summary>Hint 2: "while you hold it"</summary>

`teleopPeriodic()` runs 50 times a second. So each time, ask: "is the
bumper pressed **right now**?" If yes, set the duty cycle to 0.1.
Otherwise... what?

> **Check yourself:** what happens if you forget the "otherwise" part,
> and you let go of the bumper?
</details>

<details>
<summary>Hint 3: which drum is it?</summary>

Put a strip of masking tape on each drum, so you can see it turning.
Then write down which drum is A (CAN ID 39) and which is B (8), where
each one is (top or bottom), and which way it spins.
</details>

## Step 4: Spin both drums the right way

The drums must spin in **opposite directions**, so the ball gets pulled
in between them and flung out (not pushed back at you).

**Goal:** the right bumper spins both drums, in the right directions,
at the same power, never above **0.2**.

**Checkpoint:** a mentor checks both directions **before** any ball
goes near the robot.

<details>
<summary>Hint 1: flipping one motor</summary>

You don't have to send one motor a negative number. One of the
motor's constructor arguments says which way is "forward".
</details>

<details>
<summary>Hint 2: which way is "right"?</summary>

Look at the drums from the **side**, and find the **gap** between them,
where the ball goes through. At the gap, both drum surfaces must move
the same way: **out of the front** of the robot. Draw it with arrows
before you change any code. Then check with the tape strips from Step 3,
at 10% power, before you go any faster.
</details>

<details>
<summary>Hint 3: making the 0.2 cap impossible to break</summary>

Don't type `0.2` all over your code. Make **one** constant with a clear
name, and make sure every number you send the drums goes through a
check against it first. Look up `Math.min`, or WPILib's
`MathUtil.clamp`.

> **Check yourself:** later, someone changes your 0.1 to 0.5 by
> mistake. What does your code send the drums?
</details>

## Step 5: Know when the drums are ready

A ball fed into slow drums just dribbles out. So the robot needs to
**know** when the drums are up to speed.

**Goal:** print (or log) whether the drums are **ready**: both at least
as fast as a target speed you choose.

**Checkpoint:** in [Glass](10_GLASS_POWER.md#make-a-graph), graph both
drum speeds as they spin up. Mark where they cross your target. How long
does spin-up take?

> **Nothing under `log` in Glass?** Your new study needs the same
> `robotPeriodic()` calls as before
> ([Step 2](../part1/08_MOTOR_CODE.md#step-2-keep-the-library-ticking)).

<details>
<summary>Hint 1: reading the speed</summary>

Measure each drum's speed like you did on the Discus: `encoder()` gives
you the motor's speed in radians per second. Pick a target a bit below
the speed they reach at your power, so "ready" happens a moment before
they level off.
</details>

<details>
<summary>Hint 2: "both" drums</summary>

"Ready" is true only if drum 1 is fast enough **and** drum 2 is fast
enough. Look up Java's `&&`. Make it a method, like `boolean ready()`,
so Step 6 can ask it.
</details>

<details>
<summary>Hint 3: meters per second, not radians</summary>

What matters for the ball is how fast the drum's **surface** moves. Measure the
drum's diameter with a ruler. Surface speed (m/s) = speed (rad/s) ×
radius (m). Does that match how fast the ball comes out?
</details>

<details>
<summary>Hint 4: the graph looks late</summary>

Look closely at your spin-up graph. Does the speed line rise **after**
you can hear the drums speed up? The SPARK works out speed by averaging
over a stretch of time, and REV's default is long, so the reading lags
behind the real drum. That's what the `averageDepth` and
`measurementPeriod` arguments from Step 2 control.

With a mentor, try smaller values (read the comments in `lib`'s
`RevConfigurator` for the allowed range), and graph the spin-up again.
What do you gain? What gets worse? (Look at how smooth the line is.)
</details>

## Step 6: Feed one ball

**Goal:** while you hold the **B** button, the drums spin up, and **only
when they're ready**, the indexer turns just long enough to push in
**one** ball. Then it waits for the drums to recover, and feeds the next.

**Checkpoint:** with safety glasses on and the robot aimed at a net,
hold **B** and watch it shoot a few balls, one at a time, all at about
the same distance.

<details>
<summary>Hint 1: break it into small steps</summary>

Write down, in plain words, what should happen in order: spin up →
wait until ready → feed for a short time → stop feeding → wait until
ready again → … Then turn each line into code.
</details>

<details>
<summary>Hint 2: first, the indexer on its own</summary>

Before you combine anything: with **no balls** in the robot, make **X**
spin just the indexer at a low power while you hold it. Which way does it
push a ball: toward the drums, or back out? Fix that the same way as in
Step 4.
</details>

<details>
<summary>Hint 3: "for a short time" needs a clock</summary>

To feed "for 0.2 seconds", remember **when** you started feeding, and
each cycle compare that with the time now. WPILib's `Timer` class can
tell you the time. Too short, and the ball doesn't make it in. Too
long, and a second ball follows it. Try a few times, and watch.
</details>

<details>
<summary>Hint 4: one way to write it</summary>

Keep a variable that says what the shooter is doing **right now**:
"spinning up", "feeding", or "recovering". Each cycle, look at it, and
decide whether to move on to the next one. Draw it as boxes and arrows
first: what makes you go from each box to the next?

Or use **commands**. This is exactly what they're for: small actions
you can put "in sequence", "in parallel", "until something is true",
and "repeatedly". Read
[WPILib: command compositions](https://docs.wpilib.org/en/stable/docs/software/commandbased/command-compositions.html).
</details>

> **Check yourself:** you let go of **B** in the middle of feeding a
> ball. What does your code do? What **should** it do?

> **Check yourself:** what happens to the drums' speed at the moment a
> ball goes through? Look at your Glass graph. Why?

## Step 7: The mystery graph

Same game as on the [Discus](11_DISCUS.md#step-5-the-mystery-graph),
now with the whole shooter. One of you shoots; the other has to work
out **what happened, and when, just by looking at the graphs**.

1. **Both:** in Glass, graph both drum **speeds**, the drums' and the
   indexer's **stator** and **supply** currents, and the battery
   voltage. Make the plot show about the **last 30 seconds**.
1. **Person A** (safety glasses on, aimed at a net) secretly does a
   sequence, and writes it down: for example, spin up, shoot 2 balls,
   wait, shoot 1 more, stop. **Person B** looks away from the robot.
1. **Person A** takes a screenshot of the graph right away, and gives
   it to Person B.
1. **Person B** annotates it: mark when the drums **started** spinning
   up, when they were **ready**, every moment a **ball went through**,
   and when it **stopped**. Explain what gave each one away.
1. Compare with Person A's list. Then swap.

**Checkpoint:** post the annotated graph to **`#rookie_training`** as
proof, with how many events Person B got right.

> **Check yourself:**
> 1. How could you tell exactly when a ball went through? Which lines
>    moved, and which way?
> 2. Which drew more current: spinning the drums up, or keeping them
>    spinning? Why?
> 3. How long did the drums take to recover after each ball? Could you
>    shoot faster?

## Make it better

Pick one, plan it with a mentor, and send it in as a PR. Each one will
help you in the challenge below.

* Make the LEDs show when the shooter is **ready**.
* Add a button that **stops everything** at once.
* Make the shooter more consistent: does a little **feedback** (from the
  Discus) help the drums recover faster after each ball?

## 🏆 The Shooter Challenge

Get as many balls as possible into a bucket, as fast as possible.

### Setup: just a tape measure

No cameras, no fancy tools. On purpose.

1. Put a **bucket** at the challenge distance from the front of the
   robot, measured with a tape measure. Start at **10 m**, and the
   mentor moves it closer if the shooter can't reach.
   > **TODO (mentors):** set the real distance after the first runs,
   > and write it here.
1. Put a **net or wall behind the bucket**, and mark the robot's spot on
   the floor with tape, so every team starts from the same place.
1. Aim by eye. You may turn the robot by hand before the run, but not
   during it.
1. Load **5 balls**.

> [!CAUTION]
> Safety glasses for everyone. **Nobody downrange**, including whoever
> watches the bucket: stand to the side. The **0.2 power cap** stays:
> if you can't reach the bucket, the bucket moves, not the cap.

### Scoring

* **Your code** times the run (from pressing the button to the 5th ball
  leaving the robot) and counts the balls it fed. Print both at the end.
* **A human** counts how many balls landed **in the bucket** (and stayed
  in).

> **Score = balls in the bucket ÷ seconds**

That's **makes per second**. It rewards both accuracy *and* speed:

* `balls in ÷ balls shot` = your **accuracy**
* `balls shot ÷ seconds` = your **speed**
* score = accuracy × speed

All 5 balls must be shot for the run to count. Ties go to the more
accurate run.

**Example:** 3 balls in, in 6 seconds: score **0.5**. 4 balls in, in 10
seconds: score **0.4**. The first run wins, even with fewer balls in.

> **Check yourself:** if you shoot as fast as possible and only 1 goes
> in, in 3 seconds, what's your score? What if you shoot slowly and
> carefully: 5 in, in 20 seconds? What's the best strategy?

Practice as much as you like. For an **official** run, a mentor watches;
you get **3 tries**, and your best one counts. Post your score, the
printed time, and a video to **`#rookie_training`**.

### Records

Beat a record at a distance? Post the proof, then update this table
with a pull request.

| Distance | Score | Balls in | Seconds | Who | Date | Slack proof |
| --- | --- | --- | --- | --- | --- | --- |
| 10 m | — | | | | | |

### After the challenge: what did you have to guess?

Write down every guess you made: the bucket's exact distance, where the
robot was pointing, the right drum speed, how each ball would fly.

> **Check yourself:**
> 1. If someone moved the bucket half a meter while you weren't
>    looking, would your robot know? How could it find out?
> 2. What if a robot could **see** the bucket and measure the distance
>    itself? What would it need to know about its own camera to get the
>    distance right?

Keep your list. In [Part 5](../part5/23_VISION.md), robots use cameras to
measure where things are, and you'll see why a camera has to be
**calibrated** before its measurements can be trusted.

## Appendix: find the CAN IDs yourself

Only needed if the [CAN IDs in Step 1](#step-1-the-motors-and-their-can-ids)
don't work.

These motors use **REV SPARK** controllers, so **Phoenix Tuner X can't
see them**. Use the **REV Hardware Client** instead.

**Goal:** you know the CAN ID of each motor, and each one is written on
masking tape.

1. Find the REV Hardware Client on the laptop (Windows key, type "REV").
   If it's not there, download it
   [here](https://docs.revrobotics.com/rev-hardware-client).

   ![](../img/rev_hardware_client.png)

1. Plug a USB-C cable from the laptop straight into one **SPARK**, and
   click **Refresh**.

   ![](../img/rev_hardware_client_working.png)

1. The **CAN Bus** tab should say "Bus OK", and **CAN Data** should
   show messages flowing.

   ![](../img/can_bus_flowing.png)

1. Write down its CAN ID. To find out which motor this SPARK drives,
   spin it slowly from the client's run tab and watch.

> 📷 **TODO photo:** `docs/img/demo_bot_spark_usb.jpg`: a USB-C cable
> plugged into one of the demo bot's SPARKs.

**Checkpoint:** a table of motor → CAN ID, checked with a mentor. Fix
the table in [Step 1](#step-1-the-motors-and-their-can-ids) if yours
is different, and tell a mentor, so the next group doesn't get stuck.

Next: [Part 3: Where am I? (localization)](../part3/13_LOCALIZATION.md)
