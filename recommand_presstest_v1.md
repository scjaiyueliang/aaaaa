# recommand压测结果 #

## 1 环境 ##

日期：2018-03-02

机器: 10.77.100.66  端口：18011
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
* log level = 2或5

样本：

* 特征为1个string类型数据，1个string类型数据，共2个
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
#### （1）CPU（log level = 2） 
从结果中可以看到，QPS最大约为`4000`

<table>
<tr>
<td>testParams</td><td>aveTime(us)</td><td>totalTime(us)</td><td>qps</td>
</tr>
<tr>
<td>json_1_200000_1_100</td><td>1165.40000</td><td>322732(us)	</td><td>309.85462</td>
</tr>
<tr>
<td>json_1_200000_10_100</td><td>1506.66100</td><td>363796(us)
</td><td> 2748.79328</td>
</tr>
<tr>
<td>json_1_200000_20_100</td><td>2468.35300</td><td>582093(us)
</td><td>3435.87708</td>
</tr>
<tr>
<td>json_1_200000_50_100</td><td>9138.59240</td><td>1377851(us)
</td><td>3628.83940</td>
</tr>
<tr>
<td>json_1_200000_100_100</td><td>23106.75040</td><td>2571835(us)
</td><td>3888.27433</td>
</tr>
<tr>
<td>json_1_200000_200_100</td><td>45006.91060</td><td>5029941(us)
</td><td>3976.18978</td>
</tr>
<tr>
<td>json_1_200000_500_100</td><td>121443.89068</td><td>12733601 (us)</td><td>3926.61903</td>
</tr>
<tr>
<td>json_1_200000_1000_100</td><td>248186.52231</td><td>25657419(us)</td><td>3897.50816</td>
</tr>
<tr>
<td>json_1_200000_1500_100</td><td>374820.92058</td><td>37937244 (us)</td><td>3953.89818</td>
</tr>
<tr>
<td>json_1_200000_2000_100 </td><td>551066.87200</td><td>12618405565 (us) </td><td>15.84986</td>
</tr>
<tr>
<td>json_1_200000_3000_100</td><td>1040492.37091</td><td>105942176  (us)</td><td>2831.73342</td>
</tr>
</table>
cpu利用率：500

测试中现象：当线程数小于1500时，qps接近4000，cpu利用率为500左右，但是在线程数为1500或者2000后，qps下降开始下降，下降程度每次测试不同,有时会下降为15，cpu利用率也会下降，有时下降为300，有时下降为50左右。

#### （2）CPU（log level = 3）  
从结果中可以看到，QPS最大约为`15000`

<table>
<tr>
<td>testParams</td><td>aveTime(us)</td><td>totalTime(us)</td><td>qps</td></tr>
<tr>
<td>json_1_200000_1_100</td><td>989.09000</td><td>305242(us)</td><td>327.60891</td>
</tr>
<tr>
<td>json_1_200000_10_100</td><td>920.86100</td><td>301379(us)</td><td> 3318.08122</td>
</tr>
<tr>
<td>json_1_200000_20_100</td><td>796.39150</td><td>289282(us)</td><td>6913.66902</td>
</tr>
<tr>
<td>json_1_200000_50_100</td><td>1678.52860</td><td>384834(us)</td><td>12992.61500</td>
</tr>
<tr>
<td>json_1_200000_100_100</td><td>4636.98380</td><td>688127(us)</td><td>14532.20118</td>
</tr>
<tr>
<td>json_1_200000_200_100</td><td>11074.11370</td><td>1346369(us)</td><td>14854.76864</td>
</tr>
<tr>
<td>json_1_200000_500_100</td><td>30598.42252</td><td>3353338 (us)</td><td>14910.51603</td>
</tr>
<tr>
<td>json_1_200000_1000_100</td><td>63391.88631</td><td>6684724(us)</td><td>14959.48075</td>
</tr>
<tr>
</table>
cpu利用率：1500   

测试中现象：没有出现qps下降的情况。

#### （3）CPU（log level = 5） 
从结果中可以看到，QPS最大约为`18000` 

<table>
<tr>
<td>testParams</td><td>aveTime(us)</td><td>totalTime(us)</td><td>qps</td>
</tr>
<tr>
<td>json_1_200000_1_100</td><td>836.87000</td><td> 289812  (us)	</td><td> 345.05127</td>
</tr>
<tr>
<td>json_1_200000_10_100</td><td>785.77200</td><td> 287906 (us)	</td><td> 3473.35589</td>
</tr>
<tr>
<td>json_1_200000_20_100</td><td> 744.14150</td><td> 284128	(us) </td><td> 7039.08098</td>
</tr>
<tr>
<td>json_1_200000_50_100</td><td>1604.31420</td><td>380916(us)	</td><td> 13126.25356</td>
</tr>
<tr>
<td>json_1_200000_100_100</td><td>3312.34780</td><td>556360(us)	</td><td> 17973.97369</td>
</tr>
<tr>
<td>json_1_200000_200_100</td><td>8295.52275</td><td>1100439(us) </td><td> 18174.56488</td>
</tr>
<tr>
<td>json_1_200000_500_100</td><td>20305.11874</td><td>2732749(us) </td><td>18296.59438</td>
</tr>
<tr>
<td>json_1_200000_1000_100</td><td>47607.78196</td><td>5526047(us) </td><td>18096.11826</td>
</tr>
<tr>
<td>json_1_200000_1500_100</td><td>78351.97323</td><td>8246289(us) </td><td>18190.00037</td>
</tr>
<tr>
<td>json_1_200000_2000_100</td><td>106243.16249</td><td>10990239(us) </td><td>18197.96640</td>
</tr>
<tr>
<td>json_1_200000_3000_100</td><td>160721.20229</td><td>16493662(us) </td><td> 18188.80489</td>
</tr>
</table>
cpu利用率：2000  

