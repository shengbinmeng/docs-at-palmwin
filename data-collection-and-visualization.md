数据收集与可视化

# 概述

数据产生端通过socket将数据发送给集散地（Hub）。可视化程序可以通过socket从Hub读取数据并显示。
（通过socket发送主要是便于实时收集和观察，当然也可以在任何节点将数据存为文件进行离线使用。）

目前数据产生端主要是echorobot和udpServer。
Hub是运行在一个公网服务器上的[socket-hub](https://git.v5.cn/sbmeng/socket-hub)。
可视化程序是Mac版的[QoSTester](https://git.v5.cn/sbmeng/qos-tester)。

# 数据产生端：echorobot

echorobot会产生自己的终端状态数据（当为其配置ReportServerIP和ReportServerPort时），并将其按照编号为0x01的协议发送。

这些数据包括当前的可用带宽、接收到的建议码率、实际发送的码率等。

# 数据产生端：udpServer

udpServer的网络控制模块会产生分析性数据，并按照编号为0x03的协议发送。

# 可视化程序：QoSTester

QoSTester支持上述0x01、0x03协议，将收到的数据绘制成动态曲线。

协议可以按需定制和增加。