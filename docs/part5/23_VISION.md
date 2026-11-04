# How our vision works

You've logged into the Pis and changed their code. Now let's see what
that code actually **does**: how a camera on the robot turns a picture
into "the robot is *here* on the field".

## The big picture

```
camera ──frames──▶ Raspberry Pi ──"I see tag 7, there"──▶ SystemCore
                   (finds AprilTags)    (over the network)   (works out
                                                              where the
                                                              robot is)
```

1. A **camera** on each Pi takes pictures many times a second.
1. The **Pi** looks at each picture and finds **AprilTags**: the
   black-and-white square markers placed at known spots around the
   field. Each tag has a number.
1. For each tag it sees, the Pi works out **where the tag is compared to
   the camera**: how far, which direction, and how it's turned.
1. The Pi sends that over the network to the robot.
1. The robot code knows where every tag is on the field, and where each
   camera is on the robot, so it can work out **where the robot is**.
   Then it uses that to fix the drift you measured in
   [Where am I?](../part3/13_LOCALIZATION.md). You'll put it to work in
   [Park on the X, from anywhere](27_PARK_FROM_ANYWHERE.md).

We wrote our own vision code instead of using a ready-made product, so
that you can understand it and change it. The team's
[Raspberry Pi docs](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/README.md)
explain why.

## AprilTags: a barcode with a direction

An AprilTag is like a QR code, but simpler, and designed so a computer
can find it fast, even small, far away, or at an angle. Because the Pi
knows the tag's real size, it can tell how far away it is: the smaller
it looks, the farther it is. Because it's square, the Pi can tell how
it's tilted.

> **Try it:** print a tag (ask a mentor for the right family and size),
> and look at it with your phone camera from far away and at an angle.
> How small can it get before it's just a gray blob?

## Two tricky things

* **Lenses bend pictures.** Straight lines near the edge of a camera
  image look curved. The Pi has to un-bend (**undistort**) the image
  math before it can measure accurately. See
  [distortion](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/distortion.md).
* **Pictures are always a little old.** By the time the Pi has taken a
  picture, found the tags, and sent the answer, the robot has moved on,
  by about a tenth of a second. So the Pi and the robot keep their
  **clocks in sync**, and every answer says *when* the picture was
  taken. See the
  [sync protocol](https://github.com/Team100/all26/blob/2027/lib/src/main/java/org/team100/lib/network/SYNC.md).

> **Check yourself:** a robot driving at 3 m/s gets a vision answer
> that's 0.1 s old. How far has the robot moved since the picture was
> taken? Is that a lot, if you're trying to line up with a target?

## Exercise: see what the Pi sees

1. Follow [Change the Pi code](22_PI_CHANGES.md#3-test-on-your-laptop-first)
   to run the vision code on your **laptop** with the fake camera.
1. Read the
   [code walkthrough](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/walkthrough.md).
   Find where a frame comes in, where tags are found, and where the
   result is sent.
1. On a real Pi, hold a tag in front of the camera, and look at the
   Pi's dashboard and at the values that reach the robot in
   [Glass](../part2/10_GLASS_POWER.md).

**Checkpoint:** you can point to the three places in the code (frame in,
tags found, result out), and show a tag being detected live.

> **TODO (mentors):** add the tag family and print size we use, where
> to find the Pi dashboard, and which NetworkTables values to look at in
> Glass.

Next: [Set up a new camera](24_NEW_CAMERA.md)