测试中现象：cpu利用率,qps没有出现下降的情况。  

### GPU ###

#### （1）GPU（log level = 2） 
从结果中可以看到，QPS最大约为`2900` 

<table>
<tr>
<td>testParams</td><td>aveTime(us)</td><td>totalTime(us)</td><td>qps</td>
</tr>
<tr>
<td>json_1_200000_1_100	</td><td>1917.11000</td><td>397835(us)	</td><td>251.36049</td>
</tr>
<tr>
<td>json_1_200000_10_100</td><td>2806.31300</td><td>492921(us)	</td><td>2028.72266</td>
</tr>
<tr>
<td>json_1_200000_20_100</td><td>5212.81300</td><td>738774(us)	</td><td>2707.18785</td>
</tr>
<tr>
<td>json_1_200000_50_100</td><td>15552.88260</td><td>1780904(us) </td><td>2807.56290</td>
</tr>
<tr>
<td>json_1_200000_100_100</td><td>33178.09700</td><td>3547987(us) </td><td>2818.49962</td>
</tr>
<tr>
<td>json_1_200000_200_100</td><td>68045.70395</td><td>7059521(us) </td><td>2833.05340</td>
</tr>
<tr>
<td>json_1_200000_500_100</td><td>173503.76214</td><td>17688688(us)	</td><td>2826.66527</td>
</tr>
<tr>
<td>json_1_200000_1000_100</td><td>348567.88738</td><td>35313690(us) </td><td>2831.76298</td>
</tr>
<td>json_1_200000_1500_100</td><td>909725.16069</td><td>91812263(us) </td><td>1633.76868</td>
</tr>
<td>json_1_200000_2000_100</td><td>1344041.35947</td><td>135351132(us) </td><td>1477.63818</td>
</tr>
<td>json_1_200000_3000_100</td><td>1640573.69558</td><td>165357108(us)  </td><td>1814.25524</td>
</tr>
</table> 
cpu利用率：900，gpu利用率：33   

测试中现象：当线程数小于1500时，qps接近2900，cpu利用率为900左右，gpu利用率为33，但是在线程数为1500后，qps下降开始下降为1600，cpu利用率也会下降为500，gpu利用率下降为17%。  

#### （2）GPU（log level = 5） 
从结果中可以看到，QPS最大约为`2900`

<table>
<tr>
<td>testParams</td><td>aveTime(us)</td><td>totalTime(us)</td><td>qps</td>
</tr>
<tr>
<td>json_1_200000_1_100	</td><td>1857.54000</td><td>391865(us) </td><td>255.18993</td>
</tr>
<tr>
<td>json_1_200000_10_100</td><td>2710.53000</td><td>482563(us) </td><td>2072.26828</td>
</tr>
<tr>
<td>json_1_200000_20_100</td><td>4964.80600</td><td>711279(us) </td><td>2811.83614</td>
</tr>
<tr>
<td>json_1_200000_50_100</td><td>14910.10160</td><td>1713841(us) </td><td>2917.42349</td>
</tr>
<tr>
<td>json_1_200000_100_100</td><td>32189.91580</td><td>3451764(us) </td><td>2897.06944</td>
</tr>
<tr>
<td>json_1_200000_200_100</td><td>65322.98475</td><td>6789918(us) </td><td>2945.54367</td>
</tr>
<tr>
<td>json_1_200000_500_100</td><td>166414.14164</td><td>16978240(us)	</td><td>2944.94600</td>
</tr>
<tr>
<td>json_1_200000_1000_100</td><td>336075.14243</td><td>34070165(us) </td><td>2935.11933</td>
</tr>
<td>json_1_200000_1500_100</td><td>502954.12872</td><td>50867119(us) </td><td>2948.85975</td>
</tr>
<td>json_1_200000_2000_100</td><td>671518.03175</td><td>67826612(us) </td><td>2948.69512</td>
</tr>
<td>json_1_200000_3000_100</td><td>1010904.97817</td><td>101940814(us) </td><td>2942.88409</td>
</tr>
</table>
cpu利用率：800，gpu利用率：33  

测试中现象：cpu利用率,qps没有出现下降的情况。
  
## 4 结果比较

<table>
<tr>
<td>dnn.so编译方式</td><td>日志级别</td><td>aveTime(us)</td><td>CPU利用率(%)</td><td>GPU利用率(%)</td><td>qps</td>
</tr>
<tr>
<td>CPU</td><td>2</td><td>1.1</td><td>500</td></td><td>0</td></td><td>4000</td>
</tr>
<tr>
<td>CPU</td><td>3</td><td>0.9</td><td>1500</td></td><td>0</td></td><td>15000</td>
</tr>
<tr>
<td>CPU</td><td>5</td><td>0.8</td><td>2000</td></td><td>0</td></td><td>18000</td>
</tr>
<tr>
<td>GPU</td><td>2</td><td>1.9</td><td>900</td></td><td>33</td></td><td>2900</td>
</tr>
<tr>
<td>GPU</td><td>5</td><td>1.8</td><td>800</td></td><td>33</td></td><td>2900</td>
</tr>
</table>
通过比较可以看出，CPU性能优于GPU。

## 5 待优化事项 ##

* 其他...

