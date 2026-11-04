# Easy mode: the Discus

The **Discus** is a heavy aluminum disc spun by one motor. It's the
simplest "real" mechanism we have, and it's great for learning
controls: it has real weight (so it takes effort to speed up and slow
down), but it can't drive away or throw anything.

In this chapter you'll write **your own study** that controls it, step
by step: first just spinning it, then measuring it, then making it hold
a speed by itself. Nobody hands you the code. Each step has a goal, a
checkpoint, and hints if you're stuck, just like
[Control a motor from code](../part1/08_MOTOR_CODE.md).

![The Discus: a Falcon 500 motor, a 4:1 gearbox, and a heavy aluminum disc](../img/discus.jpg)

| Part | What it is |
| --- | --- |
| Motor | **Falcon 500** |
| Gearbox | **4:1**: the motor turns 4 times for each turn of the disc |
| Disc | Heavy aluminum, so it has a lot of **inertia** (resistance to speeding up or slowing down) |
| Controller | RoboRIO |

> [!WARNING]
> **The disc is heavy and can spin fast.** Clamp the Discus to the
> table. Keep hands, hair, and loose sleeves away from it while the
> robot is enabled. One person keeps a finger on **Enter** (disable).

> **Connecting:** plug a **USB-B cable** straight from your laptop into
> the robot's RoboRIO, just like the test board in
> [Deploy](../part1/04_DEPLOY.md). No radios needed: those come in
> [Part 4](../part4/17_ROBOT_NETWORK.md), on the real robot.

## Step 1: Find the motor

**Goal:** you know the Discus motor's CAN ID, and its firmware is ready
for our code.

Use Phoenix Tuner X, exactly like in [Your first motor](../part1/05_FIRST_MOTOR.md).

**Checkpoint:** the CAN ID is written on masking tape on the motor.

<details>
<summary>Hint</summary>

A Falcon 500 is a CTRE motor, so Tuner X can see it. Check its card for
**Phoenix 5** vs. **Phoenix 6** firmware: our code needs Phoenix 6.
</details>

## Step 2: Your Discus study

**Goal:** a new study, with `lib` included, that creates the Discus
motor. Use a **low current limit** to start.

