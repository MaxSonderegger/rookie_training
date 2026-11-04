# AI assistants in rookie_training

This repo is Team 100's training material for new programmers. It holds
docs only; the robot code is in `Team100/all26`.

**AI use is encouraged here.** You may read, explain, and edit these
pages, draft new chapters, and help students or mentors work through
the material.

Guidelines:

* **Audience:** high-school students, many of whom have never
  programmed. Use plain language, one step at a time, and end each
  chapter with a concrete checkpoint. Define jargon the first time it
  appears.
* **Layout.** Chapters live in `docs/`, numbered in reading order:
  * `docs/part1/` (basics), `docs/part2/` (real robots: Discus, shooter
    bot), `docs/part3/` (localization and auton): all for the
    **RoboRIO**, `all26` branch `main`, 2026 WPILib.
  * `docs/part4/` (SystemCore), `docs/part5/` (Pis and vision),
    `docs/part6/` (object detection), `docs/part7/` (final challenge):
    the **SystemCore**, branch `2027`, 2027 WPILib.
  * Part 3 deliberately uses **wheels only** (odometry). Cameras and
    AprilTags come in Part 5, so don't make Parts 3 or 4 depend on them.
  * `docs/reference/`: debugging and troubleshooting.
  * Every chapter gets its own number (no `05a`-style suffixes);
    companion pages that aren't read in order (like `BREAKER_CARDS.md`)
    have no number. Renumbering means updating every link and the
    README.
  * `docs/advanced/`: experiments that need a mentor's sign-off first.
  * `docs/img/`: images.
  * Part 2 deliberately has **no robot code and no pointers to existing
    robot code**: students build their own studies from goals, hints,
    and checkpoints.

  The README lists every chapter. If you add, move, or renumber one,
  update the README and the "Next:" links around it.
* **Accuracy:** check claims against the right `Team100/all26` branch
  for that part, and the matching WPILib docs (`en/stable` for 2026,
  `en/2027` for 2027), before writing them. Link to the real file
  instead of copying code that may change. Mark anything unverified
  with `TODO`.
* **Links** into the robot code use full GitHub URLs
  (`https://github.com/Team100/all26/blob/main/...` in Parts 1 to 3,
  `.../blob/2027/...` in Parts 4 to 7), since this is a separate repo.
* **No secrets.** This repo may be public. Don't write radio WPA keys or
  other team passwords here; say "ask a mentor". Vendor-default
  passwords that are already public (SystemCore, Pi) are fine.
* **Images** go in `docs/img/`. Placeholders for photos still needed
  look like `> 📷 **TODO photo:** docs/img/name.jpg: what it shows`.
* **Teaching style:** chapters that teach coding use "check yourself"
  questions and escalating hints rather than handing over full
  solutions. Keep that pattern.
* When a student follows a chapter *inside* `all26`, that repo's own
  `AGENTS.md` applies there (AI acts as mentor only).
