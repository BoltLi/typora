<center><font size=7>Gazebo_03_meshes</font></center>

[toc]

---

# Import Meshes （导入3D网格）

http://gazebosim.org/tutorials?tut=import_mesh&cat=build_robot

关于mesh其他内容，见Gazebo_02_build_a_robot

> - Gazebo uses a right-hand coordinate system where +Z is up (vertical), +X is forward (into the screen), and +Y is to the left.
> - Gazebo中的坐标系为垂直于电脑进去为X轴正方向，左手边为Y轴正方向，Z轴正向垂直于XOY平面向上
>
> ![image-20200327152740615](https://raw.githubusercontent.com/BoltLi/typora/master/image-20200327152740615.png)

---

> **Reduce Complexity**
>
> Many meshes can be overly complex. A mesh with many thousands of triangles should be reduced or split into separate meshes for efficiency. Look at the documentation of your 3D mesh editor for information about reducing triangle count or splitting up a mesh.
>
> **Center the mesh**
>
> The first step is to center the mesh at (0,0,0) and orient the front (which can be subjective) along the x-axis.
>
> **Scale the mesh**
>
> Gazebo uses the metric system. Many meshes (especially those from 3D warehouse), use English units. Use your favorite 3D editor to scale the mesh to a metric size.
>
> 在进行导入前，先
>
> 1. 减少mesh复杂度，这个需要结合具体的3D编辑器进行设置，主要是减少其中的三角形的数量
> 2. 居中mesh到原点（0,0,0），对齐X轴
> 3. 缩放mesh到公制大小，一样是使用3D编辑器

## Export the Mesh

> export it as a Collada file.
>
> 完成前面的准备后，使用3D编辑器将mesh输出为collada格式文件

## Test the Mesh

```xml
<?xml version="1.0"?>
<sdf version="1.4">
    <world name="default">
        <include>
            <uri>model://ground_plane</uri>
        </include>
        <include>
            <uri>model://sun</uri>
        </include>

        <model name="my_mesh">
            <!-- <pose>0 0 0 0 0 0</pose> -->
            <pose>0 0 0 1.5708 0 0</pose>
            <static>true</static>
            <link name="body">
                <visual name="visual">
                    <geometry>
                        <mesh>
                            <uri>file://duck.dae</ur
                        </mesh>
                    </geometry>
                </visual>
            </link>
        </model>

    </world>
</sdf>

```

![image-20200327154525469](https://raw.githubusercontent.com/BoltLi/typora/master/image-20200327154525469.png)

在my_mesh目录下创建my_mesh.world，同时导入duch.dae在同一目录下，

然后就可以在这个目录直接启动：

```sh
gazebo my_mesh.world
```

![image-20200327212810551](https://raw.githubusercontent.com/BoltLi/typora/master/image-20200327212810551.png)

注意其中的pose中的1.5708对应的是将其原来的模型绕X轴旋转90度，也就是$\frac {\pi} {2}$

---

# Attach Meshes

[Attach a Mesh as Visual](http://gazebosim.org/tutorials/?tut=attach_meshes)

### 操作

- （下文其实就是对可视化的mesh进行修改，只是为了更好的显示效果）

---

在~/.gazebo/models/my_robot下，对model.sdf文件进行修改：

```xml
<static>true</static>
<!-- 对底盘<link name='chassis'>下的visual元素设置为使用pioneer2dx的底盘chassis-->
<!-- 
    <visual name='visual'>
      <geometry>
        <box>
          <size>.4 .2 .1</size>
        </box>
      </geometry>
    </visual>
-->
    <visual name='visual'>
      <geometry>
        <mesh>
          <uri>model://pioneer2dx/meshes/chassis.dae</uri>
        </mesh>
      </geometry>
    </visual>
```

---

### 程序说明

如上设置以后，拖动My Robot model到Gazebo中，就会看到，底盘由原来的`0.4*0.2*0.1`的长方形变成如下：

再优化一下，缩小底盘的各个轴方向的尺寸，同时，指定摆放位置。

```xml
            <visual name="visual">
                <pose>0 0 0.05 0 0 0</pose>
                <geometry>
                    <mesh>
                        <uri>model://pioneer2dx/meshes/chassis.dae</uri>
                        <scale>0.9 0.5 0.5</scale>
                    </mesh>
                </geometry>
            </visual>
```



![image-20200327232341674](https://raw.githubusercontent.com/BoltLi/typora/master/image-20200327232341674.png)

- 自己尝试：

1. Find and download a new mesh on [3D Warehouse](https://3dwarehouse.sketchup.com/). Make sure the mesh is in the Collada (.dae) format.
2. Put the mesh in the `~/.gazebo/models/my_robot/meshes`, creating the `meshes` subdirectory if necessary
3. Use your new mesh on the robot, either as a replacement for the chassis, or as an additional `<visual>`.

Note: Materials (texture files such with extension like .png or .jpg), should be placed in `~/.gazebo/models/my_robot/materials/textures`.



# Add a Sensor to a Robot

http://gazebosim.org/tutorials/?tut=add_laser

 [Gazebo Model Database](https://bitbucket.org/osrf/gazebo_models/src)

[SDF](http://gazebosim.org/sdf/)

 [<include>](http://sdformat.org/spec?ver=1.5&elem=world#world_include) tags and [<joint)>](http://sdformat.org/spec?ver=1.5&elem=joint) 

### 操作

在my_robot的model.sdf的``</model>``标签的前面加入如下：

```xml
        <include>
            <uri>model://hokuyo</uri>
            <pose>0.2 0 0.2 0 0 0</pose>
        </include>
        <joint name="hokuyo_joint" type="fixed">
            <child>hokuyo::link</child>
            <parent>chassis</parent>
        </joint>
```

---

###  程序说明

> 使用的激光是hokuyo这个model的，具体形状可以从[hokuyo model's SDF](https://bitbucket.org/osrf/gazebo_models/src/6cd587c0a30e/hokuyo/model.sdf?at=default)中的visual知道是hokuyo.dae这个mesh的，所以加入的时候看到的就是这个网格数据所展现的形状
>
> ```xml
>       <visual name="visual">
>         <geometry>
>           <mesh>
>             <uri>model://hokuyo/meshes/hokuyo.dae</uri>
>           </mesh>
>         </geometry>
>       </visual>
> ```
>
> ---
>
> The `<include>` block tells Gazebo to find a model, and insert it at a given `<pose>` relative to the parent model. 
>
> `<include>`标签指定了模型的查找位置，以及摆放位置
>
> The `uri` block tells gazebo where to find the model inside its model database (note, you can see a listing of the model database uri used by these tutorials [here](http://models.gazebosim.org/), and at the corresponding [mercurial repository](https://bitbucket.org/osrf/gazebo_models)).
>
> 对于其中的model，系统会自动检索路径，一般是以下两个默认路径，所以直接下载所有的模型以后移动到下面的两个位置中的其中一个
>
> /home/ljx/.gazebo/models
>
> /usr/share/gazebo/models

---

> The new `<joint>` connects the inserted hokuyo laser onto the chassis of the robot. The joint is `fixed` to prevent it from moving.
>
> 新增激光传感器为底盘chassis的一个关节joint，设置为固定。

---

The `<child>` name in the joint is derived from the [hokuyo model's SDF](https://bitbucket.org/osrf/gazebo_models/src/6cd587c0a30e/hokuyo/model.sdf?at=default), which begins with:

```xml
    <?xml version="1.0" ?>
    <sdf version="1.4">
      <model name="hokuyo">
        <link name="link">
```

> When the hokuyo model is inserted, the hokuyo's links are namespaced with their model name. In this case the model name is `hokuyo`, so each link in the hokuyo model is prefaced with `hokuyo::`. （[preface](http://www.youdao.com/w/eng/preface/?spc=preface#keyfrom=dict.typo) n. 前言；引语 | vt. 为…加序言；以…开始...）
>
> hokuyo:: 相当于C++中的命名空间，对应的hokuyo::link这里表示hokuyo下的名叫link的模型，显示的对应的是link下的visual元素内包含的mesh的形状

![img](https://bitbucket.org/osrf/gazebo_tutorials/raw/default/add_laser/files/Add_laser_pioneer.png)

### 同理可以添加相机：

```xml
        <include>
            <uri>model://camera</uri>
            <pose>0.2 0 0.3 0 0 0</pose>
        </include>
        <joint name="camera" type="fixed">
            <child>camera::link</child>
            <parent>chassis</parent>
        </joint>
```

![image-20200328071416584](https://raw.githubusercontent.com/BoltLi/typora/master/image-20200328071416584.png)

## Make a Simple Gripper

http://gazebosim.org/tutorials/?tut=simple_gripper