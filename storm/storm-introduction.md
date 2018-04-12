### Storm的架构
- [x] Storm集群由一个主节点和多个工作节点组成。
- [x] 主节点运行了一个名为“Nimbus”的守护进程，用于分配代码、布置任务及故障检测。
- [x] 每个工作节点都运行了一个名为“Supervisor”的守护进程，用于监听工作，开始并终止工作进程。
- [x] Nimbus和Supervisor都能快速失败。而且是无状态的，这样一来它们就变得十分健壮，两者的协调工作是有zookeeper来完成的。
- [x] Zookeeper用于管理集群中的不同组件。
- [x] ZeroMQ是内部消息系统。
- [x] JZMQ是ZeroMQMQ的JavaBinding。

==有个名为storm-deploy的子项目，可以在AWS上一键部署Storm集群。==

---

### Storm基本概念
storm与hadoop的对比


<html>
<!--在这里插入内容-->
<table style="width:100%;background-color:#CCCCCC;" cellpadding="2" cellspacing="0" border="0" bordercolor="#000000">
	<tbody>
		<tr>
			<td>
				对比
			</td>
			<td>
				Hadoop
			</td>
			<td>
				Storm
			</td>
		</tr>
		<tr>
			<td rowspan="3">
				系统角色
			</td>
			<td>
				JobTracker
			</td>
			<td>
				Nimbus
			</td>
		</tr>
		<tr>
			<td>
				TaskTracker
			</td>
			<td>
				Supervisor
			</td>
		</tr>
		<tr>
			<td>
				Child
			</td>
			<td>
				Worker
			</td>
		</tr>
		<tr>
			<td>
				应用名称
			</td>
			<td>
				Job
			</td>
			<td>
				Topology
			</td>
		</tr>
		<tr>
			<td>
				组件接口
			</td>
			<td>
				Mapper/Reducer
			</td>
			<td>
				Spout/Bolt
			</td>
		</tr>
	</tbody>
</table>
<br />
</html>

- Nimbus:负责资源分配和任务调度。
- Supervisor：负责接受nimbus分配的任务，启动和停止属于自己管理的worker进程。
- Worker：运行具体处理组件逻辑的进程。
- Task：worker中每一个spout/bolt的线程称为一个task。在storm0.8之后，task不再与物理线程对应，同一个spout/bolt的task可能会共享一个物理线程，该线程称为executor。

---
### 架构图

![image](storm.png)
- **Topology**：storm中运行的一个实时应用程序，因为各个组件间的消息流动形成逻辑上的一个拓扑结构。
- **Spout**：在一个topology中产生源数据流的组件。通常情况下spout会从外部数据源中读取数据，然后转换为topology内部的源数据。Spout是一个主动的角色，其接口中有个nextTuple()函数，storm框架会不停地调用此函数，用户只要在其中生成源数据即可。
- **Bolt**：在一个topology中接受数据然后执行处理的组件。Bolt可以执行过滤，函数操作、合并、写数据库等任何操作。Bolt是一个被动的角色，其接口中有个execute（Tupleinput）函数，在接收到消息后会调用此函数，用户可以在其中执行自己想要的操作。
- **Tuple**：一次消息传递的基本单元。本来应该是一个key-value的map，但是由于各个组件间传递的tuple的字段名称已经事先定义好，所以tuple中只要按序填入各个value就行了，所以就是一个value list。
- **Stream**：stream是由无限制的tuple组成的序列。

---
### 技术栈
所谓的技术栈就是想了解下storm和topology是基于哪些技术建立的。
- [x] **Java和Clojure**：storm运行在Java虚拟机上。大部分是使用Java和Clojure开发的。
- [x] **Python**：所有storm的后台程序和管理命令都是使用单独一个可执行Python文件来启动。这包括了nimbus和supervisor后台程序，并包括发布管理命令。


---

### 使用场景
- [x] 流聚合
- 流聚合把两个或者多个数据流聚合一个数据流
- [x] 批处理
- 把一组tuple一起处理。
- [x] Basicbolt
- [x] 缓存+Fileds grouping组合


---
### 数据流分组
Storm定义了七种内置数据流分组的方式：
- [x] Shuffle grouping【随机分组】
- [x] Fields grouping【按字段分组】
- [x] All grouping【全复制分组】
- [x] Globle grouping【全局分组】
- [x] None grouping【不分组】
- [x] Direct grouping【指向型分组】
- [x] Local or shuffle grouping【本地或随机分组】

