# feed_dnn压测结果 #

## 1 环境 ##

日期：2018-03-02

机器: 10.77.100.66  端口：18010
GPU型号：M40 个数：2

* CPU版本直接在物理机上启动
* GPU版本在docker中通过host模式提供服务 (没有限制GPU个数)

都打开了batching参数，具体设置如下

* 单个batch大小 15
* batch线程数 48
* batch队列的大小 48
* 其他参数默认

service配置：

* 版本 5.3.2
* worker count=32
* max_connection=1000
* log level = 5

样本：

* 特征为23个string类型数据，66个string类型数据，共89个
* 每次请求发送15条样本

>注意
由于未知原因，每次服务器启动后的第一次请求耗时通常较长，以下测试均为启动服务后耗时稳定后的结果

## 2 方法 ##

使用service框架源码下的presstest工具（由`pluginservice-framework-multithread/client/cpp/src/main.cpp`编译而来）。
(修改后的源码路径在66上，/data1/usr/dingping/pluginservice-framework-multithread/client/cpp/src/main.cpp，如需求购，cd到src下的build目录，make即生成新的presstest二进制文件)

其中打印的日志:
    
* `grep "json presstest"` 为单个请求耗时
* `grep total` 为线程启动到线程结束总耗时，所以注意这个时间包括线程启动的开销

## 3 结果 ##

### CPU ###

从结果中可以看到，QPS最大 `1196` ，延迟最小 `16` ms, qps达到最大时的延迟为 `1238` ms。

<table>
<tr>
<td>testParams</td><td>aveTime(us)</td><td>totalTime(us)</td><td>qps</td>
</tr>
<tr>
<td>json_1_200000_1_100	</td><td> 16546.67000	</td><td> 1860935  (us)	</td><td> 53.73643 </td>
</tr>
<tr>
<td>json_1_200000_10_100	</td><td>17762.50100</td><td>  2017549 (us)	</td><td> 495.65091 </td>
</tr>
<tr>
<td>json_1_200000_20_100	</td><td> 22427.87950 </td><td> 2511647	(us)	</td><td> 796.29024 </td>
</tr>
<tr>
<td>json_1_200000_50_100	</td><td>39618.74720 	</td><td> 4323355  (us)	</td><td> 1156.50924 </td>
</tr>
<tr>
<td>json_1_200000_100_100	</td><td> 80628.69140	</td><td> 8427638  (us)	</td><td> 1186.57209 </td>
</tr>
<tr>
<td>json_1_200000_200_100	</td><td> 162641.22900  </td><td> 16803052	(us)	</td><td> 1190.25996 </td>
</tr>
<tr>
<td>json_1_200000_500_100	</td><td>406671.40472	</td><td>  41909713  (us)	</td><td> 1193.04086 </td>
</tr>
<tr>
<td>json_1_200000_1000_100    </td><td>821427.38568    </td><td>  83653923  (us)    </td><td> 1195.40120 </td>
</tr>
<tr>
<td>json_1_200000_1500_100    </td><td>1238602.16859    </td><td>  125408272  (us)    </td><td> 1196.09335 </td>
</tr>
<tr>
<td>json_1_200000_2000_100    </td><td>1651562.59606    </td><td>  167091389  (us)    </td><td> 1196.94977 </td>
</tr>
<tr>
<td>json_1_200000_3000_100    </td><td>2465821.90034    </td><td>  250766361  (us)    </td><td> 1196.33271 </td>
</tr>

</table>

### GPU ###

从结果中可以看到，QPS最大 `32` ，延迟最小 `41` ms, qps达到最大时的延迟为 `30643` ms。

<table>
<tr>
<td>testParams</td><td>aveTime(us)</td><td>totalTime(us)</td><td>qps</td>
</tr>
<tr>
<td>json_1_200000_1_100	</td><td>41778.03000	</td><td>4383903(us)	</td><td>22.81072</td>
</tr>
<tr>
<td>json_1_200000_10_100	</td><td>309432.86500 	</td><td>31617164(us)	</td><td>31.62839</td>
</tr>
<tr>
<td>json_1_200000_20_100	</td><td>622074.91900</td><td>	62735851(us)	</td><td>31.87970</td>
</tr>
<tr>
<td>json_1_200000_50_100	</td><td>1543690.33280  </td><td>157249795(us)	</td><td>31.79654</td>
</tr>
<tr>
<td>json_1_200000_100_100	</td><td>3132180.00490   </td><td>317491063(us)	</td><td>31.49695</td>
</tr>
<tr>
<td>json_1_200000_200_100	</td><td>6244087.97885  </td><td>	629025631(us)	</td><td>31.79521</td>
</tr>
<tr>
<td>json_1_200000_500_100	</td><td>15373572.74838	</td><td>1545488316us)	</td><td>32.35223</td>
</tr>
<tr>
<td>json_1_200000_1000_100    </td><td>30643291.33776    </td><td>3079489362(us)    </td><td>32.47292</td>
</tr>

</table>

测试过程中GPU利用率较小，没有超过30%

## 待优化事项 ##

* 使omp生效，或手动多线程进行特征处理
* 客户端多线程
* 其他...

