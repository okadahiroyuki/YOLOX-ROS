# YOLOX-ROSをDockerで動かす
YOROX-ROS humble

## YOLOX-ROSのDockerイメージの作成
ワークスペースに移動して、YOLOX-ROSのリポジトリをクローンする。
```
cd ~/ros2_ws_src
git clone --recursive https://github.com/okadahiroyuki/YOLOX-ROS -b humble
cd YOLOX-ROS/yolox_ros_cpp/docker/onnxruntime/
```
Docker イメージのビルドとコンテナ起動
```
docker compose build
docker conpose up -d
```
Dockerコンテナに入って、YOLOX-ROSのセットアップ
```
xhost +
export DISPLAY=:0
docker exec -it -e DISPLAY=$DISPLAY yolox_onnxruntime bash
cd ~/ros2_ws
./src/YOLOX-ROS/weights/onnx/download.bash all
source /opt/ros/humble/setup.bash

python3 -m pip install --upgrade setuptools packaging

colcon build --cmake-args -DYOLOX_USE_ONNXRUNTIME=ON
```

サンプルの実行
```
ros2 launch yolox_ros_cpp yolox_onnxruntime.launch.py
```

## RealSenseを使う
docker コンテナ内でRealSenseにアクセスできるように docker-compose.ymlに以下を追加
```
privileged: true
```

コンテナ内にlibrealsense とrealsense_ros のインストール
```
sudo apt update
sudo apt install ros-humble-librealsense2 ros-humble-realsense2-camera
```
realsense-rosとYOLOX-ROSの実行　
```
ros2 launch realsense2_camera rs_launch.py
ros2 launch yolox_ros_cpp yolox_onnxruntime.launch.py src_image_topic_name:="/camera/color/image_raw"
```

## model を変更する場合　（デフォルトはyolox_tiny）
```
ros2 launch yolox_ros_cpp yolox_onnxruntime.launch.py src_image_topic_name:="/camera/color/image_raw" model_path:='./src/YOLOX-ROS/weights/onnx/yolox_m.onnx'
```


