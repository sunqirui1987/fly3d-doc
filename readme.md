
# Fly3D开发文档

------

## Fly3D简介

跨平台的Golang 的3D引擎

特性：动画模块，粒子系统，特效（阴影，雾等）模块，灯光（点光源，方向光源等）模块，碰撞模块，反光材质，漫反射材质等，相机模块，光线检测。


## Fly3D 架构
![3dengine](https://raw.githubusercontent.com/suiqirui1987/fly3d/master/doc/3dengine.png)


## 如何使用
go get -u https://github.com/suiqirui1987/fly3d

## 快速入门

```
    var engine *engines.Engine
    var scene *engines.Scene
    var app *windows.App

    appoption := &windows.AppOption{
		Width:  800,
		Height: 600,
	}
	
	//构建APP窗口
	var err error
	app, err = windows.NewApp(appoption)
	if err != nil {
		panic(err)
	}

    //初始引擎
	engine = engines.NewEngine(app, true)
	//初始场景
	scene = engines.NewScene(engine)
	
	//添加一个相机
	camera := cameras.NewArcRotateCamera("Camera", 0, 0, 10, math32.NewVector3(0, 0, 0), scene)
	camera.SetPosition(math32.NewVector3(-3, 3, 0))
	camera.AttachControl(app)
    
    //添加光源
	lights.NewPointLight("Omni", math32.NewVector3(20, 100, 2), scene)

    //添加BOX Mesh
	box := meshs.CreateBox("box", 2.0, scene, false)
	material := materials.NewStandardMaterial("box1", scene)
	material.DiffuseColor = math32.NewColor3(1, 0, 0)
	box.Material = material


    //开始绘制
	engine.RunRenderLoop(func() {

		if scene != nil {
			scene.Render()
		}
		engine.WipeCaches() // for draw ui
	})

	app.Run()
```

# 模块说明
### 1.动画模块
 

1. **说明**

    运行时动态更改结构体属性，达到动画效果.支持更改的数据类型 float32,vector3,quaternion, matrix4

2. **基本方法**
```
	var animation = animations.NewAnimation("animation", "Rotation.X", 30, animations.ANIMATIONTYPE_FLOAT,
		animations.ANIMATIONLOOPMODE_CYCLE)
```
参数1 - 动画的名称。

参数2 - 要修改的属性。具体取决于您要更改的内容。这里我们想要在X轴上旋转一个对象，因此它将是“Rotation.x”。

参数3 - 请求的每秒帧数,

参数4 - 更改类型。确切的值是：

```
    ANIMATIONTYPE_FLOAT      = 0  //数字型，可以是int,float64
	ANIMATIONTYPE_VECTOR3    = 1 // math32.Vector3 类型
	ANIMATIONTYPE_QUATERNION = 2 // math32.Quaternion 类型(四元数)
	ANIMATIONTYPE_MATRIX     = 3  //math32.Matrix 类型（4x4矩阵）
```
参数5 - 动画类型：

使用以前的值并递增它： ANIMATIONLOOPMODE_RELATIVE
从初始值重新启动：ANIMATIONLOOPMODE_CYCLE
保持常数： ANIMATIONLOOPMODE_CONSTANT

有了Animation对象，然后我们再定义**动画数组**，让类型的属性按我们的定义的规则运动
```
var keys = make([]*animations.AnimationKeyFrame, 0)
	keys = append(keys, &animations.AnimationKeyFrame{
		Frame: 0,
		Value: 0,
	})
	keys = append(keys, &animations.AnimationKeyFrame{
		Frame: 50,
		Value: math.Pi,
	})
	keys = append(keys, &animations.AnimationKeyFrame{
		Frame: 100,
		Value: 0,
	})
```

将**动画数组**添加到动画对象：
```
	animation.SetKeys(keys)
```

我们现在完成了动画相关的设置后，我们要将动画作用到 Animation对象上（注意必须实现 interfaces/IAnimationTarget 接口）
```
	// Fountain object
	var mes = meshs.CreateBox("foutain", 1.0, scene, false)
	mes.AddAnimation(animation)
```

最后 启动动画 
  animations.BeginAnimation(scene, mes, 0, 100, true, 1.0)

示例代码:
```
fly3d_demo/samples/animation.go
```

### 2.相机模块

    相机模块用来控制场景的交互行为，fly3d里面内置两种相机

    - a.弧形旋转相机(ArcRotateCamera)
     此摄像机始终指向给定的目标位置，并且可以围绕该目标旋转，目标作为旋转中心。它可以用光标和鼠标控制，也可以用触摸事件控制。
     
     构造弧形旋转相机
     ```
        var camera = cameras.NewArcRotateCamera("ArcRotateCamera", 1, 0.8, 20, math32.NewVector3(0, 0, 0), scene)
	camera.AttachControl(app)
     ```
    
    - b.自由相机(FreeCamera)
       此摄像机是六自由度相机，在ArcRotateCamera基础上多了一个平移向量
       构造自由相机
     ```
     	// Need a free camera for collisions
	var camera = cameras.NewFreeCamera("FreeCamera", math32.NewVector3(0, -8, -20), scene)
	camera.AttachControl(app)
     ```


### 3.碰撞模块
### 4.灯光模块
### 5.材质模块
### 6.网络模块
### 7.特效模块
### 8.贴图模块
