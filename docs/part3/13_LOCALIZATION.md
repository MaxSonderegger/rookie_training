# Where am I? (localization)

Before a robot can drive itself to a spot, it has to know **where it
is**. Working that out is called **localization**.

In this chapter the robot uses only its **wheels**. You'll watch the
numbers change as the wheels turn, and then find out how wrong they can
get. You don't need to read any robot code: you'll learn it from
experiments and graphs. (Cameras come later, in
[Part 5](../part5/23_VISION.md).)

Do this in pairs, with the **demo bot** and a mentor.

## Pose: where and which way

A robot's **pose** is three numbers:

* **x** and **y**: where it is on the field, in meters
* **heading** (written **θ**, "theta"): which way it's facing

The [auton chapter](14_AUTON.md#pose-where-am-i) explains the field's
coordinates in detail; skim that section now.

## How the wheels say where you are (odometry)

If you know how far each wheel turned, you can work out how far the
robot moved. Add up all those little moves, 50 times a second, and you
know where you are. That's **odometry**, like counting your steps with
your eyes closed.

Each drive motor knows how many times it has turned (it has a sensor
inside called an **encoder**). The code turns that into meters in two
steps:

1. **Gears:** the motor turns many times for each turn of the wheel.
   That number is the **gear ratio**.
1. **Wheel size:** each turn of the wheel rolls it forward one
   **circumference**: π × the wheel's diameter.

The demo bot's numbers are near the top of its
[`Robot.java`](https://github.com/Team100/all26/blob/main/studies/demo_bot/src/main/java/org/team100/frc2026/Robot.java)
(you don't need to understand the rest of the file):

| Name in the code | Value | What it is |
| --- | --- | --- |
| `DRIVE_GEAR_RATIO` | about 27.4 | the motor turns 27.4 times per wheel turn (two 5.23 : 1 gearboxes, one after the other) |
| `DRIVE_WHEEL_DIAM` | 0.098 m | wheel diameter, about 10 cm |
| `TRACK_WIDTH` | 0.5 m | the distance between the left and right wheels |

> **Check yourself:** with these numbers, how many times does the
> **motor** turn while the robot drives 1 m? (Work out the wheel's
> circumference first.)

### Turning, without a gyro

The demo bot is a **tank drive**: a left side and a right side, like a
tank or a wheelchair.

* Both sides roll the **same** distance: the robot drives **straight**.
* One side rolls farther: the robot **turns** toward the shorter side.
* The sides roll **opposite** ways: the robot **spins in place**.

The demo bot has no gyro (a sensor that measures turning) in its code,
so it works out its heading from the **difference** between the two
sides, divided by the track width. Hold on to that idea: it matters in
Experiment 5.

> **Check yourself:** the left wheels roll 0.5 m forward, and the right
> wheels 0.5 m backward. Where does the robot end up? Which way is it
> facing?

## Get set up

1. **Deploy the team's demo bot code.** Open
   [`studies/demo_bot`](https://github.com/Team100/all26/tree/main/studies/demo_bot)
   from your fork, on `main`, in **2026** WPILib VS Code, and deploy it
   over USB, as in [hard mode](../part2/12_DEMO_BOT.md). It already has
   driving and odometry built in.
1. **Connect Glass** to the robot
   ([how](../part2/10_GLASS_POWER.md#open-glass-and-connect)).
1. **Open the field view:** **NetworkTables → field**. The little robot
   on it is where the demo bot **thinks** it is. It starts at (0, 0),
   the corner of the field, facing +x.
1. **Find the numbers.** In **NetworkTables View**:
   * `field` → `robot`: the pose, as **x (m)**, **y (m)**, **heading
     (degrees)**
   * `log` → `Tank Drive` → `left` → `LinearMechanism` → `position (m)`:
     how far the left wheels have rolled since the code started. Same
     for `right`.

   No `LinearMechanism`? Set the
   [log level](../part2/10_GLASS_POWER.md#find-your-motors-values) to
   `DEBUG`.
1. **Make a graph** ([how](../part2/10_GLASS_POWER.md#make-a-graph)) of
   the left and right `position (m)`.
1. **Learn the controls.** Drive with the **right stick** only: up and
   down to go forward and back, left and right to turn. The top speed is
   1 m/s, slow on purpose.

**Back to zero:** the pose starts at (0, 0) every time the robot code
starts. To zero it, click **Restart Robot Code** in the Driver Station
(on the **Diagnostics** tab), or redeploy.

> [!CAUTION]
> Experiments 1 to 3 are **on blocks**, with the wheels off the floor.
> For the floor experiments, clear the area, say **"Enabling!"**, and
> keep a finger on **Enter** (disable).

> **TODO (mentors):** the floor experiments need the laptop to follow
> the robot. A long USB cable works; or join the radio's 2.4 GHz Wi-Fi
> ([how](../part4/17_ROBOT_NETWORK.md#or-connect-over-wi-fi-24-ghz-backup)),
> if the demo bot has a radio. Say which one we use, and which floor
> (carpet or smooth).

For every experiment: **predict first**, write it down, *then* try it.

## Experiment 1: drive without moving

Robot **on blocks**. Push the stick forward gently for two seconds, then
let go.

**Predict:** what will the two lines on your graph do? What will the
little robot on the field view do?

<details>
<summary>What you should see</summary>

Both lines climb together, by the same amount. On the field view, the
robot **drives forward**, even though the real robot didn't move an
inch. Odometry only knows that the wheels **turned**. It has no idea
whether they were touching the floor.
</details>

## Experiment 2: spin without moving

Still on blocks. Push the stick **sideways** for two seconds.

**Predict:** the graph? The field view?

<details>
<summary>What you should see</summary>

The lines go **opposite** ways: one up, one down. The field-view robot
**spins in place**, and its heading changes. Now turn a little while
driving forward. What do the lines do?
</details>

## Experiment 3: the kidnapped robot

**Disable** the robot. Note its pose in Glass. Now two people lift it
and carry it 2 m across the room, turn it around, and put it down.

**Predict:** what does Glass say now?

<details>
<summary>What you should see</summary>

Nothing changed. The wheels didn't turn, so as far as odometry knows,
the robot never moved. Robots call this the **kidnapped robot problem**.
In a match it happens a little bit every time another robot shoves
yours.
</details>

> **Check yourself:** list three things that move the robot without
> turning its wheels the right amount. (Hint: think about ice, bumper
> cars, and a ramp.)

## Experiment 4: one meter, for real

Now on the floor. Lay a tape measure along the floor, and put a strip of
tape across it at 0 as the start line.

1. Line up the **front** of the robot with the start line, and
   **restart robot code** so the pose is (0, 0).
1. Drive **slowly**, straight forward, until Glass says **x = 1.00**.
   Stop.
1. Measure how far the front of the robot really moved.
1. Do it **3 times**.

| Run | Robot thinks (m) | Tape measure (m) | Error (cm) | Error (%) |
| --- | --- | --- | --- | --- |
| 1 | 1.00 | | | |
| 2 | 1.00 | | | |
| 3 | 1.00 | | | |

**Error (%)** = error ÷ 100 cm × 100.

> **Check yourself:**
> 1. Is it off the **same way** every time (always short, or always
>    long)? That's a **bias**, and it usually has one cause.
> 2. The wheel diameter in the code is 0.098 m. If the real wheels were
>    a bit **bigger** (new tires, say), would the robot go **too far** or
>    **not far enough**?

<details>
<summary>⭐ With a mentor: break the wheel size</summary>

In `Robot.java`, make `DRIVE_WHEEL_DIAM` 10% bigger (`0.108`), deploy,
and do Experiment 4 again. Predict first: how far does the robot really
go when Glass says 1.00 m? Put the number back afterwards.

This is a bias you made yourself. Real ones come from worn tires, or
someone measuring the wheel wrong.
</details>

## Experiment 5: the spin test

Put a strip of tape on the floor along the robot's side, so you can see
exactly which way it faces.

1. **Restart robot code**, so the heading is 0.
1. Spin the robot in place, slowly, **4 full turns**, watching the real
   robot (not Glass). Stop when it lines up with the tape again.
1. Read the heading in Glass. It should say 0. Whatever it says is the
   error, after 4 turns.

Glass counts from −180° to 180°, then jumps around. So 10° too much
after 4 turns reads **10**, and 10° too little reads **−10**.

> **Check yourself:**
> 1. Divide your error by 4. That's the error per turn. Does more turns
>    mean more error?
> 2. Heading comes from the **difference** between the wheels, divided
>    by the **track width**. If the track width in the code is **too
>    small**, does the robot think it turned **more** or **less** than it
>    really did?
> 3. Find this line in `Robot.java`: `// measured 4/30/26, used to be
>    0.4.` Somebody did your test! With 0.4 in the code, what would the
>    spin test have shown?

> **Why tank drives are bad at this:** when a tank drive spins, its
> wheels have to skid sideways a little. Skidding means the wheels turn
> a bit more than the robot does, so wheel-only heading is rarely
> perfect. That's one reason the competition robot has a **gyro**.

## Experiment 6: drift

Put tape at a start spot, along two edges of the robot. Restart robot
code, and drive a **square**, about 2 m on each side, back to the tape.
Write down what Glass says (x, y, heading) and measure where the robot
really is.

Now, **without** restarting, drive the square **three more times**,
writing down the error after each lap.

| Lap | Glass x, y (m) | Glass heading (°) | Really off by (cm) |
| --- | --- | --- | --- |
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |

> **Check yourself:** does the error **grow** each lap? Why does a
> small heading error early on make the **position** error worse and
> worse? (Imagine walking in a straight line with your eyes closed, but
> pointed 5° wrong.)

That's **drift**: every small mistake adds up, and odometry has no way
to notice. It can only be fixed with information from **outside** the
robot.

## At home: the same experiments in the simulator

The demo bot code runs in the [simulator](../part1/09_SIMULATOR.md) too.
Try Experiments 4 and 6 there, driving with the keyboard.

> **Check yourself:** the simulator gets them almost perfect. Why? What
> does a real floor have that the simulator doesn't?

## What about cameras?

Odometry is **fast** and **smooth**, but it **drifts**, and it never
knows when it's been pushed. In [Part 5](../part5/23_VISION.md) you'll
add **cameras** that look for **AprilTags**, markers at known places on
the field. Seeing one tells the robot where it really is.

Cameras have their own problems: they're **jumpy** (every picture is a
little different), **late** (about a tenth of a second), and only work
when a tag is in view. So our robots use **both**: odometry every cycle,
and cameras to **nudge** the pose back toward the truth now and then.

If you're curious, the team's notes are in the
[localization README](https://github.com/Team100/all26/blob/main/lib/src/main/java/org/team100/lib/localization/README.md).
You don't need them yet.

> **Check yourself:**
> 1. In your own words: why does the robot need to know where it is
>    before it can drive itself somewhere?
> 2. Why not just use cameras, and skip odometry?
> 3. Which of your experiments would a camera have fixed? Which wouldn't
>    it?

**Checkpoint:** show a mentor:

* your tables from Experiments 4, 5, and 6, filled in, with one
  sentence each on what caused the error
* the field view moving while the robot is on blocks, and explain why
  that's not a bug

Next: [Work on auton](14_AUTON.md)
