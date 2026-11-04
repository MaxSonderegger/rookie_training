# Breaker cards

> [!CAUTION]
> **Finders: stop reading.** These are the answers. Go to the
> [finder's guide](FINDER_GUIDE.md) instead.

You're the **breaker**. Your job is to cause one realistic failure,
make sure it's really broken, and then put it back **exactly** the way
it was. Every card is something that really happens to robots at
competitions. Read the whole card before you start.

Rules (see [the game](25_BREAK_THE_CAMERAS.md#lab-1-the-breaker-vs-finder-game)):

* **Only the cards a mentor approved.** Nothing else.
* **Check it's really broken** using the card's "What the finder should
  see". If it isn't, the round isn't fair.
* **Always undo**, then check everything is healthy with your finder.
* Write down anything you had to change, so you can put it back.

Each card has:

* **Do it:** the exact steps.
* **Why:** what this looks like at a real competition, and what it
  teaches.
* **What the finder should see:** so you can check it worked.
* **Undo:** how to put it back.

---

## Card 1 ⭐: Ethernet unplugged

**Do it:** unplug the Ethernet cable from the **Pi** (not the switch).
Leave it hanging next to the port, like it fell out.

**Why:** robots get hit, and vibration shakes connectors loose. A loose
Ethernet cable is one of the most common failures in FRC. It teaches the
finder to check the **network** before blaming the code.

**What the finder should see:** the Pi doesn't answer `ping`; its web
pages (`:9001`, `:1181`) don't load; the robot stops getting vision data.
The Pi itself is still powered on and running.

**Undo:** plug it back in until it clicks. Wait 30 seconds, then check
`ping` works again.

## Card 2 ⭐: USB camera unplugged

**Do it:** unplug the camera's USB cable **from the Pi**.

**Why:** camera cables get snagged and pulled during matches. It teaches
the finder that "the Pi is fine" and "the camera is fine" are two
different questions.

**What the finder should see:** the Pi answers `ping` and `:9001` loads,
but the camera view `:1181` shows no picture, `lsusb` has no Arducam
line, and `app.stderr` complains about the camera.

**Undo:** plug the camera back into the **same** port. Check `lsusb`
shows it again. If the app doesn't recover by itself within a minute,
restart it (`supervisorctl restart app`), and write down that you had to.

## Card 3 ⭐: The app is stopped

**Do it:** `ssh` into the Pi and run `supervisorctl stop app`.

**Why:** at competitions, programs crash: a bad deploy, a bug that only
shows up on the field, running out of memory. It teaches the finder to
check whether our program is even **running**.

**What the finder should see:** the Pi answers `ping`, `:9001` loads
and shows `app` **STOPPED**, `:1181` doesn't load, and vision data stops.

**Undo:** `supervisorctl start app`. Check `:9001` says **RUNNING**.

## Card 4 ⭐: Lens covered

**Do it:** put a small piece of **paper tape** over the camera lens.
(Paper tape, not clear tape or anything sticky that leaves residue.)

**Why:** lenses get blocked by game pieces, other robots, a piece of
tape someone forgot, or a fingerprint. Everything "works", but the
camera sees nothing. It teaches the finder to **look at the picture**,
not just at whether the program runs.

**What the finder should see:** everything is running and connected,
`lsusb` sees the camera, but the `:1181` view is dark, and no tags are
detected.

**Undo:** peel the tape off gently. Check `:1181` shows a clear picture.

## Card 5 ⭐⭐: Smudged lens

**Do it:** with a mentor, put a light **fingerprint** on the lens.

**Why:** a smudge is sneakier than tape: the picture looks almost OK,
but tags far away or at an angle stop being detected. It teaches the
finder that "it detects a tag up close" doesn't mean it's healthy.

**What the finder should see:** a slightly blurry or hazy `:1181`
picture; close tags still work, but far tags (2 m+) fail.

**Undo:** clean the lens with a lens cloth (a mentor has one). Check far
tags are detected again.

## Card 6 ⭐⭐: Wrong USB port

**Do it:** unplug the camera and plug it into a **different** USB port
on the Pi (for example, from a blue USB 3 port to a black USB 2 one).

**Why:** someone sees a loose camera in the pits, plugs it into the
nearest port, and walks away. Sometimes it works, sometimes it doesn't.
It teaches the finder to compare the robot to how it's **supposed** to
be wired, not just "is it plugged in".

**What the finder should see:** it depends on our code and camera. The
camera may work normally, run at a lower frame rate, or not be found.
Write down which one happens: that's the real lesson.

**Undo:** plug it back into the original port. Restart the app if
needed, and check `:1181` again.

> **TODO (mentors):** confirm which USB port our cameras should use, and
> what happens in the wrong one.

## Card 7 ⭐⭐: Two devices, one address

**Do it:** on **your** laptop, set your Ethernet to a static IP address
that is the **same** as one of the Pis (for example `10.1.0.31`, mask
`255.255.255.0`), with your laptop plugged into the robot's network.
(See [Connect to the robot](../part4/17_ROBOT_NETWORK.md) for how to set
an IP.)

**Why:** two devices with the same IP address confuse the whole network:
messages go to the wrong one, randomly. It happens when a laptop is left
on a static IP from an earlier test. It teaches the finder that network
problems can come and go, and how to check who's really answering.

**What the finder should see:** the Pi works **sometimes**: `ping`
answers come and go, `:9001` loads or doesn't, vision flickers.

**Undo:** set your laptop back to **Automatic (DHCP)**. Check `ping` to
the Pi is steady again.

## Card 8 ⭐⭐: Weak power

**Do it:** with a mentor, shut the Pi down properly
(`sudo shutdown -h now`, wait for the green LED to stop), then power it
from a **weak phone charger** instead of its proper supply.

**Why:** when the robot drives hard, the battery voltage dips, and a
Pi's power can sag too. An underpowered Pi slows itself down
("throttles"), or reboots in the middle of a match. It teaches the
finder to check **power**, not just cables and code.

**What the finder should see:** the Pi works, but slower;
`vcgencmd get_throttled` is **not** `0x0`; maybe a warning in the
system log about undervoltage.

**Undo:** shut down properly again, put the proper power supply back,
power on. Check `vcgencmd get_throttled` says `0x0`.

## Card 9 ⭐⭐⭐: A broken deploy

**Do it:** with a mentor, on a branch in **your fork**, make one small
change in the Pi code that makes it crash at startup (for example, a
typo in a name). Deploy it to the Pi
([how](22_PI_CHANGES.md#4-deploy-to-a-pi)).

**Why:** "it worked on my laptop" is the most famous last words in
programming. A bad deploy right before a match is a classic disaster,
and so is someone's half-finished test branch left on the robot. It
teaches the finder to read **logs** and to ask "what changed?".

**What the finder should see:** `:9001` shows `app` restarting over and
over (or **FATAL**), and `app.stderr` shows a Python error with your
typo in it.

**Undo:** redeploy the team's code from `2027`. Check `:9001` says
**RUNNING** and stays that way for a minute. Delete your test branch.

## Card 10 ⭐⭐⭐: Robot side gone

**Do it:** leave the Pi completely alone. Instead, turn off the robot's
**radio**, or unplug the Ethernet cable between the switch and the
**SystemCore**.

**Why:** the Pi can be perfect and the robot still gets no vision,
because the problem is on the **other** end. It teaches the finder to
check the whole path, not just the Pi.

**What the finder should see:** the Pi is healthy, and the camera view
works from a laptop on the same switch, but the Pi can't `ping` the
SystemCore (`10.1.0.2`), and the robot gets no vision data.

**Undo:** turn the radio back on (wait 90 seconds for it to link), or
plug the cable back in. Check the Pi can `ping 10.1.0.2` again.

## Card 11 ⭐⭐⭐: No calibration

**Do it:** with a mentor, on a branch in **your fork**, remove the
camera calibration for one Pi (its entry in the Pi code's calibration
list), and deploy that branch to the Pi. Everything else stays the
same.

**Why:** this is the sneakiest failure there is. Nothing crashes, the
camera works, tags get detected, and the picture looks perfect. But
every **distance and angle** is a little wrong, so the robot misses
shots and thinks it's somewhere it isn't. It happens for real when a
camera is replaced, swapped to another Pi, or its Pi is reset, and
nobody recalibrates. It teaches the finder that "running" isn't the same
as "**right**", and to check measurements against something trusted.

**What the finder should see:** every check passes, until they measure:
a tag at a tape-measured distance reports the wrong distance, worse near
the edges of the picture. The Pi's normal output (`app.stdout`, not
`stderr`) shows a big **CAMERA INTRINSIC IS NOT CALIBRATED** banner at
startup.

**Undo:** redeploy the team's code from `2027`. Check the banner is
gone, and a tag at a known distance reads correctly. Delete your test
branch.

## Card 12 ⭐⭐⭐: Swapped cameras

**Do it:** with a mentor, shut down two Pis properly, swap their USB
cameras, and power them back on.

**Why:** each camera's calibration belongs to the Pi it's plugged into.
Swap the cameras, and both Pis use the **other** camera's numbers,
without any warning. Easy to do by accident when fixing a robot in a
hurry. It teaches the finder that labels and records matter.

**What the finder should see:** everything works, no warnings at all,
but measured distances are slightly off on both cameras. The camera
**labels** don't match the Pis they're written down for.

**Undo:** shut both down properly, swap the cameras back, power on, and
check a tag at a known distance reads correctly.

---

**Ideas for new cards?** If something breaks for real at a competition,
write a card for it, and send a pull request. That's how this list gets
better every season.
