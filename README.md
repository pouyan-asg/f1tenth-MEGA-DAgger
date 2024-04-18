# f1tenth-MEGA-DAgger
 This is the repository of MEGA-DAgger on F1TENTH autonomous racing platform.

**mega_dagger_agent** is a ROS2 foxy package. 
Move the package in the /src folder of your workspace.
Under the workspace directory, source the overlay and underlay, and colcon build it. 
Then, enter the following commandlines for different usage.

**KUNPENG'S PKG** is...

## 1 f1tenth_gym_ros Simulation

Run following commands on your PC. 
```bash
# modify the path, lidarscan_topic, etc. for your specific usage
# change as "num_agent: 2" in "f1tenth_gym_ros/config/sim.yaml"
# in the 1st terminal
ros2 launch f1tenth_gym_ros gym_bridge_launch.py  # run the simulator
# in the 2nd terminal
conda activate f110_mega-dagger  # enable your specific env to run pytorch, etc.
ros2 run mega_dagger_agent agent_node.py  # self.is_real = False, modify self.lidarscan_topic
# in the 3rd terminal
ros2 run mega_dagger_agent fake_scan_node.py  # run this if you want the modified lidar scan
# in the 4th terminal
ros2 run mega_dagger_agent oppo_node.py  # is_real == False, to enable the opponent car in sim
```

## 2 Real World Implementation

1 - Run following commands on your PC. 
```bash
# connect the same network with the same ROS_DOMAIN_ID as your agent car. 
# modify the path, lidarscan_topic, etc. for your specific usage
# in the 1st terminal
conda activate f110_mega-dagger  # enable your specific env to run pytorch, etc.
ros2 run mega_dagger_agent agent_node.py  # self.is_real = True
# in the 2nd terminal
ros2 run mega_dagger_agent fake_scan_node.py  # run this if you want the modified lidar scan
# in the 3rd terminal
rviz2  # for visualization
```

2 - Run following commands on your agent car.
```bash
# connect the same network with the same ROS_DOMAIN_ID as your PC. 
# in the 1st terminal
ros2 launch f1tenth_system bringup_launch.py  # enable remote control
# in the 2nd terminal
ros2 launch particle_filter localize_launch.py  # enable localization
# in the 3rd terminal
rviz2  # for visualization
```
Enable remote desktop tools like NoMachine if you need. 

3 - Run following commands on your opponent car.
```bash
# Build the packages for running pure pursuit on the opponent car
cd f1tenth_ws/
colcon build

source install/setup.bash # source the workspace
ros2 launch f1tenth_stack bringup.py # enable the controller
ros2 launch particle_filter localize.py # enable the particle filter to do localization
ros2 run pure_pursuit pure_pursuit.py # run pure pursuit to follow the waypoints

# Build the packages for integration services and ROS2 topic conversion on the opponent car
cd ../is-workspace/
colcon build
source install/setup.bash # source the installation

integration-service src/Integration-Service/examples/basic/ros2__domain_id_change.yaml # run the integration service to connect different ros2 topics under different ros2 domain ID
```
