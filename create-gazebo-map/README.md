## References
**Building a world:** _*http://gazebosim.org/tutorials?tut=build_world*_ 

## How to run
Copy `small_room` and `volen` into your workspace and `catkin_make`

Run `$ roslaunch small_room room.launch` view the resulting work that will be demonstrated in this tutorial

Run '$ roslaunch volen volen.launch' to view the map of volen ground floor using the same method in this tutorial.

## Tutorial
### 1. Open a Gazebo simulation:
Click on `Edit` --> `Building Editor` and you should see the following page. Note there are three areas:
- **Platte:** You can choose models that you wish to add into the map here. 
- **2D View:** The only place you make changes to the map. 
- **3D View:** View only. 

![p1](/images/p1.png)

#### 2. Import a floor plan
You may create a scene from scratch, or use an existing image as a template to trace over. On the Platte, click on `import` and selet a 2D map plan image in the shown prompt and click on `next`.
![p2](/images/p2.png)

To make sure the walls you trace over the image come up in the correct scale, you must set the image's resolution in pixels per meter _(px/m)_. To do so, click/release on one end of the wall. As you move the mouse, an orange line will appear as shown below. Click/release at the end of the wall to complete the line. Once you successfully set the resolution, click on `Ok` and the 2D map plan image you selected should show up in the 2D-View area.
![p3](/images/p3.png)

### 3. Add & Edit walls
- Select **Wall** from **Platte**. 
- On the **2D View**, click/release anywhere to start the wall. As you move the mouse, the wall's length is displayed. 
- **Click again** to end the current wall and start an adjacent wall. 
- **Double-click** to finish a wall without starting a new one. 
- **Double-clicking on an existing wall** allows you to modify it. 
You can manipulate other models likewise. For more detailed instructions, please refer to _*http://gazebosim.org/tutorials?tut=build_world*_ for more details
![p4](/images/p4.png)

### 4. Prepare a package
You need to create a package for your Gazebo world so that you can use `roslaunch` to launch your it later.

- Go to your catkin workspace
`$ cd ~/catkin_ws/src`
- Create a package using the following command.
`$ catkin_create_pkg ${your_package_name}`
- Go to your package and create three folders **launch**, **worlds** and **models**.
```
$ cd ${your_package_name}
$ mkdir launch}
$ mkdir worlds
$ mkdir models
```

### 5.Save your map
Once you finish editing the map, give a name to your model on the top on the **Platte** and click on `File` -> `Save As` to save the model you just created into `../${your_package_name}/models`. 

![p5](/images/p5.png)

Click on `File` -> `Exit Building Editor` to exit. **Please note that once you exit the editor, you are no longer able to make changes to the model.** Click on `File` -> `Save World As` into `../${your_package_name}/worlds`. 

I will refer to this world file as `${your_world_file_name}.world` from now on.

### 6.Create a launch file for your gazebo map
Go to `../${your_package_name}/launch` and make a new file `${your_launch_file}`
Copy and paste the following code into your launch file and substitute `${your_package_name}` and `{your_world_file_name}` with their actual names.
 ```xml
 <launch>
	<arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>
	<arg name="x_pos" default="0.0"/>
	<arg name="y_pos" default="0.0"/>
	<arg name="z_pos" default="0.0"/>
	<include file="$(find gazebo_ros)/launch/empty_world.launch">
		<arg name="world_name" value="$(find ${your_package_name})/worlds/${your_world_file_name}.world"/>

		<arg name="paused" value="false"/>
	    <arg name="use_sim_time" value="true"/>
	    <arg name="gui" value="true"/>
	    <arg name="headless" value="false"/>
	    <arg name="debug" value="false"/>
	</include>

 	<param name="robot_description" command="$(find xacro)/xacro.py $(find turtlebot3_description)/urdf/turtlebot3_$(arg model).urdf.xacro" />

 	<node name="spawn_urdf" pkg="gazebo_ros" type="spawn_model" args="-urdf -model turtlebot3_$(arg model) -x $(arg x_pos) -y $(arg y_pos) -z $(arg z_pos) -param robot_description" />
</launch>
 ```

### 7. Test
Go to the workspace where your new package was created 
**e.g.** `cd ~/catkin_ws`

run `catkin_make` and then `roslaunch ${your_package_name} ${your_launch_file}`

You should see the Gazebo map you just created along with a turtlebot loaded.
