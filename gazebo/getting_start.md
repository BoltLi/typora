[toc]

<center><font size=7>Gazebo_01_getting_start</font></center>



# Gazebo_begining

1. gazebo在两个文件处

> /home/ljx/.gazebo/models
>
> /usr/share/gazebo-7/models

2. gzclient和gzserver

> The `gzserver` executable runs the physics update-loop and sensor data generation. This is the core of Gazebo, and can be used independently of a graphical interface. You may see the phrase "run headless" thrown about. This phrase equates to running only the `gzserver`. An example use case would involve running `gzserver` on a cloud computer where a user interface is not needed.
>
> 主要说：Gzserver 可执行程序运行物理更新循环和传感器数据生成，可以在不需要用户界面的云计算机上运行gzserver。
>
> The `gzclient` executable runs a [QT](http://qt-project.org/) based user interface. This application provides a nice visualization of simulation, and convenient controls over various simulation properties.
>
> 主要用于可视化以及仿真，提供了用户接口

## World Files

> The world description file contains all the elements in a simulation, including robots, lights, sensors, and static objects. 
>
> 涵盖所有仿真元素的描述文件
>
> using [SDF (Simulation Description Format)](http://gazebosim.org/sdf.html), and typically has a `.world` extension.
>
> These worlds are installed in `/share/gazebo-/worlds`，另外获取：[source code](https://bitbucket.org/osrf/gazebo/src/default/worlds/).

## Model Files

> A model file uses the same [SDF](http://gazebosim.org/sdf.html) format as world files, but should only contain a single ` ... `. The purpose of these files is to facilitate model reuse, and simplify world files. Once a model file is created, it can be included in a world file using the following SDF syntax:
>
> (模型重用需要，一样使用SDF，但是如下格式)
>
> ```xml
> <model>…..</model>
> 
> ```
>
> - World file如果要包含model files，使用
>
> ```xml
> <include>
> 	<uri>model://model_file_name</uri>
> </include>
> ```
>

## gazebo环境变量

> `GAZEBO_MODEL_PATH`: colon-separated set of directories where Gazebo will search for models
>
> `GAZEBO_RESOURCE_PATH`: colon-separated set of directories where Gazebo will search for other resources such as world and media files.
>
> `GAZEBO_MASTER_URI`: URI of the Gazebo master. This specifies the IP and port where the server will be started and tells the clients where to connect to.
>
> `GAZEBO_PLUGIN_PATH`: colon-separated set of directories where Gazebo will search for the plugin shared libraries at runtime.
>
> `GAZEBO_MODEL_DATABASE_URI`: URI of the online model database where Gazebo will download models from.
>
> ```
> source <install_path>/share/gazebo/setup.sh
> ```
>
> If you want to modify Gazebo's behavior, e.g., by extending the path it searches for models, you should first source the shell script listed above, then modify the variables that it sets.
>
> 对应我的安装路径就是
>
> source /usr/share/gazebo-7/setup.sh
>
> 环境变量是电脑设置例如bashrc上进行设置的时候用到的
>
> 

## Gazebo Server

使用命令行进行仿真。

```
gzserver <world_filename>
```

The `<world_filename>` can be:

1. relative to the current directory,
2. an absolute path, or
3. relative to a path component in `GAZEBO_RESOURCE_PATH`.
4. `worlds/`, where `` is a world that is installed with Gazebo

For example, to use the `empty_sky.world` which is shipped with Gazebo, use the following command

```
gzserver worlds/empty_sky.world
```

```sh
gazebo worlds/empty_sky.world  # 同时启动gaserver和gzclient
```

### plugin 插件

http://gazebosim.org/tutorials/?tut=plugins_hello_world

```
gzserver -s <plugin_filename>，例如
gzserver --verbose -s libRestWebPlugin.so
gzclient的插件调用命令行：
gzclient -g libTimerGUIPlugin.so

```

## Communication Between Processes 

进程之间的通信

>  It supports the publish/subscribe communication paradigm. For example, a simulated world publishes body pose updates, and sensor generation and GUI will consume these messages to produce output.

rendering 渲染

- The client and server communicate using the gazebo communication library.

### Communication Library

- **Dependencies:** Protobuf and boost::ASIO
- **External API:** Support communication with Gazebo nodes over named topics
- **Internal API:** None
- **Advertised Topics:** None
- **Subscribed Topics:** None

This library is used by almost all subsequent libraries. It acts as the communication and transport mechanism for Gazebo. It currently supports only publish/subscribe, but it is possible to use [RPC](http://en.wikipedia.org/wiki/Remote_procedure_call) with minimal effort.

1. ### Physics Library

   - **Dependencies:** Dynamics engine (with internal collision detection)
   - **External API:** Provides a simple and generic interface to physics simulation
   - **Internal API:** Defines a fundamental interface to the physics library for 3rd party dynamic engines.

   **The physics library provides a simple and generic interface to fundamental simulation components, including rigid bodies, collision shapes, and joints for representing articulation constraints.** This interface has been integrated with four open-source physics engines:

   - [Open Dynamics Engine (ODE)](http://ode.org/)
   - [Bullet](http://bulletphysics.org/)
   - [Simbody](https://simtk.org/home/simbody)
   - [Dynamic Animation and Robotics Toolkit (DART)](http://dartsim.github.io/)

   A model described in the [Simulation Description Format (SDF)](http://sdformat.org/) using XML can be loaded by each of these physics engines. This provides access to different algorithm implementations and simulation features.

   ### Rendering Library 渲染库

   - **Dependencies:** OGRE
   - **External API:** Allows for loading, initialization, and scene creation
   - **Internal API:** Store metadata for visualization, call the OGRE API for rendering.

   The rendering library uses OGRE to provide a simple interface for rendering 3D scenes to both the GUI and sensor libraries. It includes lighting, textures, and sky simulation. It is possible to write plugins for the rendering engine.

   ### Sensor Generation

   - **Dependencies:** Rendering Library, Physics Library
   - **External API:** Provide functionality to initialize and run a set of sensors
   - **Internal API:** TBD

   The sensor generation library **implements all the various types of sensors, listens to world state updates from a physics simulator and produces output specified by the instantiated sensors**.

   传感器生成库实现了所有各种类型的传感器，监听来自物理模拟器的world的状态更新，并生成由实例化传感器指定的输出。

   ### GUI

   - **Dependencies:** Rendering Library, Qt
   - **External API:** None
   - **Internal API:** None

   The GUI library uses Qt to create graphical widgets （小工具（widget的复数）；窗体小部件）for users to interact with the simulation. The user may control the flow of time by pausing or changing time step size via GUI widgets. The user may also modify the scene by adding, modifying, or removing models. Additionally there are some tools for visualizing and logging simulated sensor data.

   ### Plugins

   The physics, sensor, and rendering libraries support plugins. These plugins provide users with access to the respective libraries without using the communication system.

   这些插件为用户提供了访问各自库的权限，而无需使用通信系统。

### 截图保存在~/.gazebo/pictures

## Link Inspector

双击直接调用Link Inspector

`Roll` to 1.5707 radians (90 degrees)

1. Next, resize the wheel by giving it exact dimensions. Go to the Visual tab to see the list of visuals in this link. There should only be one. Expand the visual item by clicking on the small arrow next to the `visual` text label. Scroll down to the `Geometry` section and change the `Radius` to 0.3m and `Length` to 0.25m.

   ![img](https://bitbucket.org/osrf/gazebo_tutorials/raw/default/guided_b/files/ftu4-wheel_visual.png)

2. You should now see a smaller cylinder inside a bigger cylinder. This is expected as we have only changed the visual geometry but not the collision. **A 'visual' is the graphical representation of the link and does not affect the physics simulation. On the other hand, a 'collision' is used by the physics engine for collision checking.** To also update the wheel's collision, go to the Collision tab, expand the only collision item, and enter the same Geometry dimensions. `Radius`: 0.3m and `Length`: 0.25m. Click on `OK` to save the changes and close the Inspector.

- 注意：可视化和碰撞检测的大小中，一般设置相同，但是，影响物理仿真的是碰撞检测的，可视化的无关



[Plugin tutorials](http://gazebosim.org/tutorials?cat=write_plugin)



导入从inkscape中生成svg文件的正确步骤：

> Your chassis is most likely not an SVG path. In inkscape:
>
> 1. Select the shape
> 2. Go to the "Path" menu
> 3. Select "Object to Path"
> 4. Save and load into gazebo.

这样就可以在Gazebo中调用svg模型，从而保存为gazebo的model

