研发（PC-2）	VLAN 10	仅内部互通，不可访问 VLAN 20、30
测试（PC-1）	VLAN 20	仅内部互通，不可访问 VLAN 10、30
IT 管理员（PC-5）	VLAN 30	可访问 VLAN 10 和 VLAN 20

VLAN 30（IT 管理员终端）配置
{

[S1] vlan 30

[S1-vlan30] interface Ethernet0/0/4

[S1-Ethernet0/0/4] port hybrid pvid vlan 30

[S1-Ethernet0/0/4] port hybrid untagged vlan 10 20 30

}

默认接收 PC-5 发来的无标签帧 → 归入 VLAN 30；
发出帧时，无论是 VLAN 10/20/30 的，都剥去标签，以 Untagged 方式送出，确保 PC 能正确接收

 VLAN 20（测试部）终端配置（PC-1）
{

[S1]interface ethernet0/0/2

[S1-Ethernet0/0/2] port hybrid pvid vlan 20

[S1-Ethernet0/0/2] port hybrid untagged vlan 20 30

}
接收 Untagged → 归入 VLAN 20；
来自 VLAN 20 和 30 的帧剥标签送出。

 VLAN 10（研发部）终端配置（PC-2）
 {

[S1]interface ethernet0/0/3

[S1-Ethernet0/0/3] port hybrid pvid vlan 10

[S1-Ethernet0/0/3] port hybrid untagged vlan 10 30
}

 S1 与 S2 交换机互联口配置（Tagged 中继）
{
[S1]interface ethernet0/0/1

[S1-Ethernet0/0/1] port hybrid tagged vlan 10 20 30

[S2]interface ethernet0/0/1

[S2-Ethernet0/0/1] port hybrid tagged vlan 10 20 30

}
带 Tag 转发 VLAN 数据，确保多个 VLAN 能跨交换机隔离传输

S2 交换机 Hybrid 接口配置
{
[S2] interface ethernet0/0/2

[S2-Ethernet0/0/2] port hybrid pvid vlan 20

[S2-Ethernet0/0/2] port hybrid untagged vlan 20 30

[S2] interface ethernet0/0/3

[S2-Ethernet0/0/3] port hybrid pvid vlan 10

[S2-Ethernet0/0/3] port hybrid untagged vlan 10 30

[S2] interface ethernet0/0/1

[S2-Ethernet0/0/1] port hybrid tagged vlan 10 20 30
}
接口 E0/0/1 与 S1 互联，为 Hybrid Tagged，收发 VLAN 10、20、30 的数据；
所有 VLAN Tag 在 S2 → PC 的方向都会被剥离，终端以 Untagged 方式通信；
VLAN 10 和 20 不互通，但管理员所在 VLAN 30 可访问两者。
