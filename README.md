# HPFlowC
分布式流控


可以采用动态配额申请：

1）初始部署的时候，根据节点数和静态流控QPS阈值，拿出一定比例的配额做初始分配，剩余的配额放在资源池

2）使用完配额的节点主动向注册中心申请配额，配额的申请：若流控周期为T，则将T分成更小的周期T/N N为经验值，默认为10 当前的节点总数M，则申请的配额为 (总的QPS-已分配的QPS配额)/M*T/N

3）总配额被申请完的时候，返回0个配额给申请的节点，就开始进行流控

注意一点：流控是为了保命，不是对流量进行限制，当系统负载压力过大的时候，系统进入过负载状态，可能是CPU、内存资源已过载，也可能是应用进程内的资源几乎耗尽，若继续处理全量业务可能会导致长时间的Full GC，消息严重堆压，或者进程宕机，最终将压力转移到集群其它节点，引起级联故障

流控也需要分级别，不同级别的流控，限制掉的流量值应该不同，比如1级流控，限制1/8的流量，二级流控限制1/4的流量
