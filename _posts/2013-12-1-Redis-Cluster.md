Redis集群配置

### 整体结构

```
			TwemProxy
		__________|__________
		|					|
	Master1				Master N
Slave1 	SlaveN		Slave 1	Slave N
		
			Redis Sentinel
```

### 简介

* 1. 10个主库：`Master0 ~ Master9` 端口分表为 `6380~6389`
* 2. 每个主库分表有2个从库：`Slave1 ~ Slave2` 端口分表为 `26380` `36380`，以此类推。
* 3. 使用[Redis Sentinel][1]管理主从容错切换。
* 4. 使用[TwemProxy][2]代理Redis连接，使用端口`6379`提供服务。
* 5. 使用[redis-twemproxy-agent][3] 管理自动更新TwemProxy。

### 启动流程

* 1. 启动所有redis Master主服务
* 2. 启动所有redis Slave从服务
* 3. 启动Redis集群管理Redis Sentinel服务
* 4. 启动TwemProxy代理服务
* 5. 启动redis-twemproxy-agent服务


[1]: http://redis.io/topics/sentinel
[2]: https://github.com/twitter/twemproxy
[3]: https://github.com/Stono/redis-twemproxy-agent