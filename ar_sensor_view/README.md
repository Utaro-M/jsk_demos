# AR Sensor View

## Description

実画像にセンサーデータを重畳するために、簡易的にカメラ位置のキャリブレーションを行う。
ロボットと外部カメラの相対位置をキャリブレーションするために、ロボットに予め設定された点（TF）をカメラ画像上でクリックする。

![センサ画像サンプル](https://github.com/jsk-ros-pkg/jsk_demos/blob/master/ar_sensor_view/docs/jaxon_ar_real.png)

## Settings

- キャリブレーションされたカメラ (camera_infoメッセージが出ている)
- ロボット上に配置されたTFのリスト (LARM_LINK5等のロボットの姿勢に依存して変化するTF)

## Usage

- cameraノードのlaunch ( /for_ar_camera/image_rect_color, /for_ar_camera/camera_info 等のトピックが出ている )
- ar_sensor_view.launchのlaunch
- 指定したTFの順に、ロボットのTFに相当する部分をカメラ画像上で左クリックする
- (sample.launchでは下画像の click_target_0 ~ click_target_5 の順)
- 最後のTFをクリックした後に右クリックする（メニューが出るが、キャンセルすれば良い）

![TF位置サンプル](https://github.com/jsk-ros-pkg/jsk_demos/blob/master/ar_sensor_view/docs/jaxon_ar_v.png)

## ar_sensor_view.launch
### arguments
- カメラのトピックとして、camera/camera_info, camera/image_rect を使う
- CAMERA_NAME (default: camera)
- IMAGE_TYPE (default: image_rect)
- 使用するframe
- ORIGIN_FRAME (default: map) / camera_infoのframe_idとORIGIN_FRAME間のtfを推定し出力する
- ROBOT_FRAME (default: BODY) / robotに固定されていて、TARGET_LISTとの間のtransformationを取得できるframe
- USE_TARGET_LIST (default: true)
- TARGET_LIST (default: [] ) / クリックする対象となるframeのリスト
- 推定するカメラの初期位置(ROBOT_FRAMEに対する位置)
- DEFAULT_x (default: 3.0)
- DEFAULT_y (default: -1.2)
- DEFAULT_z (default: 1.0)
- DEFAULT_roll (default: -1.570796)
- DEFAULT_pitch (default: 0)
- DEFAULT_yaw (default: 1.570796)

## Memo(2022/10/16)
### required packages
```
git clone -b rotate_image https://github.com/Utaro-M/video_stream_opencv.git
git clone -b ar_sensor_view_matsuura https://github.com/Utaro-M/jsk_demos.git ## this repository, please use ar_sensor_view_matsuura branch
```

### commands
```
rosparam set /use_sim_time true ##for simulation time
roslaunch ar_sensor_view set_robot_description.launch ## set jaxonred
rosbag play [bag file name].bag -l --clock
roslaunch ar_sensor_view camera.launch video_stream_provider:=/home/utaro/Documents/hand_video/VID20221015175257.mp4 camera_info_url:="file:///$(rospack find ar_sensor_view)/config/camera_info_oppo_reno5.yaml" visualize:=false loop_videofile:=true rotate_90_clockwise:=true width:=1080 height:=1920
roslaunch ar_sensor_view sample.launch IMAGE_TYPE:="image_raw" CAMERA_NAME:="camera" ORIGIN_FRAME:="rs_t265_odom"
rosrun rviz rviz -d $(rospack find ar_sensor_view)/jaxon_ar_sample.rviz
```
