# 直播系统传输架构

## 参与方

* 主播：A
* UDP服务器：B
* RTMP服务器：C
* 观众：P（P1、P2、P3……）
* 连麦者：D（D1、D2）

## 简单架构

普通直播时的架构比较简单。

A将自己采集的音频编码为Sa-A、采集的视频编码为Sv-A，经RTMP协议发送给C；观众P从C接收Sa-A、Sv-A进行解码播放。

## 连麦架构

观众中有人请求连麦并经允许后，则其成为连麦者（P成为D）；这时简单架构转换成连麦架构，UDP服务器B才参与进来。

### 主播与连麦者

D将自己采集的音频编码为Sa-D、采集的视频编码为Sv-D，经UDP协议发送给B，B转而发送给A；

A解码播放Sa-D，解码播放Sv-D。

A将自己采集的音频和Sa-D进行混合，然后编码得到Saa-A；将自己采集的视频和Sv-D进行混合，然后编码得到Svv-A。

A将自己采集的音频编码为Sa-A，将Sa-A和Svv-A经UDP协议发送给B，B转而发送给D。

D解码播放Sa-A，解码播放Svv-A。

A与D相当于在进行露脸视频通话，不同之处在于连麦者D看到的视频是Svv-A。

有更多连麦者（D1、D2）时，A、D1、D2三者之间的数据传输与露脸视频群聊一样。
例如，D2除了收到Svvv-A，还会收到Sv-D1，连麦者显示Svvv-A时将Sv-D1叠加到上面以提高质量。

### 直播给观众

主播与连麦者的通话要直播给观众，也就是需要将Saa-A和Svv-A传给C，然后观众P从C接收Saa-A、Svv-A进行解码播放。

Saa-A和Svv-A传给C的方案有以下两种。

方案1：A将Saa-A、Svv-A经RTMP协议上传给C；

方案2：A将Saa-A经UDP协议发送给B，由B把Saa-A和Svv-A经RTMP协议上传给C。

方案2比方案1，优势在于为A节省了一次发送Svv-A的数据量；劣势在于，Saa-A和Svv-A传给B经由了UDP协议，导致B无法可靠地准备上传给C的数据。

方案2.1: A将Saa-A和Svv-A传给B时均改为采用可靠协议（UDP+）。

方案2.1旨在弥补方案2的劣势，但问题在于可靠传输导致延迟大，A与D的实时通话不能保证。