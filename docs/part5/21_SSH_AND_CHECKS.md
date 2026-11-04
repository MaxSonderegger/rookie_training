# SSH into the Pis

Part 5 is about the robot's **cameras**. Each camera plugs into a
**Raspberry Pi**, a small Linux computer that runs our vision code. You
already log in to the SystemCore with `ssh`; the Pis work the same way.
This page shows you how to log in to a Pi and what to check.

Like Part 4, Part 5 uses the **SystemCore**, the **`2027`** branch, and
**2027 WPILib**.

`ssh` is built into Windows, Mac, and Linux. On Windows, open **Command
Prompt** or **PowerShell**.

## Who's who on the robot network

| Device | Address | ssh login | Password |
| --- | --- | --- | --- |
| Robot radio | `10.1.0.1` | — | — |
| SystemCore | `10.1.0.2` or `robot.local` | `systemcore` | `systemcore` |
| Driver Station laptop | `10.1.0.5` | — | — |
| Raspberry Pi cameras | `10.1.0.30` – `10.1.0.36` | `pi` | `raspberry` |

Each Pi's IP is written on its case. See [network.md](https://github.com/Team100/all26/blob/2027/lib/doc/network.md)
for the full scheme.

## Connect your laptop

* **To the whole robot:** plug into the access point's (or robot
  radio's) **DS** port, with Ethernet set to **Automatic (DHCP)**. You
  should get a `10.1.0.x` address. If you get `169.254…`, see
  [Connect to the robot](../part4/17_ROBOT_NETWORK.md#troubleshooting).
* **To a Pi on the bench, without a robot:** set your laptop to
  `10.1.0.2` so it pretends to be the SystemCore
  ([how](https://github.com/Team100/all26/blob/2027/lib/doc/network.md#impersonating-the-roborio)).

First check that you can reach it at all:

```
ping 10.1.0.2
ping 10.1.0.31
```

On a Mac, use `ping -c 4 10.1.0.2`: without `-c 4`, Mac `ping` never
stops by itself (press `Ctrl+C` to stop it). Windows stops after 4
tries on its own.

If `ping` fails, `ssh` will fail too. Fix the cable and IP first.

## SystemCore

You already know this one:
[SSH into the SystemCore](../part4/16_SYSTEMCORE.md#6-ssh-into-the-systemcore).
Its checks include whether it can see the cameras:
`ping -c 3 10.1.0.31` (each Pi) should get 3 replies.

## Raspberry Pi

```
ssh pi@10.1.0.31
```

The password is `raspberry`.

### Things to check

| Question | Command | Good answer |
| --- | --- | --- |
| Which Pi is this? | `grep Serial /proc/cpuinfo` | Last 16 characters match an entry in [identity.py](https://github.com/Team100/all26/blob/2027/raspberry_pi/app/config/identity.py) |
| What's its address? | `hostname -I` | The address written on the case |
| Can it see the SystemCore? | `ping -c 3 10.1.0.2` | 3 replies. Without this, no vision data reaches the robot |
| Is our app running? | `supervisorctl status` | `app  RUNNING` |
| What is the app saying? | `tail -n 50 app.stdout`<br>`tail -n 50 app.stderr` | No repeating errors in `stderr` |
| Is a camera attached? | see below | Your camera model is listed |
| Too hot or underpowered? | `vcgencmd measure_temp`<br>`vcgencmd get_throttled` | Under ~80'C; `throttled=0x0` |
| Is the disk full? | `df -h /` | `Use%` well under 90% |

You can also see the app status and logs in a browser at
`http://10.1.0.31:9001`, and the annotated camera view at
`http://10.1.0.31:1181`.

#### Is a camera attached?

Our cameras plug into the Pi with **USB**. First, check the Pi sees a
USB camera at all:

```
lsusb
```

Look for a line with the camera's maker (ours are **Arducam**). No line
means the Pi doesn't see the camera: check the USB cable at both ends,
try another USB port, or another cable.

Then check our camera library can use it. The camera can only be used
by one program at a time, so stop our app first, check, then start it
again:

```
supervisorctl stop app
rpicam-hello --list-cameras
supervisorctl start app
```

If `rpicam-hello` isn't installed, use Python instead:

```
python3 -c "from picamera2 import Picamera2; print(Picamera2.global_camera_info())"
```

An empty list (`[]`) or "No cameras available", while `lsusb` *does*
show the camera, means the camera is plugged in but something else is
wrong. Restart the app, or reboot the Pi (`sudo reboot`).

### Turning a Pi off

Don't just pull the power, because that can corrupt the SD card. Run:

```
sudo shutdown -h now
```

wait for the green LED to stop blinking, then unplug. (Or use the
`shutdown` task at `:9001`; see [use.md](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/use.md).)

## Common problems

* **`REMOTE HOST IDENTIFICATION HAS CHANGED`**: the device was reimaged,
  or a different Pi now has that IP. This is expected on our robots. Clear
  the old key and try again:
  ```
  ssh-keygen -R 10.1.0.31
  ```
* **`Connection timed out`**: wrong IP, wrong laptop network settings, or
  the device is off. Go back to `ping`.
* **`Connection refused`**: the device is still booting. Wait 30
  seconds.
* **`Permission denied`**: wrong user or password. It's `pi@` for the
  Pis, and `systemcore@` for the SystemCore.

## Rules

* **Look, don't change.** Don't edit files on the SystemCore or a Pi by
  hand. The next deploy overwrites them, and nobody else will know what
  you did. Change the code in your fork and deploy it
  ([Pi changes](22_PI_CHANGES.md)).
* **Don't `sudo apt upgrade` a robot at an event.** Updates can break
  things right before a match.
* Tell the drive team before you restart anything on a robot that someone
  else is using.

Next: [Change the Raspberry Pi code and deploy it](22_PI_CHANGES.md)
