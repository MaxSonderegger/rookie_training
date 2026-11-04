# Spot the balls (object detection)

AprilTags are easy for a camera: black and white squares, printed on
purpose so a computer can find them. A **yellow ball** on a messy floor
is much harder. It's round from every side, it can be in shadow or in
bright light, half hidden behind a robot, or far away and tiny. And there
are lots of other yellow things in the world.

To find balls, we use a **neural network**: a program that learned what a
ball looks like from lots of example pictures, instead of from rules
someone wrote. Ours is called **YOLO** ("You Only Look Once"). In this
part you'll see how well it works, find where it fails, and **retrain**
it to do better.

> 🚧 **This part is still being built.** It starts with retraining the
> model. Sending the balls to the robot code comes later, in
> [Part 7](../part7/29_FETCH_AND_SHOOT.md).

## Why it matters

A robot that can see game pieces can pick them up **by itself**: in
auton, or to help the driver line up in a match. But a detector that
misses balls wastes time, and one that "sees" balls that aren't there
sends the robot chasing a yellow shirt.

## The words you need

| Word | What it means |
| --- | --- |
| **Model** | The trained neural network: a file full of numbers. Ours is `ball-detection` |
| **Detection** | One thing the model found: a **box** around it, and a **confidence** from 0 to 1 |
| **Confidence threshold** | Boxes less sure than this are thrown away. Ours is **0.5** |
| **Label** | A box *you* drew on a picture, to tell the model "this is a ball" |
| **Training** | Showing the model your labeled pictures, over and over, until it gets them right |
| **Epoch** | One pass through all your training pictures |
| **Validation set** | Labeled pictures the model **doesn't** train on, used to check it honestly |

## Step 1: See what it sees

The code is in `all26` on the `2027` branch:

* [`raspberry_pi/app/analysis/balls.py`](https://github.com/Team100/all26/blob/2027/raspberry_pi/app/analysis/balls.py)
  runs the model on every camera frame and draws a **green box** around
  each ball.
* It only runs on the Pis whose
  [identity](https://github.com/Team100/all26/blob/2027/raspberry_pi/app/interpreter/interpreter_factory.py)
  is set up for it (look for `Balls(cam, network)`).

1. Find a Pi that runs `Balls` (ask a mentor which one).
1. Open its camera view in a browser: `http://<pi address>:1181`
   ([how](../part5/23_VISION.md#exercise-see-what-the-pi-sees)).
1. Put a yellow ball in front of the camera. You should see a green box.

> **Check yourself:** read `balls.py`. Find:
> 1. Where it loads the model. Where on the Pi does the model file live?
> 2. The confidence threshold. What would happen if you made it 0.1?
>    0.9?
> 3. What it does with each box. Does it send anything to the robot yet?

**Checkpoint:** you see a green box around a ball, live.

## Step 2: Find where it fails

Before you fix a model, you need to know **how** it's wrong. Be a
breaker, like in [Break the cameras](../part5/25_BREAK_THE_CAMERAS.md).

Try each of these, and write down what happens:

| Test | Found it? | Wrong boxes? |
| --- | --- | --- |
| 1 ball, 1 m away, good light | | |
| 1 ball, as far away as it still works (measure it) | | |
| 5 balls, close together | | |
| Ball half hidden behind something | | |
| Ball in shadow; ball in bright light | | |
| Ball moving (roll it) | | |
| **Decoys:** a yellow shirt, a lemon, a tennis ball, yellow paper | | |
| No balls at all | | |

Post your table to **`#rookie_training`**.

> **Check yourself:** there are two ways to be wrong.
> * A **miss**: a ball is there, but no box.
> * A **false alarm**: a box, but no ball.
>
> Which one does our model do more? Which one is worse for a robot that
> drives to balls by itself?

## Step 3: Collect and label pictures

A model is only as good as the pictures it learned from. To fix the
failures you found, you need **more pictures of exactly those
situations**: far-away balls, shadows, decoys with **no** label (so the
model learns they're *not* balls).

1. Take pictures **with our camera**, on the robot, in the places it will
   really be used.
1. **Label** them: draw a box around every ball in every picture.
   Every ball, even the hard ones. A ball you skip teaches the model that
   it isn't a ball.

> **TODO (mentors):** where the existing labeled pictures live (they're
> not in `all26`), how to capture pictures from our camera, and which
> labeling tool we use. Labels need to be in YOLO format: one `.txt`
> file per picture, one line per box.

**Checkpoint:** at least **50 new labeled pictures**, mostly of the
cases that failed in Step 2.

## Step 4: Retrain

The training script is
[`raspberry_pi/training/train.py`](https://github.com/Team100/all26/blob/2027/raspberry_pi/training/train.py).
It's short. Read it first.

> **Check yourself:**
> 1. `autosplit` splits your pictures 80/20. Which 80% is for, and which
>    20%? Why not train on all of them?
> 2. What does `epochs=25` mean?
> 3. `YOLO("yolo26n.pt")` is the starting point. Why start from a model
>    that someone else already trained, instead of from nothing?

Run it on a laptop, from the `raspberry_pi` folder, with the
`ultralytics` Python package installed. When it finishes, it prints
where it saved the result (something like
`runs/detect/train/weights/best.pt`) and a score called **mAP**: higher
is better.

> **TODO (mentors):** check the exact steps on a team laptop (Python
> version, `pip install ultralytics`, where the `labeling/` folder goes)
> and how long 25 epochs take without a GPU.

<details>
<summary>Hint: training takes forever</summary>

Try fewer epochs first (like 5) just to check everything works. Then
run the full 25 while you do something else.
</details>

## Step 5: Put it on the Pi

The Pi runs a faster version of the model, in a format called **NCNN**.
Convert yours:

```
yolo export model=runs/detect/train/weights/best.pt format=ncnn imgsz=512
```

That makes a folder ending in `_ncnn_model`. `balls.py` loads it from
`/home/pi/models/ball-detection_ncnn_model`.

> **TODO (mentors):** how we copy a new model to the Pi (the normal
> camera-code deploy doesn't copy `models/`), and how to keep the old
> one so you can switch back.

**Checkpoint:** after restarting the app, the Pi draws boxes with
**your** model.

## 🏆 The Ball Spotter Challenge

**Before vs. after:** does your model beat the old one?

1. A mentor sets up a scene: some yellow balls (a secret number, 0 to
   8), and some decoys, at different distances and in different light.
1. Count from the camera view at `:1181`:
   * **+1** for every ball with a box
   * **−1** for every box with no ball (false alarm)
   * **−1** for every ball with no box (miss)
1. Do the **same scene** with the old model and with yours.
1. Repeat for **3 scenes**, and add them up.

**Score = your total − the old model's total.** Above zero means you
made it better.

Post both totals and a screenshot of each scene to **`#rookie_training`**.

### Records

| Score (yours − old) | Who | Date | Slack proof |
| --- | --- | --- | --- |
| — | | | |

If your model wins, it should become the team's model: open a pull
request with the new model files, and the Step 2 table showing why it's
better.

<details>
<summary>⭐ Stretch: what's the right threshold?</summary>

Try the threshold at 0.3, 0.5, and 0.7 on the same scene. Make a graph:
**threshold** across the bottom, **misses** and **false alarms** up the
side (two lines). Where do they cross? Is that the best threshold for a
robot that drives to balls?
</details>

## Coming next

> **TODO (mentors):** sending each ball's position from the Pi to the
> robot (the `Target` messages; `balls.py` doesn't send them yet), and
> reading them in Java with
> [`Targets`](https://github.com/Team100/all26/blob/2027/lib/src/main/java/org/team100/lib/targeting/Targets.java).

Next: [Part 7: Fetch and shoot](../part7/29_FETCH_AND_SHOOT.md)