You've done this before: [Your first study](../part1/07_STUDY.md), then
Step 3 of [Control a motor from code](../part1/08_MOTOR_CODE.md#step-3-create-the-motor).

**Checkpoint:** it builds, deploys, and prints your "motor created"
messages.

<details>
<summary>Hint</summary>

Which motor class represents a **Falcon 500**? Look in `lib`'s motor
folder, in the `ctre` part.
</details>

## Step 3: Spin it with the stick

**Goal:** the left stick controls the disc's duty cycle, capped at
**20%**.

**Checkpoint:** the disc follows the stick, gently, both directions.

> **Check yourself:** the disc keeps spinning for a while after you let
> go of the stick. Why? What does `NeutralMode100` have to do with it?
> Try changing it.

## Step 4: Measure it

**Goal:** you can see, in [Glass](10_GLASS_POWER.md#make-a-graph), a graph
of the disc's **speed**, **stator current**, and **supply current**
while you drive it with the stick.

Then work out the **disc's** speed, not just the motor's. Remember the
4:1 gearbox.

**Checkpoint:** a screenshot of your graph in Slack, and the disc's top
speed in **rotations per second**.

> **Check yourself:**
> 1. Push the stick to 20% and hold it. The disc speeds up, then levels
>    off. Why does it stop speeding up? (Think back-EMF, from
>    [How motors work](../part1/06_HOW_MOTORS_WORK.md#why-it-doesnt-melt-every-time-back-emf).)
> 2. Look at the current graph. When is the current biggest: while
>    speeding up, or at a steady speed? Why?

<details>
<summary>Hint</summary>

Your motor already logs its speed and currents. Find them in Glass
under `log`. Nothing there? Check your `robotPeriodic()`
([Step 2 of Control a motor from code](../part1/08_MOTOR_CODE.md#step-2-keep-the-library-ticking)). If you also print the speed, divide by the gear ratio to
get the disc's speed.
</details>

## Step 5: The mystery graph

Now use what you learned in the [rag test](10_GLASS_POWER.md#your-first-power-draw-test-the-rag-test)
on a real mechanism. One of you makes things happen to the Discus; the
other has to work out **what happened, and when, just by looking at
the graph**.

**Work in pairs** (a mechanical teammate is a great partner for this):

1. **Both:** in Glass, graph the disc's speed, **stator current**,
   **supply current**, and the battery voltage. Make the plot show about
   the **last 30 seconds**.
   > **TODO:** add where Glass's plot time-window setting is.
1. **Person A (the driver)** writes down a secret plan of 4 to 6 events,
   for example: "spin up to 15%, hold, slow down, reverse, stop,
   coast". Then does them, about 5 seconds each, and writes down the
   order. **Person B** looks away from the Discus.
1. **Person A** takes a screenshot of the graph right away, and gives
   it to Person B.
1. **Person B** annotates the screenshot: mark **when** each thing
   happened and **what** it was, using only the graph. Explain how you
   could tell ("the stator current jumped and the speed fell, so it
   slowed down here").
1. Compare with Person A's secret plan. Then swap.

**Checkpoint:** post the annotated graph to **`#rookie_training`** as
proof, with how many events Person B got right.

> **Check yourself:** which events were easy to spot, and which were
> hard? Which line on the graph gave each one away?

## Step 6: Speed vs. power

How **fast** can you spin the disc up? And what does that **cost** in
current? Real robots face this all the time: a shooter that spins up
faster draws more from the battery.

> [!WARNING]
> A mentor picks the **highest current limit** you may use. Keep the
> Discus clamped.

### Get ready

1. Pick a **target speed** that even 5% duty can reach (look at your
   Step 4 results). About **1 rotation per second** of the disc is a
   good start.
1. Change your study so a button starts the disc **from a stop** at a
   set duty cycle. Every run must start from a stop.
1. In Glass, graph: disc speed, stator current, supply current, battery
   voltage.

### How to measure one run

![How to read time to target from your Glass graph](../img/discus_how_to_measure.png)

For every run, write down: **time to target**, **highest stator
current**, **highest supply current**, **lowest battery voltage**.

### Experiment A: push harder

Keep the current limit the same (your mentor's number). Change only the
**duty cycle**: 5%, 10%, 15%, 20%. Do each one **3 times**.

Make this graph, one point per run:

![Template: duty cycle vs. time to target](../img/discus_template_a.png)

### Experiment B: change the current limit

Keep the duty cycle at **20%**. Change only the **stator current
limit**: 5, 10, 20, 30 A (or your mentor's numbers). Do each one
**3 times**.

Make this graph, one point per run:

![Template: stator current limit vs. time to target](../img/discus_template_b.png)

> **Look closely:** in Experiment B, what **shape** is the speed line in
> Glass while the disc speeds up? Is it the same shape as in
> Experiment A? Why might that be?

### Put it together: the tradeoff

Put **every** run from A and B on one graph:

![Template: time to target vs. peak supply current](../img/discus_template_tradeoff.png)

> **Check yourself:**
> 1. Which runs are fast **and** easy on the battery?
> 2. Is there a point where a lot more current only makes it a little
>    faster?
> 3. Which knob gave you more control: duty cycle or current limit?

### Design challenge

Your mentor gives you a budget, like: *"reach the target in under
**X** seconds, and never go over **Y** amps of supply current."* Pick a
duty cycle and current limit that meet it, and **prove it** with a
graph.

**Checkpoint:** post your three graphs and your design-challenge proof
to **`#rookie_training`**.

<details>
<summary>⭐ Stretch: energy and efficiency</summary>

How much of the battery's energy actually ends up in the spinning disc?

* **Energy from the battery** (in joules): every 20 ms, add up
  `battery voltage × supply current × 0.02`, from enable until the disc
  reaches the target.
* **Energy stored in the disc:** `½ × I × ω²`, where `ω` is the disc's
  speed in radians per second (`rotations/s × 2π`), and `I = ½ × mass ×
  radius²` (in kg and meters). Weigh and measure the disc!
* **Efficiency** = stored ÷ from the battery × 100.

![Template: time to target vs. efficiency](../img/discus_template_energy.png)

Where did the rest of the energy go? (Think about
[heat](../part1/06_HOW_MOTORS_WORK.md#heat), friction, and the
gearbox.) Are the fastest runs the most efficient, or the least?

</details>

## Step 7: Hold a speed by itself (feedback)

So far *you* control the power. Now make the code **hold a target
speed**, even if you slow the disc down by hand (gently, with a rag).

**Goal:** when you hold the **A** button, the disc spins up to **1
rotation per second** and stays there.

The idea is called **feedback**: every cycle, compare the speed you
**want** to the speed you **have**. The difference is the **error**.
Then set the power based on the error: big error, more power.

**Checkpoint:** a Glass graph showing the target speed and the actual
speed, getting close to each other.

<details>
<summary>Hint 1</summary>

`error = target − measured`. Then `power = k × error`, where `k` is a
small number you choose. Start very small.
</details>

<details>
<summary>Hint 2</summary>

Try a few very different values of `k`: tiny, medium, and big. Graph
each one. What's different about how the disc gets to the target?
</details>

> **Check yourself:** does the disc end up **exactly** at the target?
> If not, which way is it off, and why? (What would make `error`
> exactly zero, and what would the power be then?)

### Disturb it: watch the motor fight back

Now the fun part. While your code holds the speed, **push on the disc
with a rag** (gently at first; same safety as the
[rag test](10_GLASS_POWER.md#your-first-power-draw-test-the-rag-test)).
Your code doesn't know you're there. Can it keep the speed anyway?

1. Make Glass graph these four things, stacked like the template below.
   Your motor already logs its **output** and **stator current**. Log
   your **target** and **error** too, so they show up in Glass (ask a
   mentor how, or look at how your motor's values got there).
1. Hold **A** until the disc is steady at the target.
1. Press the rag on the disc for about **5 seconds**, then let go.
1. Screenshot right away, and mark when the rag went **on** and **off**.

![Template: speed, error, output, and current, with rag on/off marked](../img/discus_template_hold.png)

**Find these on your graph**, and circle each one:

* 🔍 The moment the rag goes on. Which line moves **first**?
* 🔍 A time when the **speed** looks almost normal, but the **output**
  and **current** say the motor is working much harder. That's
  feedback doing its job.
* 🔍 The moment you let go. What does the speed do right then? Why?
* 🔍 While the rag is on: is the error zero, or not quite? Compare it
  with when the rag is off.

Then try it again with a **bigger** `k`, and with a **smaller** one.
How does each one handle the rag?

**Checkpoint:** post your annotated graph to **`#rookie_training`**,
with your four 🔍 spots circled and one sentence about each.

## Step 8: Help it with a guess (feedforward)

In Step 7, feedback only reacted **after** something went wrong: no
error, no push. **Feedforward** is the opposite: a **guess** at how much
power a speed needs, sent **before** anything goes wrong. Good robots
use both.

Remember the `Friction` argument you set to zeros back in
[Control a motor from code](../part1/08_MOTOR_CODE.md#step-3-create-the-motor)?
By the end of this step, you'll know what it's for.

### 8a: Build a recipe

A **recipe** says "for this speed, use this much duty cycle". You'll
measure it.

1. Run the disc at a **fixed** duty cycle: 2%, then 4%, 6%, and so on
   up to 20%.
1. For each one, wait until the speed **stops changing**, then write
   down the steady speed.
1. Put one point per run on this graph, then draw the **best straight
   line** you can through your points.

![Template: steady speed vs. duty cycle](../img/discus_template_feedforward.png)

> **Check yourself:**
> 1. Does a straight line fit your points well?
> 2. Does your line go through **zero** (0 speed at 0% duty)? If not,
>    what does the gap at the bottom mean? (Try it: what's the smallest
>    duty cycle that makes the disc turn at all? You found this in
>    Step 3.)
> 3. Write your line as a rule: `duty = a + b × speed`. What are your
>    `a` and `b`?

### 8b: Feedforward alone

Now use **only** your recipe, with **no** feedback: when you hold **A**,
set the duty cycle to `a + b × target`.

1. Hold **A**. Does it reach the target? How close?
1. Do the **rag test** from Step 7, with the same four-panel graph.
   What happens to the speed this time? Does the output change at all?

> **Check yourself:** why can't feedforward alone fight back against
> the rag?

### 8c: Both together

Now combine them: `duty = (a + b × target) + k × error`.

Run the **same three tests** for each version, and fill in this table:

| Version | Time to target (Step 6 method) | Error when steady | Error with the rag on |
| --- | --- | --- | --- |
| Feedback only (Step 7) | | | |
| Feedforward only (8b) | | | |
| **Both** | | | |

> **Check yourself:** which version wins each column? Why do you think
> real robots use both?

**Checkpoint:** post your recipe graph, your table, and the rag-test
graph for **both together** to **`#rookie_training`**.

<details>
<summary>⭐ Stretch: does your recipe always work?</summary>

* Try **three different targets** (say 0.5, 1, and 2 rotations/s). Is
  your recipe still a good guess at each one?
* Do 8a again with a **half-charged battery**. Does the recipe change?
  Why? Our motor classes can also set a **voltage** instead of a duty
  cycle: why might a recipe in volts work better than one in duty
  cycle?
* Now look at the comments on the `Friction` class in `lib`. Which of
  its numbers is like your `a`?

</details>

## 🏆 The Discus Challenge

Now you know feedforward, feedback, current limits, and how to measure
energy. Can you **beat the record**?

### The course

Starting with the disc **stopped**, do this **5 times** in a row:

1. Spin up to the **target**: 1 rotation/s (within ±5%).
1. **Hold** it there for **1 second**.
1. Bring it back to **stopped** (under 0.05 rotations/s).

The run ends when the disc is stopped after the 5th lap.

### Three leaderboards

| Board | Goal | Rule |
| --- | --- | --- |
| ⚡ **Eco** | Use the **least energy** | Must finish within 30 seconds |
| 🏎️ **Speed** | Finish in the **least time** | Supply current never over 10 A |
| 🏆 **Overall** | Lowest **time × energy** | Both rules above |

### Rules

* **Your code keeps score.** It must time the run, add up the energy
  from the battery (`battery voltage × supply current × 0.02`, every
  cycle), check each lap really reached the target and really stopped,
  and print the result at the end.
* **Fair start:** a freshly charged battery (above 12.5 V), the disc
  stopped, and nobody touches it during the run.
* **Safety:** stay within your mentor's current limit and duty cycle
  cap. No exceptions for records.
* **Proof:** a mentor watches the run. Post your Glass graph and your
  printed score to **`#rookie_training`**.

<details>
<summary>💡 Ideas to try (open after your first run)</summary>

* Is it better to push hard and get there fast, or gently?
* When should you **stop** pushing? Does the disc need power to coast
  the last bit?
* What's the cheapest way to **stop**: coast, brake mode, or push
  backwards? Measure all three.
* Does feedforward help you hit the target without wasting energy on
  overshoot?
* Read the rules again: what **are** you allowed to change? Make a list,
  and try each one.

</details>

### Records

Beat a record? Post your proof, then update this table with a pull
request.

| Board | Record | Who | Date | Slack proof |
| --- | --- | --- | --- | --- |
| ⚡ Eco | — J | | | |
| 🏎️ Speed | — s | | | |
| 🏆 Overall | — J·s | | | |

## Send it in

Commit your study, push, and open a PR with your graphs and your
answers to the check-yourself questions.

Next: [Hard mode: the shooter bot](12_DEMO_BOT.md)
