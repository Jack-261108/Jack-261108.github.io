---
title: netty的使用
date: 2024-03-17
tags: [linux]
---

netty的使用
LengthFieldBasedFrameDecoder的说明
1.可能产生的疑惑
1: 既然我都知道这是一个消息了，那这个类还有啥用？
解释：
其实我们并不知道这是一个消息,而我们之所以能知道只是一个消息，是因为这个类内部维护了几个指针，每当我们从链接起发的每一个消息都会让指针指向每个消息的末尾，这样，当下个数据来时，就知道消息的起始位置和终止位置
### 参数说明：
1
2
3
4
public
LengthFieldBasedFrameDecoder
(
int
maxFrameLength,
int
lengthFieldOffset,
int
lengthFieldLength,
int
lengthAdjustment,
int
initialBytesToStrip)
maxFrameLength 消息的最大长度
lengthFieldOffset  消息字段的偏移量
lengthFieldLength 消息的长度
lengthAdjustment  消息的修正值
例子
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
1
：本例中length字段的值为
12
(
0x0C
)，表示“HELLO, WORLD”的长度。默认情况下，解码器假定长度字段表示长度字段后面的字节数。因此，可以用简单的参数组合进行解码。
lengthFieldOffset   =
0
lengthFieldLength   =
2
lengthAdjustment    =
0
initialBytesToStrip =
0
(=
do
not strip header)
BEFORE
DECODE
(
14
bytes)
AFTER
DECODE
(
14
bytes)
+--------+----------------+      +--------+----------------+
| Length | Actual Content |----->| Length | Actual Content |
|
0x000C
|
"HELLO, WORLD"
|      |
0x000C
|
"HELLO, WORLD"
|
+--------+----------------+      +--------+----------------+
在偏移量
0
处的
2
字节长度字段，条带头
因为我们可以通过调用得到内容的长度 ByteBuf.readableBytes ()，您可能希望通过指定initialbyteststrip来剥离长度字段。在本例中，我们指定了
2
，这与length字段的长度相同，以剥离前两个字节。
lengthFieldOffset   =
0
lengthFieldLength   =
2
lengthAdjustment    =
0
initialBytesToStrip =
2
(= the length of the Length field)
BEFORE
DECODE
(
14
bytes)
AFTER
DECODE
(
12
bytes)
+--------+----------------+      +----------------+
| Length | Actual Content |----->| Actual Content |
|
0x000C
|
"HELLO, WORLD"
|      |
"HELLO, WORLD"
|
+--------+----------------+      +----------------+
2
个字节的长度字段在偏移量
0
，不剥头，长度字段表示整个消息的长度
在大多数情况下，length字段仅表示消息体的长度，如前面的示例所示。但是，在某些协议中，length字段表示整个消息的长度，包括消息头。在这种情况下，我们指定一个非零长度调整。因为这个示例消息中的长度值总是比正文长度大
2
，所以我们指定-
2
作为补偿的长度调整。
lengthFieldOffset   =
0
lengthFieldLength   =
2
lengthAdjustment    = -
2
(= the length of the Length field)
initialBytesToStrip =
0
BEFORE
DECODE
(
14
bytes)
AFTER
DECODE
(
14
bytes)
+--------+----------------+      +--------+----------------+
| Length | Actual Content |----->| Length | Actual Content |
|
0x000E
|
"HELLO, WORLD"
|      |
0x000E
|
"HELLO, WORLD"
|
+--------+----------------+      +--------+----------------+
3
字节长度字段在
5
字节报头的末尾，不剥离报头
下面的消息是第一个示例的简单变体。一个额外的报头值被附加到消息中。长度调整又为零，因为解码器在计算帧长时总是考虑前置数据的长度。
lengthFieldOffset   =
2
(= the length of Header
1
)
lengthFieldLength   =
3
lengthAdjustment    =
0
initialBytesToStrip =
0
BEFORE
DECODE
(
17
bytes)
AFTER
DECODE
(
17
bytes)
+----------+----------+----------------+      +----------+----------+----------------+
| Header
1
|  Length  | Actual Content |----->| Header
1
|  Length  | Actual Content |
|
0xCAFE
|
0x00000C
|
"HELLO, WORLD"
|      |
0xCAFE
|
0x00000C
|
"HELLO, WORLD"
|
+----------+----------+----------------+      +----------+----------+----------------+
3
字节长度字段在
5
字节报头的开始，不剥报头
这是一个高级示例，展示了在长度字段和消息体之间存在额外报头的情况。您必须指定一个正的lengthAdjustment，以便解码器将额外的报头计算到帧长度计算中。
lengthFieldOffset   =
0
lengthFieldLength   =
3
lengthAdjustment    =
2
(= the length of Header
1
)
initialBytesToStrip =
0
BEFORE
DECODE
(
17
bytes)
AFTER
DECODE
(
17
bytes)
+----------+----------+----------------+      +----------+----------+----------------+
|  Length  | Header
1
| Actual Content |----->|  Length  | Header
1
| Actual Content |
|
0x00000C
|
0xCAFE
|
"HELLO, WORLD"
|      |
0x00000C
|
0xCAFE
|
"HELLO, WORLD"
|
+----------+----------+----------------+      +----------+----------+----------------+
在
4
字节报头的中间偏移
1
处的
2
字节长度字段，剥离第一个报头字段和长度字段
这是上述所有例子的组合。在长度字段之前有一个前置报头，在长度字段之后有一个额外的报头。附加的报头影响lengthFieldOffset，额外的报头影响lengthAdjustment。我们还指定了一个非零initialbyteststrip来从帧中删除长度字段和前置报头。如果您不想剥离前缀头，您可以为initialbytestosterone指定
0
。
lengthFieldOffset   =
1
(= the length of HDR1)
lengthFieldLength   =
2
lengthAdjustment    =
1
(= the length of HDR2)
initialBytesToStrip =
3
(= the length of HDR1 + LEN)
BEFORE
DECODE
(
16
bytes)
AFTER
DECODE
(
13
bytes)
+------+--------+------+----------------+      +------+----------------+
| HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
|
0xCA
|
0x000C
|
0xFE
|
"HELLO, WORLD"
|      |
0xFE
|
"HELLO, WORLD"
|
+--
1
----+---
2
-----+-
1
-----+------
12
----------+      +------+----------------+
在
4
字节报头中间偏移量
1
处的
2
字节长度字段，剥离第一个报头字段和长度字段，长度字段代表整个消息的长度
让我们对前面的例子做另一个改变。与前一个示例的唯一区别是，length字段表示整个消息的长度，而不是消息体的长度，就像第三个示例一样。我们必须将HDR1和length的长度计算到lengthAdjustment中。请注意，我们不需要考虑HDR2的长度，因为长度字段已经包含了整个报头长度。
lengthFieldOffset   =
1
lengthFieldLength   =
2
lengthAdjustment    = -
3
(= the length of HDR1 + LEN, negative)
initialBytesToStrip =
3
BEFORE
DECODE
(
16
bytes)
AFTER
DECODE
(
13
bytes)
+------+--------+------+----------------+      +------+----------------+
| HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
|
0xCA
|
0x0010
|
0xFE
|
"HELLO, WORLD"
|      |
0xFE
|
"HELLO, WORLD"
|
+--
1
----+---
2
-----+-
1
-----+--------
12
--------+      +------+----------------+
处理器
@Sharable的作用
当netty尝试往多个channel的pipeline中添加同一个ChannelHandlerAdapter实例时，会判断该实例类是否添加了@Sharable，没有则抛出… is not a @Sharable handler, so can’t be added or removed multiple times异常
如果你添加的
不是单例Handler，你加不加@Sharable没有任何区别
如果
你添加的是单例Handler，只要它会被添加到多个channel的pipeline，那就必须加上@Sharable
对 LengthFieldBasedFrameDecoder和messagecodec的作用产生混淆
解释：
LengthFieldBasedFrameDecoder的作用是
确定消息边界
，而codec的作用是对接受的消息进行解码，对发送的消息进行编码。(编码的意思是：比如客服端要发送一条消息 I Love you 这个字符串，我们可能还需要加上一些额外的信息，比如 消息的头信息，消息的类型，消息体的长度。更通俗的解释就是把 I love you 编码成http消息，能被http所认识，就绪要加上http的请求头和其他信息)，而LengthFieldBasedFrameDecoder，的作用是确定一个消息的起始和结束。
​
