
TF / VISP Hand-Eye Calibration
=================================

## Use Cases

This package uses the VISP
hand-eye calibration from `visp_hand2eye_calibration` to provide a simple
camera pose estimation package. Input is expected as transformations published in TF.

If you're unfamiliar with Tsai's hand-eye calibration [1], it can be used in two ways:

- **eye-in-hand** -- To compute the static transform from a robot's
  end-effector to the optical frame of a camera. In this case, the camera is
  mounted on the end-effector, and you place the visual target so that it is
  fixed relative to the base of the rboot.
- **eye-on-base** -- To compute the static transform from a robot's base to the
  optical frame of a camera. In this case, the camera is mounted to the base of
  the robot (or kinematic chain), and you place the visual target so that it is
  fixed relative to the end-effector of the robot.

## Usage

For both use cases, you can either launch the `handeye_calibration.launch`
launchfile, or you can include it in another launchfile as shown below. Either
way, the launchfile will bring up the `visp_hand2eye_calibration` solver, along with an integration script. By
default, the integration script will interactively ask you to accept or discard
each sample.

### eye-in-hand

```xml
<launch>
  <include file="$(find handeye_calibration)/launch/handeye_calibration.launch">
    <arg name="marker_parent_frame" value="/base_link"/>
    <arg name="camera_parent_frame" value="/ee_link"/>

    <arg name="camera" value="/camera/rgb"/>
    <arg name="camera_frame" value="/camera_rgb_optical_frame"/>
  </include>
</launch>
```

### eye-on-base

```xml
<launch>
  <include file="$(find handeye_calibration)/launch/handeye_calibration.launch">
    <arg name="marker_parent_frame" value="/ee_link"/>
    <arg name="camera_parent_frame" value="/base_link"/>

    <arg name="camera" value="/camera/rgb"/>
    <arg name="camera_frame" value="/camera_rgb_optical_frame"/>
  </include>
</launch>
```

### Expected input

The Tsai-Lenz method computes the transformation between the end effector of the robot and the optical frame of the camera,
given a set of corresponding transformations between the robot base and the robot base effector, and between the camera
and a tracked target.
This package expects these transformations to be published in real time in TF, in order to save them simultaneously as pairs.
The names of the TF reference frames can be passed as input; the defaults conform to the ROS Industrial standard [2].

## Tips for accuracy

The following tips are given in [1], paragraph 1.3.2.

- Maximize rotation between poses.
- Minimize the distance from the target to the camera of the tracking system.
- Minimize the translation between poses.
- Use redundant poses.
- Calibrate the camera intrinsics if necessary / applicable.
- Calibrate the robot if necessary / applicable.

## References

[1] *Tsai, Roger Y., and Reimar K. Lenz. "A new technique for fully autonomous
and efficient 3D robotics hand/eye calibration." Robotics and Automation, IEEE
Transactions on 5.3 (1989): 345-358.*

[2] ROS Industrial: <http://wiki.ros.org/Industrial>