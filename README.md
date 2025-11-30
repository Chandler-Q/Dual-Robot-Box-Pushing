# Dual-Robot-Box-Pushing
This repository contains the firmware for a dual-robot system designed to cooperatively push a box. The code implements a Phase-Based Sensor Fusion Strategy (referred to as "Strategy C" in the associated research), combining PID speed control, heading correction, and real-time feedback from both collision (bump) and line sensors.

Key Feature: The system operates in distinct time domains—utilizing bump sensors for initial alignment and line sensors for trajectory maintenance—and features a post-run data replay system for performance analysis.

📖 Overview

The robots are programmed to push a 280g box along a straight 900mm track. To ensure stability and synchronization without explicit communication between robots, the system uses a local sensor fusion approach:

Speed Synchronization: PID control on wheel encoders to maintain target velocity.

Phase-Based Logic:

0s - 6s (Bump Phase): Uses collision sensors to detect alignment errors (one robot leading/lagging) and adjust speed.

6s - 11s (Line Avoidance Phase): Uses IR line sensors to correct lateral drift.

>11s (Stop Phase): Detects the finish line and stops.

Data Logging: Records alignment state transitions in RAM and replays them via Serial after the run for analysis.

⚙️ Hardware & Environment

Robots: 2x Pololu 3pi+ (32U4 OLED Edition recommended).

Sensors:

Line Sensors (5-Channel): For lateral correction and stop detection.

Bump Sensors (DIY): Uses capacitive discharge timing on pins A6 and D5 to detect physical contact with the box.

Object: Rectangular Box (440 x 150 x 30 mm, 280g).

Track: 900mm length with black boundary lines for visual feedback.

🚀 Software Architecture

The loop() function executes a strict 10ms control cycle with the following logic:

1. Control Phases (Time-Sequenced)

The robot behavior changes based on elapsed time (millis()):

Bump Phase (Start - 6s):

The IR Emitter is OFF to save power/interference (optional).

Reads Bump Sensors via readBumpTime().

Logic: If Left Bumper is pressed (BL=0) but Right is not (BR=1), the robot assumes it is ahead on the left side and decelerates the left wheel / accelerates the right wheel to realign with the box.

Line Avoidance Phase (6s - Finish):

IR Emitter is turned ON.

Reads Line Sensors (LS1, LS2, LS4, LS5).

Logic:

Outer sensors (LS1/LS5) trigger small PWM corrections (LINE_ADJ_low).

Inner sensors (LS2/LS4) trigger large PWM corrections (LINE_ADJ_high).

Stop Phase (>11s):

Monitors Center Sensor (LS3).

Stops all motors if a black line is detected.

2. PID Control

Three PID controllers (PID.h) run continuously to maintain motion quality:

pid_vL & pid_vR: Maintain wheel target speeds (KP=0.2).

pid_heading: Keeps the robot moving straight by minimizing encoder difference (KP=0.8).

3. Data Logging & Replay

The system includes a circular buffer bumpBuf to record stability events:

During the run, it logs whenever the bump state changes (e.g., from "Aligned" to "Left Ahead").

Post-Run Analysis: Once the robot stops, it enters a "Replay Mode". It prints the Total Running Time and a list of all 01 (Left Ahead) and 10 (Right Ahead) transitions to the Serial Monitor. This allows researchers to quantify stability without external tracking systems.

📂 Project Structure

├── Dual_Robot_Box_Pushing.ino  # Main control loop, phases, and logging logic
├── Encoders.h                  # Interrupt Service Routines for wheel encoders
├── Motors.h                    # Motor driver class
├── PID.h                       # PID control class
├── Kinematics.h                # Odometry calculation
├── oled.h / lcd.h              # Display status (Bump State / Time)
└── README.md

🛠️ How to Run
Dependencies: Ensure you have the Pololu3piPlus32U4 libraries installed.

Upload: Flash the Dual_Robot_Box_Pushing.ino to both robots.

Start:

Place robots behind the box.

Power on. The OLED will show "Bump+Line Ctrl".

The robot starts immediately (or upon reset).

During Run: Watch the OLED for real-time Bump Status (BL: 0/1, BR: 0/1).

After Stop: Connect USB and open Serial Monitor (115200 baud) to view the performance report and stability logs.

🤝 Contributing

Contributions are welcome! Please submit a Pull Request or open an issue for any bugs found in the PID tuning or sensor fusion logic.

📄 License

MIT License
