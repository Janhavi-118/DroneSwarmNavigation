# Autonomous Exploration of Unknown Indoor Environments Using Multi-UAV System : The Drone Simulation 

> A coordinated drone swarm system for autonomous indoor exploration and mapping  
> Published at [ICCCR 2025](https://ieeexplore.ieee.org/abstract/document/11072559)

---

## Overview

This project implements an advanced **multi-UAV system** for autonomous exploration and mapping of unknown indoor GPS-denied environments. The system uses a **master-slave architecture** where a central master drone coordinates several slave drones to achieve efficient area coverage while maintaining battery optimization and safety constraints.

### Key Innovations
- **Master-Slave Coordination** – Hierarchical control architecture for synchronized swarm behavior
- **Real-time Indoor Mapping** – LiDAR-based SLAM without GPS dependency  
- **Battery-Aware Navigation** – Dynamic battery management with charging station integration
- **Autonomous Door Detection** – CNN-based door recognition for multi-room exploration
- **Multi-Floor Support** – Scalable system for exploring multi-story buildings
- **Simulation-to-Reality** – Seamless transition from Gazebo simulation to real-world deployment

---

## System Architecture

### Master-Slave Formation
```
        [Master Drone]
               |
        +------+------+
        |      |      |
    [Slave] [Slave] [Slave] [Slave]
```

The master drone stays in the center, maintaining 4 slave drones in a plus (+) formation for optimal exploration coverage and communication.

### Core Components
- **ROS2 Middleware** – Multi-agent communication and coordination
- **PX4 Autopilot** – Flight control and stabilization
- **Gazebo Simulator** – Realistic 3D environment simulation
- **LiDAR SLAM** – Simultaneous localization and mapping
- **CNN-based Perception** – Door and obstacle detection

---

## Performance Metrics

The system was tested across multiple environments with increasing complexity:

| Environment Complexity | Map Accuracy | Exploration Time | Distance Traveled |
|---|---|---|---|
| **Simple Hallway** | 94.2% | 2m 15s | 47.3m |
| **Multi-Room Office** | 91.8% | 4m 42s | 89.7m |
| **Complex Warehouse** | 88.5% | 6m 38s | 124.5m |

**Comparative Analysis:**
- **Frontier SLAM** – Efficient frontier-based exploration
- **Exploratory RRT** – Rapidly exploring random trees
- **Potential Field Mapping** – Physics-based obstacle avoidance

---

## Requirements

- **PX4** – Flight control autopilot
- **ROS2** – Multi-agent communication framework (Humble recommended)
- **Micro XRCE-DDS Agent & Client** – ROS2-PX4 bridge
- **QGroundControl** – Ground control station for monitoring

---

## Installation and Setup

### Step 1: Install PX4 and ROS2

Go to the [official PX4 ROS2 User Guide](https://docs.px4.io/main/en/ros/ros2_comm.html) and follow the installation steps. We recommend using **ROS2 Humble**.

### Step 2: Install QGroundControl

Visit the [QGroundControl Guide](http://qgroundcontrol.com/) and install QGroundControl according to the provided instructions.

### Step 3: Prepare Gazebo Simulation Environment

Open a terminal and navigate to the PX4 Gazebo tools directory:

```bash
cd ~/PX4-Autopilot/Tools/simulation/gz
```

Make the simulation-gazebo file executable:

```bash
chmod +x simulation-gazebo
```

This file will be used to launch the Gazebo world.

---

## Execution

### Terminal 1: Launch QGroundControl

Open a terminal and run QGroundControl as an executable:

```bash
./QGroundControl.AppImage
```

### Terminal 2: Launch Gazebo Simulation

Open another terminal and run the simulation-gazebo file:

```bash
cd ~/PX4-Autopilot/Tools/simulation/gz
python3 simulation-gazebo
```

**To run with a custom world file:**

```bash
python3 simulation-gazebo --world <world_name>
```

### Terminal 3: Spawn Master Drone

Navigate to the PX4-Autopilot directory and spawn the first (master) drone:

```bash
cd ~/PX4-Autopilot
PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 1
```

### Terminal 4+: Spawn Slave Drones

For each additional (slave) drone, open a new terminal and run:

```bash
cd ~/PX4-Autopilot
PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_POSE="0,1" PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 2
```

**Important:** Modify the pose according to your requirements. The format is `"x_position,y_position"`. As you spawn additional drones, increment the `-i` argument (i.e., `-i 3`, `-i 4`, etc.) for each new drone.

**Example for spawning 5 drones with different positions:**

```bash
# Terminal 3: Master drone (center)
PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 1

# Terminal 4: Slave 1 (front)
PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_POSE="0,1" PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 2

# Terminal 5: Slave 2 (back)
PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_POSE="0,-1" PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 3

# Terminal 6: Slave 3 (left)
PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_POSE="-1,0" PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 4

# Terminal 7: Slave 4 (right)
PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_POSE="1,0" PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 5
```

### Final Terminal: Start Micro XRCE-DDS Agent

In a new terminal, run the Micro XRCE-DDS agent for ROS2-PX4 communication:

```bash
MicroXRCEAgent udp4 -p 8888
```

---

## Repository Structure

```
DroneSwarmNavigation/
├── src/                          # Source code for swarm algorithms
├── gazebo/                       # Gazebo world and model files
│   ├── worlds/                   # Custom environment definitions
│   └── models/                   # Drone and sensor models
├── launch/                       # ROS2 launch files
├── config/                       # Configuration files
├── results/                      # Generated maps and trajectories
├── build/                        # PX4 build artifacts
└── README.md                     # This file
```

---

## Results & Validation

### Mapping Accuracy
The system achieved greater than 88% accuracy across all test environments by fusing LiDAR data from multiple drones using a calibrated point cloud registration algorithm.

### Energy Efficiency
Multi-drone coordination reduced **exploration time by 40-60%** compared to single-drone approaches while maintaining battery levels above critical thresholds.

### Real-World Applicability
- GPS-denied navigation validation
- 3D point cloud fusion verified
- Scalability tested up to 10 drones
- Robustness across complex environments

---

## Research Applications

This system excels in time-sensitive scenarios requiring precise spatial data:

- **Disaster Response** – Search and rescue operations in collapsed structures
- **Architectural Planning** – Building surveys and volumetric analysis
- **Warehouse Management** – Inventory verification and facility inspection
- **Heritage Preservation** – Detailed documentation of historical sites
- **Environmental Monitoring** – Indoor air quality and structural assessment

---

## Key Findings

From our extensive simulations:

| Finding | Impact |
|---|---|
| Multi-drone systems **outperform single-drone approaches** by 40-60% | Significantly accelerates exploration missions |
| **Master-slave architecture scales** to 10+ drones | Enables large-scale industrial applications |
| **LiDAR SLAM plus Visual odometry** provides robust positioning without GPS | Enables operation in GPS-denied environments |
| **Battery-aware routing** extends mission duration by 25-30% | Optimizes energy consumption |
| **CNN-based door detection** achieves greater than 92% accuracy | Enables autonomous multi-floor exploration |

---

## Troubleshooting

### Problem: Drones not connecting to ROS2

**Solution:** Ensure the Micro XRCE-DDS Agent is running on port 8888:
```bash
MicroXRCEAgent udp4 -p 8888
```

### Problem: Gazebo simulator not launching

**Solution:** Ensure the simulation-gazebo file has executable permissions:
```bash
chmod +x ~/PX4-Autopilot/Tools/simulation/gz/simulation-gazebo
```

### Problem: PX4 instance fails to spawn

**Solution:** Verify that the build exists by checking:
```bash
ls ~/PX4-Autopilot/build/px4_sitl_default/bin/px4
```

If the build is missing, rebuild PX4:
```bash
cd ~/PX4-Autopilot
make px4_sitl
```

### Problem: Drones not appearing in QGroundControl

**Solution:** Check that all three components are running:
1. QGroundControl is open
2. Gazebo simulation is running
3. Micro XRCE-DDS Agent is active on port 8888

---

## Publications & References

### Primary Publication
```bibtex
@inproceedings{shrote2025autonomous,
  title={Autonomous Exploration of Unknown Indoor Environments Using Multi-UAV System},
  author={Shrote, Sumukh and Sriraman, Harini and Bagaddeo, Aaditya and Chaurasia, Janhavi},
  booktitle={2025 International Conference on Cognition and Cognizable Computing (ICCCR)},
  year={2025},
  organization={IEEE}
}
```

### Recommended Reading
- Hess, W., et al. (2016). "Real-time Loop Closure in 2D LIDAR SLAM" – Loop closure implementation
- Yasin, J. N., et al. (2021). "Energy-efficient Navigation of an Autonomous Swarm" – Battery management
- Topiwala, A., et al. (2018). "Frontier Based Exploration for Autonomous Robot" – Exploration algorithms
- Karam, S., et al. (2022). "Microdrone-based Indoor Mapping with Graph SLAM" – SLAM techniques

---

## Authors

- **Sumukh Shrote** – Algorithm design & coordination protocols
- **Harini Sriraman** – Lead advisor & system architecture  
- **Aaditya Bagaddeo** – LiDAR SLAM implementation
- **Janhavi Chaurasia** – Gazebo simulation & visualization

*School of Computer Science and Engineering, Vellore Institute of Technology Chennai Campus*

---

## Contributing

We welcome contributions from the research community! To contribute:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/AmazingFeature`)
3. **Commit** your changes (`git commit -m 'Add AmazingFeature'`)
4. **Push** to the branch (`git push origin feature/AmazingFeature`)
5. **Open** a Pull Request with detailed description

For major changes, please open an Issue first to discuss proposed modifications.
