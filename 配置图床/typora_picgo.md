![image-20200327210712163](../../upload/image-20200327210712163.png)

使用PicGo配置完成以后，打开.config/picgo/data.json，复制其中的“picBed”以及里面的内容到

.picgo/config.json下，最终如下，设置github图床的config.json:

```json
{
    "picBed": {
        "current": "github",
        "github": {
            "branch": "master",
            "customUrl": "",
            "path": "",
            "repo": "BoltLi/typora",
            "token": "56d6bbbb8b5d7da37f62320b409fcf0ffb5a3ce9"
        },
        "picgoPlugins": {}
    }
}
```

```sh
 [PicGo WARN] failed 2020-02-27 11:49:22 [PicGo ERROR] StatusCodeError: 422 - 
 {"message":"Invalid request.\n\n\"sha\" wasn't supplied.
 git不支持同名文件
```

---

> 话说我用过，用Typora写的笔记图片都放在本地一个文件夹里，然后都同步到github，然后用文本批量替换把相对路径替换成github图片路径
>
> 其实是typora设置图片放某一目录，然后只用同步这个目录图片到github即可，然后我写了个小程序，把目录下.md文档里的图片路径全替换成github链接😂这就是白嫖github图床吧
>
> **ljx**：也就是把文件从相对路径直接传送到github的网址上，然后在使用github的文件上传后的前缀进行对文件的整体替换
>
> 需要解决的问题：
>
> 1. git命令行速度慢，
> 2. git一次提交一个文件夹
> 3. Ctrl+F整体替换名称前缀
>
> 方案：每一次需要同步，将图片文件夹（如果要也可以加md文件）复制到typora文件夹下，进行同步（这样就会所有图片都同步到同一个仓库上去）然后将md文件修改相对路径为url

！！！最终解决方案：本地目录保存相对路径，然后直接通过拖拽上传整个文件夹到typora文件夹下

，要使用文件，直接使用url修改，保留相对路径的部分。(可以通过grep直接进行文本替换或者typora里面Ctrl+F)

![image-20200327210712163](https://raw.githubusercontent.com/BoltLi/typora/master/image-20200327210712163.png)

根据上面的照片知道url前缀，直接替换掉就行，如下：

![duck](https://raw.githubusercontent.com/BoltLi/typora/master/Gazebo_03_meshes.assets/TutorialMeshDuck.png)

[TutorialMeshDuck.png](https://github.com/BoltLi/typora/blob/master/Gazebo_03_meshes.assets/TutorialMeshDuck.png)

## 加速域名：

开源cdn：

https://cdn.jsdelivr.net/gh/BoltLi/typora/

也就是：

```sh
![duck](https://raw.githubusercontent.com/BoltLi/typora/master/Gazebo_03_meshes.assets/TutorialMeshDuck.png)
变成
![duck](https://cdn.jsdelivr.net/gh/BoltLi/typora/Gazebo_03_meshes.assets/TutorialMeshDuck.png)
```



![duck](https://cdn.jsdelivr.net/gh/BoltLi/typora/Gazebo_03_meshes.assets/TutorialMeshDuck.png)

**笔记直接同步github，md以及图片，同时，使用url的md也可以直接发布在cnblog上。**

![image-20200330152543987](https://cdn.jsdelivr.net/gh/BoltLi/typora//image-20200330152543987.png)

