# Dual-Robot-Box-Pushing
Dual-robot cooperative box-pushing system using Pololu 3pi+. Implements Asymmetric, Symmetric PID, and Phase-Based Sensor Fusion strategies. Uses bump and line sensors for real-time trajectory correction. Results show Strategy C (Sensor Fusion) achieves superior stability and accuracy over standard PID methods.

📖 Overview

Collaborative mobile robots offer greater adaptability than fixed automation. This project implements a system where two differential-drive robots push a rectangular box (280g) along a 900mm straight track.


The core objective is to evaluate how different control architectures affect stability, lateral drift, and angular deviation.


The Three Strategies Evaluated:

Strategy A (Asymmetric Control): Hybrid approach where one robot uses PID and the other uses Open-Loop fixed PWM. Acts as a baseline.

Strategy B (Symmetric PID): Fully symmetric dual closed-loop architecture. Both robots use PID for speed and heading control.

Strategy C (Sensor Fusion - Recommended): Optimised approach combining PID control with Line Sensor correction (lateral offset) and Bump Sensor alignment (phase synchronization).

⚙️ Hardware & Environment

Robots: 2x Pololu 3pi+ (32U4 OLED Edition recommended).

Sensors:

Line Sensors (IR): 5-channel array (A11, A0, A2, A3, A4) for trajectory tracking.

Bump Sensors: Left/Right collision sensors (D5, A6) for detecting robot-box alignment.

Object: Rectangular Box (440 x 150 x 30 mm, 280g).

Track: 900mm length with black boundary lines for visual feedback.

🚀 Key Algorithms

1. PID Speed & Heading Control

Used in Strategy B and C. The system runs a control loop every 10ms.

Speed Loop: Maintains target velocity (KP=0.2,KI=0.001).

Heading Loop: Synchronizes left/right wheel speeds (KP=0.8).

2. Sensor Fusion Logic (Strategy C)

This is the most advanced feature of the repository:

Lateral Correction: Uses the outer line sensors. If the robot drifts (triggers A11 or A4), a PWM correction (δ) is applied to steer back to the center.

Alignment (Phase) Correction: Uses bump sensors to detect if one robot is ahead or behind the other.

Front Press (Ahead): Reduce speed.

Rear Press (Behind): Increase speed.

📂 Project Structure

├── src
│   ├── main.cpp           # Main control loop and FSM
│   ├── Strategy_A.cpp     # Asymmetric Control Implementation
│   ├── Strategy_B.cpp     # Symmetric PID Implementation
│   ├── Strategy_C.cpp     # Sensor Fusion Implementation
│   ├── Sensors.cpp        # Bump and Line sensor calibration & reading
│   └── Motors.cpp         # PWM and PID controller logic
├── docs                   # Experimental data and diagrams
├── README.md
└── LICENSE

🛠️ How to Run

Dependencies: Ensure you have the Pololu 3pi+ Arduino libraries installed.

Calibration:

Run the calibration_routine first to set min/max values for line sensors.

Calibrate bump sensors to determine discharge time thresholds.

Select Strategy: Uncomment the desired strategy (MODE_A, MODE_B, or MODE_C) in main.cpp.

Deploy: Upload the code to both Pololu robots simultaneously.

🤝 Contributing

Contributions are welcome! Please submit a Pull Request or open an issue for any bugs found in the PID tuning or sensor fusion logic.

📄 License

MIT License
