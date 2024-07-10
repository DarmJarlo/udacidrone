我自己的理解：
主要文件在udacidrone内
1.message_types 定义了msg，和messaging里面的message_ids不一样
2.在backflyer.py里面的start， call的顺序是：drone.start(),connection.start(),dispatch_loop(),
dispatch_loop中两个关键函数，一个wait_for_message,一个dispatch_message查看msg.get_type 然后执行conn.notify_message_listener, go through the list of registered listeners for the msgid.  然后执行callback
3.关于state是如何transition的
消息接收：

在初始化 (__init__) 时，Drone 对象配置了一个消息接收回调函数 on_message_receive，用于监听所有从 connection 接收到的消息。
self.connection.add_message_listener(MsgID.ANY, on_message_receive) 这行代码将 on_message_receive 这个回调函数绑定到所有消息上。
处理接收到的消息：

当消息到达时，on_message_receive 函数被调用，并接收消息名称 (msg_name) 和消息内容 (msg)。
on_message_receive 函数中，根据消息名称来决定如何处理消息。例如，如果消息名称是 MsgID.STATE，则调用 _update_state 方法更新状态。
状态更新函数：

_update_state(self, msg) 方法负责更新与状态相关的属性，如 self._armed, self._guided, self._status 等。
类似地，其他更新方法如 _update_global_position，_update_local_position 等，更新相应的属性。
在这些更新方法中，会检查消息的时间戳，并计算消息的频率。

Welcome to the UdaciDrone API!

[![CircleCI](https://circleci.com/gh/udacity/udacidrone.svg?style=svg)](https://circleci.com/gh/udacity/udacidrone)

## Overview ##

This is the Udacity Drone Python API. It provides a protocol agnostic API for communicating with a quadcopter, be it in the simulators provided in the Flying Car Nanodegree Program or even some real drones.

The API is designed in two parts: a `Drone` class and a set of connection classes.  The `Drone` class provides a representation of the physical or simulated drone and exposes the core API enabling interaction with the connected drone.  The connection classes are protocol specific implementations of the abstract `Connection` class, enabling the `Drone` to be configured to communicate over several different protocols used with drones today.

For those looking to just dive right in to using the UdaciDrone API, [check out our Getting Started guide](https://udacity.github.io/udacidrone/docs/getting-started.html).

## Drone ##

The core element of the UdaciDrone API is the Drone class.  This drone class is a representation of the physical or simulated drone you are connect to.  Through this Drone class, you are able to retrieve state information and send various commands.  Most importantly, `Drone` is communication protocol independent, which means any code you write interacting with `Drone` will work on any simulator or drone who's protocol has a `Connection` implementation!

For a detailed understanding of the `Drone` class, check out the detailed [Drone](https://udacity.github.io/udacidrone/docs/drone-api.html) documentation.

## Connection ##

These set of classes contain implementations of specific communication protocols over which `Drone` can connect with a real drone or simulator.

### Currently Supported Protocols ###

While the `Drone` API is designed to be protocol agnostic, in order to communicate with different types of drones, an implementation of each desired protocol needs to be built in the backend.  Currently, UdaciDrone supports the following communication protocols:

 - the [Mavlink Protocol](https://mavlink.io/en/) used in the [Dronecode](https://www.dronecode.org/) community.

 - [cflib](https://github.com/bitcraze/crazyflie-lib-python) the API used for [Bitcraze's crazyflie](https://www.bitcraze.io/crazyflie-2/)

This means that this API currently works with the [Udacity Simulator](https://github.com/udacity/FCND-Simulator-Releases/releases/tag/0.0.1) any [PX4](http://px4.io/) powered drone, and the crazyflie!.

For a detailed understanding of the `Connection` class, check out the detailed [Connection](https://udacity.github.io/udacidrone/docs/connection-api.html) documentation.

## More Details ##

For more detailed documentation, [check out our docs page](https://udacity.github.io/udacidrone/).
