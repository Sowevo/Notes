# kafka相关操作
###　查询数据,消费者

```shell
# 执行命令,注意 --max-messages参数 限制条数 
$ /data/kafka/bin/kafka-console-consumer.sh  --zookeeper 127.0.0.1:2181 --topic Station_jilin  --from-beginning --max-messages 100
# 普通的查找全部数据,写入文件
$ /data/kafka/bin/kafka-console-consumer.sh  --zookeeper 127.0.0.1:2181  --topic SchedulePrice_neimengnew --from-beginning |grep '152531001110000005311' > dl.txt
# 后台查找全部数据,写入文件,避免ssh回话超时,要记得pid之后用来关闭
$ nohup /data/kafka/bin/kafka-console-consumer.sh  --zookeeper 127.0.0.1:2181  --topic SchedulePrice_gansu --from-beginning |grep '620801PYCZ' > 620801PYCZ.txt 2>&1 &
```
### 向kafka发送数据
```shell
$ /data/kafka/bin/kafka-console-producer.sh --broker-list 10.10.1.216:9092 --topic Station
```
### 调整偏移量

```shell
# 先检查状态
$ /data/kafka/bin/kafka-consumer-offset-checker.sh --zookeeper 127.0.0.1:2181 --topic mongoscheduleprice --group bus365pull_2019_6_6
# 进入zk控制台
$ /data/kafka/bin/zookeeper-shell.sh localhost:2181
# 获取偏移量信息
get /consumers/bus365pull_2019_6_6/offsets/mongoscheduleprice/0
get /consumers/组名/offsets/主题名/分区号
# 设置偏移量信息,1688599186为检查状态中此主题的总数据条数(logsize)
set /consumers/bus365pull_2019_6_6/offsets/mongoscheduleprice/0 1688599186
set /consumers/组名/offsets/主题名/分区号 logsize
```

# kafka监控js
```javascript
//要查的主题
var topic = "mongoscheduleprice"
var settings = {
  "async": true,
  "crossDomain": true,
  "url": "http://192.168.3.80:18080/group/bus365pull_2019_6_6",
  "method": "GET"
}

var previous_offsetsum = 0;
var previous_logSizesum = 0;
function getoffset(){
	$.ajax(settings).done(function (response) {
		offsetsum = 0;
		logSizesum = 0;
		for(var item of response.offsets) { // item代表数组里面的元素
			if(item.topic == topic){
				offsetsum = item.offset+offsetsum;
				logSizesum = item.logSize+logSizesum;
			}
		}
		console.table([
			{"name":"已消费数量","size":offsetsum},
			{"name":"消息总数量","size":logSizesum},
			{"name":"消息积压量","size":logSizesum-offsetsum},
			{"name":"上一次已消费数量","size":previous_offsetsum},
			{"name":"上一次消息总数量","size":previous_logSizesum},
			{"name":"已消费数量增加","size":offsetsum - previous_offsetsum},
			{"name":"消息总数量增加","size":logSizesum - previous_logSizesum},
			{"name":"积压数量增加","size":(logSizesum - previous_logSizesum)-(offsetsum - previous_offsetsum)}
		])
		previous_offsetsum = offsetsum;
		previous_logSizesum = logSizesum;
	});
}
getoffset();
window.setInterval("getoffset()",30 * 1000); 
```

