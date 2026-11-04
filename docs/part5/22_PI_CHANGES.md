# Change the Raspberry Pi code and deploy it

The Pi cameras run our own Python app from
[`raspberry_pi/`](https://github.com/Team100/all26/tree/2027/raspberry_pi). This page takes you from an idea to
your change running on a Pi and merged into the team code.

The short version:

```
sync fork → branch → edit → test on laptop → deploy to Pi → check → commit → push → PR
```

## One-time setup

1. Do [Setup](../part1/01_SETUP.md) so you have your fork cloned.
1. Set up Python and the VS Code extensions:
   [raspberry_pi/doc/setup.md](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/setup.md).
   This creates a virtual environment in `raspberry_pi/env`.
1. Read the [code walkthrough](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/walkthrough.md) so you
   know where things are.

Open the **`raspberry_pi`** folder in VS Code (not the whole repo), so the
Python and Gradle extensions find everything.

## 1. Sync and branch

As in the [fork workflow](../part1/02_FORK_WORKFLOW.md):

```
git checkout 2027
git pull upstream 2027
git checkout -b alice/pi-exposure
```

## 2. Make your change

Edit files under `raspberry_pi/app/`. Keep changes small; one idea per PR.

If you're adding a **new Pi**, add its serial number (from
`grep Serial /proc/cpuinfo` on the Pi; see [SSH and checks](21_SSH_AND_CHECKS.md))
to [identity.py](https://github.com/Team100/all26/blob/2027/raspberry_pi/app/config/identity.py), **and** keep
the Java side,
[Camera.java](https://github.com/Team100/all26/blob/2027/lib/src/main/java/org/team100/lib/camera/Camera.java),
in sync.

## 3. Test on your laptop first

Everything except the real camera runs on your laptop, using a "fake"
camera that reads images from the `images` folder. From the
`raspberry_pi` folder, with the venv active:

```
python runtests.py
python runapp.py
```

* All tests should pass. Add a test for your change if you can.
* While `runapp.py` is running, open http://localhost:1181 and check
  that the test image is processed the way you expect. Press Ctrl-C to
  stop it.

See [run_locally.md](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/run_locally.md) for running
tests from the VS Code test pane.

## 4. Deploy to a Pi

1. Connect your laptop to the Pi (or the robot) and check that you can
   `ping` it. See [SSH and checks](21_SSH_AND_CHECKS.md#connect-your-laptop).
1. In VS Code's **Gradle** pane, run
   `raspberry_pi → Tasks → deploy → Deploy Camera Code`.

   > 🖥️ **Advanced:** in a terminal, run `./gradlew deploy` in the `raspberry_pi` folder ([how](../part1/01_SETUP.md#advanced-run-commands-from-a-terminal)).
1. Read the output. Each Pi it reached prints a green `DEPLOYED`.
   "skipping camera3x" just means that Pi isn't connected, which is fine.

What the deploy does: it zips `app/`, copies the zip and `runapp.py` to
`/home/pi` on each Pi, and restarts the app with `supervisorctl`. Details
are in [deploy.md](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/deploy.md).

> **Heads up:** deploy sends your code to **every camera it can reach**
> (the list is in [build.gradle](https://github.com/Team100/all26/blob/2027/raspberry_pi/build.gradle)). If you're
> plugged into a competition robot, all its cameras now run your
> unmerged code. Tell the mentor and drive team, and redeploy from the
> team's `2027` branch when you're done.
>
> If your Pi's IP isn't in the `remotes` list in `build.gradle`, add it.

## 5. Check it on the Pi

* `http://<pi-ip>:9001`: the supervisor page should show `app` **RUNNING**
  (green). Click `stderr` to see errors.
* `http://<pi-ip>:1181`: the live annotated camera view.
* In Glass (as the "server", with your laptop at `10.1.0.2`), check that the
  `vision` values are updating. See [use.md](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/use.md).
* Or `ssh pi@<pi-ip>` and run the checks in
  [SSH and checks](21_SSH_AND_CHECKS.md#raspberry-pi).

If the app keeps crashing, the error is in `app.stderr`. Fix the code on
your laptop and deploy again. **Don't edit the files on the Pi**; they're
inside a zip, and the next deploy replaces them anyway.

## 6. Commit, push, and open a PR

```
git add raspberry_pi/app/...
git commit -m "Shorten exposure so tags are sharp while driving"
git push -u origin alice/pi-exposure
```

Pushing a change under `raspberry_pi/` runs the **Raspberry Pi Tests**
workflow on your fork (if you
[enabled Actions](../part1/01_SETUP.md#turn-on-github-actions-for-your-fork)). Check
that the **Actions** tab is green, then open a PR against
**`Team100/all26`, branch `2027`**. In the description, say which Pi you
tested on and what you saw at `:1181`.

After it's merged, sync your fork and redeploy from `2027` so the robot
runs the team's code.

## Troubleshooting

| Symptom | Likely cause |
| --- | --- |
| Deploy says `skipping` for your Pi | Laptop can't reach it: check `ping`, cable, and laptop IP |
| `app FATAL can't find command '/home/pi/env/bin/python'` | The Pi was never fully set up; see [pi_setup.md](https://github.com/Team100/all26/blob/2027/raspberry_pi/doc/pi_setup.md) |
| `:1181` is blank but `app` is running | No camera found; see [Is a camera attached?](21_SSH_AND_CHECKS.md#is-a-camera-attached) |
| Works on laptop, crashes on Pi | A library is missing on the Pi, or the code needs the real camera. Read `app.stderr` |
| Robot sees no vision data | Pi can't reach `10.1.0.2`, or its serial isn't in `identity.py`/`Camera.java` |

Next: [How our vision works](23_VISION.md)
