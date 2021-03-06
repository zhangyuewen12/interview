维度表这个概念，从数仓方面来看，是区别于事实表的，

事实表是业务中真实存在的数据。

而维度表通常用来表示业务属性信息，例如针对订单信息的商品属性，针对用户信息的地区属性等都可以称之为维度表。

  flink流处理在进行实时画像，实时分析以及实时数仓建设时，同样需要使用维度表来完成一些数据过滤或字段补齐的操作。不过我们所需的维度信息通常是存储在Mysql/Redis/Hbase/Es这些外部数据库中，并且可能随时会发生变动，根据业务要求，数据的时效性需要根据不同程度的维表数据变化频率来决定，在实际使用中，常会有以下几种方案：

> 维表jion的四种方式：
>
> 1.预加载维表
>
> 2.热存储关联
>
> 3.广播维表
>
> 4.temporal table function join



1. 在维度表数据量较小且业务要求时效性不高的情况下，可以定时加载全量维表数据到内存中，直接从内存中查询维度数据；
2. 在维度表数据量比较大且业务要求的时效性不高的情况下，这时候全量加载维表就会撑爆内存。所以可以考虑使用LRU的缓存策略，当缓存的维度数据达到一定大小，采用淘汰最近最少使用的数据，同时设置数据的过期时间；
3. 业务要求数据时效性比较高时，那么就需要flink实时来查询外部存储，此时需要注意外部存储所能承受的QPS；
4. 最后一种类似于近期发布的broadcaststate，可以直接将维度数据发送到kafka中，flink任务消费kafka的维度数据，然后使用广播方式将维度数据广播到每一个处理task中，该方式同样要求维表数据量比较小。





