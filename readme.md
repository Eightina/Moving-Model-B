# 有关本模型MOVINGMODELB
----
## 1. 简要说明
本模型基于给定的货物转运时间分布数据，对洋山港引入集疏运中心和自动驾驶卡车的情况进行仿真。**添加了自动生成自动驾驶卡车数量的功能**
## 2. 运行

#### 2.1 
使用AnyLogic任意8.7.3及以上版本（PLE即可），导入**MovingModelB.alp**即可打开。

#### 2.2 
在view菜单打开Projects页面即可，找到**ParametersVariation:Main**右键运行即可进行参数仿真。
**注意，可视化仿真不可用。*

## 3. 运行结果

#### 3.1 csv输出
一部分输出形式为csv表格。包括：

>集疏运中心实时空箱数量：
>cargoData.xlsx

>集疏运中心实时卡车数目：
>centerTruckData.xlsx

>集疏运中心实时出口货物数目：
>exData.xlsx

>集疏运中心实时进口货物数目：
>ImData.xlsx

>在半途的实时自动卡车数目：
>halfwayTruckData.xlsx

#### 3.2 println输出
另一部分输出以sys.println方式显示在AnyLogic终端中。
```java
// 外来卡车发车总数
System.out.println("truckC:");
System.out.println(40000-truckCs.size());

// 对应集疏运中心规模和服务水平要求下的自动驾驶卡车车队规模
System.out.println("aiTruckNum:");
System.out.println(aiTruckNum);

// 预设的集疏运中心的卡车容量
System.out.println("distributionCenterVolume:");
System.out.println(distributionCenterVolume);

// 自动驾驶卡车发车总数
System.out.println("AiCargoSent:");
System.out.println(AiCargoSent);

// 自动驾驶卡车送达的空箱总量
System.out.println("aiPickCount:");
System.out.println(aiPickCount);

// 外来卡车排队总延误（单位：秒）
System.out.println("totalDelay:");
System.out.println(totalDelay);

//容许的发车延迟（单位：秒）
System.out.println("acceptableAiDelay:");
System.out.println(acceptableAiDelay);
```

**实时排队长度是`autoTruckQueCount`和`truckCQueCount`两个参数*

## 4. 当前功能

#### 4.1 现实还原
对外来卡车到达和提货的现实情况还原。依照给定数据、按泊松分布给出，精确到5分钟。

#### 4.2 集疏运中心约束
集疏运中心约束。包括集疏运中心可容纳最大卡车数量，超出时卡车进行排队。

#### 4.3 车队约束
自动驾驶卡车车队化。
自动驾驶卡车数目一定，没有可用卡车时，若恰有半途卡车在港口一定距离内，港口将任务发送给距离最近的半途卡车（这一距离是设定可容许延误*车速，详情见part5）；否则生成新的自动驾驶卡车。
自动驾驶卡车车载任务单，会依任务单按先进先出次序执行任务。

#### 4.4 输出
输出。能输出某些数据。按具体要求可以再加。

#### 4.5 画图
附加的画图功能。仿真运行完后，输出数据可以用文件夹内的 **pltr.py** 简单绘制折线图。
**请预装 `matplotlib` 库，并使用 jupyter notebook 运行。否则请注释掉 line3：*
```python
%matplotlib inline
```
#### 4.6 合适的自动卡车数量（新增）
此版本的模型可以根据预设的集疏运中心容量和可容许的发车延迟运行，给出对应的自动驾驶卡车数量`AiTruckNum`。

## 5. 参数调整
`Main`中的`acceptableAiDelay`和`distributionCenterVolume`分别是预设的可容许的发车延迟和集疏运中心卡车容量。
**可容许的发车延迟是指当港口需要发车时，若暂时没有空闲的自动驾驶卡车，容许等待的最长时长。超过此时长仍没有可用空闲卡车，则模型生成一辆新的自动驾驶卡车来发车。这也是本模型生成自动驾驶卡车车队规模的原理**
调整这两个参数以进行多次仿真。

## 6.补充：属性定义
收发点：
a.货物数量：包括空箱数量cargo、出口货物数量exStock两个属性；b.出口货物列表Imstock：点内的出口货物运单；c.卡车数量truckNum：点内外来集卡和自动驾驶卡车之和；d.卡车容量限制distributionCenterVolume：点内卡车数量的上限；e.排队长度：点外排队的两种卡车数量truckCQueCount和autoTruckQueCount; f.车队规模：当前受控的自动驾驶集卡群的Agent数量。
运单：
a.编号num：每个运单实例有唯一的运单号；b.行动move：运送的货物种类。
卡车：
a.运单op：集卡当前执行的任务运单；b.各种时刻和时间：包括启程时刻launchTime、到达时刻arrivalTIme、行程时间travelTime、排队延误时间delayTime；c.排队状态inQue：表示是否在排队中的布尔值；d.任务有序列表opList，自动集卡执行的任务；e.地点position：集卡所处的位置；f.空闲ready、ready2：表示港口集卡是否能接受运单和能否半途接单的布尔值。

货物收发点作为模型内信息流的中枢，具备计数和调配车货资源的能力。运单起到分类运输目的、承载流通信息的作用，是联系各Agent的关键桥梁。集车根据运单类型改变行为逻辑，起到运输货物的作用，是集疏运系统的重点分析对象和拥堵问题的重点来源，承载运单信息，是MAS的关键对象。




