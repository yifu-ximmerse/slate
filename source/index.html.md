---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - csharp


toc_footers:
  - <a href='https://github.com/lord/slate'>SDK下载地址</a>
includes:


search: true
---

# 简介
感谢您使用Ximmerse Slide-in AR头显。Ximmerse Slide-in AR头显的跟踪技术基于IR（红外射线）Marker跟踪技术。头显本身依靠手机的USB供电；IR Marker则不需要任何供电。


# 渲染原理
Ximmerse Slide-in AR双屏显示和传统VR类似。不用的是，AR的双屏画面会投射到头显的，由于镜片反射出来的图片会产生畸变。所以在Unity有两个摄像机专门负责生成RenderTexture，然后将生成的texture赋予到一个反畸变的mesh上。然后mesh上的图像再投射到曲面镜片后，这时人眼就可以看到正常的无畸变图像。

# 开发环境

# 视频教程 
##Hello World: Marker Tracking
[![IMAGE ALT TEXT](images/hello_world_marker_tracking.png)](http://v.youku.com/v_show/id_XMzU2OTM2ODkzMg==.html?spm=a2h3j.8428770.3416059.1)

# API文档

## XRInput


## XDevicePlugin
```csharp
using System.Collections;
using UnityEngine;
using Ximmerse.InputSystem;

public class Sample : MonoBehaviour {

    ControllerInput m_controllerInput;

	// Use this for initialization
    IEnumerator Start() 
    {
        XDevicePlugin.Init();
        XDevicePlugin.StartBLEScan();
        m_controllerInput = new ControllerInput("XCobra-0");

        while(m_controllerInput.connectionState!= DeviceConnectionState.Connected)
        {
            yield return null;
        }

        Debug.Log("Controller is connected!!");
        XDevicePlugin.StopBLEScan();
	}
	
	// Update is called once per frame
	void Update () 
    {
        if(m_controllerInput.connectionState == DeviceConnectionState.Connected)
        {
            Debug.Log("Battery Level = " + m_controllerInput.batteryLevel);
            if (m_controllerInput.GetButtonDown(XimmerseButton.Trigger))
            {
                Debug.Log("trigger hit!");
            }
        }
	}

	private void OnApplicationPause(bool pause)
	{
        if (pause)
            XDevicePlugin.OnPause();
        else
            XDevicePlugin.OnResume();
	}

	private void OnApplicationQuit()
	{
        XDevicePlugin.Exit();
	}
}
```

> 这段Sample Code介绍了XDevice的基本用法。每一步都非常重要。请开发者注意代码中的细节。
 

该类是Unity与Native库的桥梁，含有大量的static函数。
### XDevicePlugin.Init()
Type | 解释
--------- | ------- 
int | 返回数值在>=0时，代表执行成功。初始化SDK。在调用任何SDK函数前，必须保证该函数已经调用，否则任何SDK中的函数都无法正常工作。

### XDevicePlugin.Exit()
Type | 解释
--------- | ------- 
int | 返回数值在>=0时，代表执行成功。推荐在游戏关闭时调用，以此释放所有设备的连接。

### XDevicePlugin.onPause()
Type | 解释
--------- | ------- 
void | 必须在App进入background的时候调用。

### XDevicePlugin.onResume()
Type | 解释
--------- | ------- 
void | 必须在App从background进入前台时调用。

### XDevicePlugin.GetInputDeviceHandle(string name)
Type | 解释
--------- | ------- 
int | 通过设备名字来获取设备的handle。

### XDevicePlugin.GetInputDeviceName(int which)
Type | 解释
--------- | ------- 
string | 通过设备handle获取相应设备的设备名称。

### XDevicePlugin.SetMaxBleConnection(int num)
Type | 解释
--------- | ------- 
void | 设置当前最大可连接的手柄数量。最多可以支持2个手柄同时连接。默认情况下，手柄的最大连接数量为1.

### XDevicePlugin.GetMaxBleConnection()
Type | 解释
--------- | ------- 
int | 获取当前设置的最大可连接手柄数量。

### XDevicePlugin.StartBLEScan()
Type | 解释
--------- | ------- 
void | 开启手机的scan功能，扫描周围手柄设备。注意：扫描开启后，必须同事按下touchpad下面的两个按键才可以连接成功。

### XDevicePlugin.StopBLEScan()
Type | 解释
--------- | ------- 
void | 在手柄成功连接后，推荐使用该函数停止手机扫描功能。


## ControllerInput
```csharp
using UnityEngine;
using Ximmerse.InputSystem;

public class Sample : MonoBehaviour {

    ControllerInput m_controllerInput;

	// Use this for initialization
	void Start () {
        XDevicePlugin.Init();
        m_controllerInput = new ControllerInput("XCobra-0");
	}
	
	// Update is called once per frame
	void Update () {
        Debug.Log("Battery Level = " + m_controllerInput.batteryLevel);
        if(m_controllerInput.GetButtonDown(XimmerseButton.Trigger))
        {
            Debug.Log("trigger hit!");
        }
	}
}

```
> 这个是推荐使用的Constructor。"XCobra-0"代表“左”手柄”（其实手柄部分左右，这里用“左”和“右”只是为了方便解释）。类似的，"XCobra-1"代表“右”手柄。

此类代表真实手柄。手柄当前的位置，手柄姿态（Rotation），手柄按键数，手柄电量，手柄连接状态等均由这个类提供。

### ControllerInput.type 
Type | 解释
--------- | ------- 
ControllerType | ControllerInput的type。

### ControllerInput.handle 
Type | 解释
--------- | ------- 
int | ControllerInput的handle。该数据会被开发者频繁使用。

### ControllerInput.name 
Type | 解释
--------- | ------- 
string | ControllerInput的名字。

### ControllerInput.trackingResult
Type | 解释
--------- | ------- 
TrackingResult | 当前跟踪状态。

### ControllerInput.positionTracked
Type | 解释
--------- | ------- 
bool | 手柄位置是否被跟踪到。

### ControllerInput.rotationTracked
Type | 解释
--------- | ------- 
bool | 手柄的旋转姿态是否被跟踪到。

### ControllerInput.connectionState
Type | 解释
--------- | ------- 
DeviceConnectionState | 获取当前手柄的连接状态。

### ControllerInput.batteryLevel
Type | 解释
--------- | ------- 
int | 获取当前手柄的电量信息，数值区间为0-100。

### ControllerInput.UpdateState（）
Type | 解释
--------- | ------- 
void | 此函数已经被ControllerInput自行调用，无需开发者主动调用。这个函数的作用是从底层库中拉取最新的手柄状态。

### ControllerInput.TouchpadToDpad（）
Type | 解释
--------- | ------- 
void | 此函数将TouchPad的数据转化为DPad数据（但是仍保留原TouchPad数据）。在调用该函数后，开发者可以收到DPad类型的数据。

### ControllerInput.TouchpadToSwipe（）
Type | 解释
--------- | ------- 
void | 此函数将TouchPad的数据转化为Swipe动作（但是仍保留原TouchPad数据）。在调用该函数后，开发者可以收到Swipe类型的数据。

### ControllerInput.GetAxis（int axisIndex）
Type | 解释
--------- | ------- 
float | 此函数获取当前Touch Pad的位置信息。axisIndex=0时，获取Touch Pad的X/横向坐标。当axisIndex=1时，获取Touch Pad的Y/纵向坐标。当axisIndex=2时，获取Trigger的线性数据。注意：某些手柄不支持Trigger线性数据输出，所以输出的数据会成为0或1。

### ControllerInput.GetButton（uint buttonMask）
Type | 解释
--------- | ------- 
float | 此函数检测手柄当前按键状态。请使用`XimmerseButton`button mask。

### ControllerInput.GetButtonDown（uint buttonMask）
Type | 解释
--------- | ------- 
float | 此函数检测手柄当前按键和上一帧的对比，是否按下。请使用`XimmerseButton`button mask。

### ControllerInput.GetButtonUp（uint buttonMask）
Type | 解释
--------- | ------- 
float | 此函数检测手柄当前按键和上一帧的对比，是否松开。请使用`XimmerseButton`button mask。

### ControllerInput.GetPosition（）
Type | 解释
--------- | ------- 
Vector3 | 获取当前手柄相对于头显的位置，此位置为裸数据，不可以直接使用。

### ControllerInput.GetRotation（）
Type | 解释
--------- | ------- 
Quaternion | 获取当前手柄当前的旋转姿态，此位置为裸数据，不可以直接使用。

### ControllerInput.GetRotation（）
Type | 解释
--------- | ------- 
Vector3 | 获取当前手柄重力加速度计信息。

### ControllerInput.GetGyroscope（）
Type | 解释
--------- | ------- 
Vector3 | 获取当前手柄陀螺仪信息。注意：某些手柄不支持该功能，因此SDK只会输出0，0，0.

### ControllerInput.GetState（）
Type | 解释
--------- | ------- 
XDevicePlugin.ControllerState | 获取当前手柄的State。这个State会被ControllerInput自行使用。开发者通常无需处理这个State。

### ControllerInput.GetPrevState（）
Type | 解释
--------- | ------- 
XDevicePlugin.ControllerState | 获取手柄上一帧的State。这个State会被ControllerInput自行使用。开发者通常无需处理这个State。

### ControllerInput.StartHaptics( int strength, int frequency, float duration = 0.0f ）
Type | 解释
--------- | ------- 
void | 使手柄震动。strength：振动强度。frequency：振动频炉，目前只支持0。duration：震动时间长短。注意：某些手柄不支持该功能。

