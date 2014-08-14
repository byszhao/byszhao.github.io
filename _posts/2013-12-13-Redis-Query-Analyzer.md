Redis的query分析
=======================

### 环境

抽取redis集群（10个主从部署的redis）中的一台，使用 `MONITOR ` 命令实时导出1000条查询命令得到统计样本。
执行命令：
 
```sh
redis-cli -h 127.0.0.1 -p 6385 MONITOR | head -n 1000 >  /tmp/redis-out-6385.txt
```

开始时间：`2013年12月12日 10:43:50`
结束时间：`2013年12月12日 10:44:26`

### 脚本分析：

使用 [redis-faina](https://github.com/Instagram/redis-faina) 脚本分析

```sh
# python redis-faina.py /tmp/redis-out-6385.txt
```
### 分析结果:

``` sh
Overall Stats
========================================
Lines Processed      1000
Commands/Sec         27.56

Top Prefixes（按key前缀统计）
========================================
Hash              593    (59.30%)
SSet              340    (34.00%)
__sentinel__      7      (0.70%)
List              4      (0.40%)
comment           2      (0.20%)

Top Keys（操作最频繁的key）
========================================
Hash:user:count:2485622        71    (7.10%)
Hash:diary:count:84896016      42    (4.20%)
SSet:friends:3902952           38    (3.80%)
Hash:user:4194817              29    (2.90%)
Hash:user:count:4213134        28    (2.80%)
SSet:friends:4231823           26    (2.60%)
Hash:user:count:3541253        19    (1.90%)
SSet:followers:4211593         19    (1.90%)

Top Commands（执行最多的命令）
========================================
HGETALL      560    (56.00%)
ZCARD        334    (33.40%)
PING         36     (3.60%)
INFO         17     (1.70%)
HINCRBY      10     (1.00%)
PUBLISH      7      (0.70%)
DEL          7      (0.70%)
HMSET        6      (0.60%)

Command Time (microsecs)（命令执行时长）
========================================
Median      11902.5
75%         30755.25
90%         90490.0
99%         399452.0

Heaviest Commands (microsecs)（耗时最多的命令）
========================================
HGETALL        18684732.25
ZCARD          7519775.0
PING           4907849.5
INFO           2037476.25
HINCRBY        1553568.0
ZREVRANGE      475320.0
PUBLISH        306598.25
EXPIRE         271968.75

Slowest Calls（最慢的命令）
========================================
893037.0       "PING"
554276.25      "HGETALL" "Hash:user:count:4064648"
539275.0       "INFO"
478023.25      "PING"
432243.0       "HGETALL" "Hash:token:4231385"
419256.0       "PING"
409225.0       "ZREVRANGE" "SSet:friends:3902952" "0" "-1"
403649.75      "HGETALL" "Hash:user:count:4064648"
```

### 优化建议：

优化 Hash:user:count: ,Hash:diary:count的查询。


> :exclamation: 由于Redis的MONITOR 也对性能有所影响，所以使用时不要一直开启MONITOR来分析。可以采用定时抽样一段时间来做样本分析。
