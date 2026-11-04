# Use the simulator

WPILib includes a simulator that runs your robot code **on your laptop**,
with no robot. It can't test everything, but it saves a lot of time:
you can try auton paths, check your logic, and find crashes before you
touch hardware.

The official guide is
[here](https://docs.wpilib.org/en/stable/docs/software/wpilib-tools/robot-simulation/simulation-gui.html).

## Start the simulator

1. Open the `comp` folder (or its workspace) in VS Code.
1. `Ctrl+Shift+P` → **WPILib: Simulate Robot Code**.
1. Pick **Sim GUI** only, and nothing else.

   > 🖥️ **Advanced:** in a terminal, run `./gradlew simulateJava` in the `comp` folder. It opens the Sim GUI by itself ([how](01_SETUP.md#advanced-run-commands-from-a-terminal)).

You'll get the simulation window:

![Simulation GUI](../img/sim_gui.png)

If it doesn't start, read the terminal for errors, and ask a mentor if
it's stuck.

## The field view

Open **NetworkTables → Field** if you don't see the field.

![Field View](../img/field_view.png)

If there's no Field option, your robot code probably crashed or hit a
breakpoint. Check the terminal. More about the field widget is
[here](https://docs.wpilib.org/en/stable/docs/software/dashboards/glass/field2d-widget.html).

## Drive with the keyboard

1. Drag **Keyboard 0** onto **Joystick[0]**.

   ![Drag Keyboard](../img/drag_keyboard.png)

1. Select **Teleoperated** in the Robot State widget.

   <img src="../img/teleoperated.png">

1. `W A S D` move the robot (axes 0 and 1). `E R` rotate it (axis 2).

> **Check yourself:** which way does the robot move when you press `W`?
> Is that +x on the field? (See [Auton](../part3/14_AUTON.md#pose-where-am-i).)

## Run an auton

1. Choose an auton in **NetworkTables → SmartDashboard → Auton Command**.
   While disabled, its path is drawn on the field.
1. Click **Autonomous** in Robot State.
1. Watch the robot drive the trajectory.

   ![](../img/ugly_traj.png)

1. To tidy up the drawing, open the field's menu (top right) →
   **Trajectory** and turn off the arrows.

   ![](../img/nice_traj.png)

For more detail in the logs, set **SmartDashboard → Log Level** to
`TRACE`.

![](../img/log_level.png)

## 🎉 You finished Part 1!

You've set up your laptop, wired and spun a real motor, written code to
control it, and tested in the simulator. That's a real milestone.

**Post a photo of your group succeeding to `#rookie_training` in Slack!**

Next: [Part 2: Glass and power-draw tests](../part2/10_GLASS_POWER.md)
