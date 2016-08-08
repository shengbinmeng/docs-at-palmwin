当前传输系统的理解

# 总体架构

客户端A（self）向服务器S发送统计信息(stat_info)，S分析总结这些信息，做出决策结果(result_set)，反馈给与A通话的客户端B（peer），指导其进行媒体数据发送的配置（例如，设定视频编码码率，进行FEC）。

# 媒体数据传输的两种方式

1. A与B以P2P方式直连，媒体数据直接由A传到B；
2. 媒体数据经S中转（relay），即先由A传到S，再由S传到B。

对于两人通话，1和2均可用（初始需选择一个）；且传输方式在初始决定后，后面可根据网络情况切换。对于群聊，只采用了2。

对于群聊，在S上设置两个虚拟客户端a和b，分别作为A和B的peer；A->a和b->B按两个传输段分别对待。
对于两人通话中的2方式，服务器只起到数据中转的作用，并无虚拟客户端来统计信息，仍只有A->B这一个传输段。

# 统计信息

统计信息stat_info每固定时间间隔（T＝500ms）汇报一次。其中的数据包括：

- 这T时间内所有发送的数据包信息（sendpacketinfo_list）; // 该list长度限为了200
- 这T时间内所有接收的数据包信息（recpacketinfo_list）; // 该list长度限为了200
- 过去N个T时间内发送数据包情况的总结信息（sendsummaryinfo_list）; // 该list长度限为了5（即N为5）
- 过去N个T时间内接收数据包情况的总结信息（recsummaryinfo_list)。// 该list长度限为了5

每个数据包信息包括：包号、包大小、包的发送时间和接收时间；
每个T时间内的总结信息包括：这段时间内所有包的最大包号、最小包号、总大小、总数量、总传输时间（每个包的传输时间为接收时间与发送时间之差）。

# 统计信息的收集和使用

对于实体客户端A和B，由客户端机器收集统计信息发送给服务器S；对于虚拟客户端a和b，由服务器S收集统计信息交给自己。

统计信息在服务器S上的网络控制模块中使用。

# 网络控制模块

该模块的输入为self定时汇报来的统计信息，其输出为不时传给peer的决策结果。

决策结果包括：应发送的码率（对当前网络带宽的估计），应进行FEC的冗余率（对当前网络丢包率的估计），应采用的传输方式（P2P还是relay）。