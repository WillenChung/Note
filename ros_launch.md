## ROS
### 1. launch file
roslaunch car car.launch
roslaunch 包名 launch文件名
```xml
<launch>
	<!-- 注释 -->

	<!-- 命令行roslaunch car car.launch serial_port:=”/dev/ttyUSB0” -->
	<!-- default可以覆盖，value不能覆盖 -->
	<arg name=”serial_port” default=”/dev/ttyUSB0”/>
	<arg name=”serial_baudrate” value=”115200”/>

	<!-- 需要从命令行输入参数 -->
	<!-- export TURTLEBOT3_MODEL=burger -->
	<arg name=”model” default=”$(env TURTLEBOT3_MODEL)” doc=”model type [burger, waffle, waffle_pi]”/>

	<!-- 包含其他launch文件 -->
	<include file=”$(find turtlebot3_navigation)/launch/amcl/launch”>

	<!-- pkg:包名 type:可执行文件名 name:实际节点名 output:屏幕显示 -->
	<!-- NODES: /test (car/talker.py) -->
	<!-- 分组 节点、私有参数前加上域名 -->
	<group ns=”group”>
		<node pkg=”car” type=”talker.py” name=”test” output=”screen”/>
	</group>

	<node pkg=”serial_communication” type=”serial_commu” name=”serial_commu” output=”screen”>
		<!-- 话题名重映射 from:节点定义的话题名 to:实际发送信息的话题名 -->
		<remap from=”serial_commu/vel” to=”vel”/>

		<!-- 节点的私有参数 -->
		<!-- PARAMETERS: /serial_commu/car/serial/port -->
		<param name=”car/serial/port” value=”$(arg serial_port)”/>
		<param name=”car/serial/baudrate” value=”$(arg serial_baudrate)”/>
		<!-- 导入.yaml文件 ns要加一层域名 -->
    		<rosparam file=”$(find turtlebot3_navigation)/param/costmap_common_params_$(arg model).yaml” command=”load” ns=”global_costmap”/>
  	</node>	
</launch>
```

serial_communication_node.cpp
将car/serial/port参数值赋给car_port变量，没有参数时使用默认变量
`nh.param<std::string>(“car/serial/port”, car_port, “/dev/ttyUSB0”);`
### 2. Custom message
常用类型：float32 float64 float64[]
string Header int8 int16 uint32


