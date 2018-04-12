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
架构图

![image](storm.png)
- **Topology**：storm中运行的一个实时应用程序，因为各个组件间的消息流动形成逻辑上的一个拓扑结构。
- **Spout**：在一个topology中产生源数据流的组件。通常情况下spout会从外部数据源中读取数据，然后转换为topology内部的源数据。Spout是一个主动的角色，其接口中有个nextTuple()函数，storm框架会不停地调用此函数，用户只要在其中生成源数据即可。
- **Bolt**：在一个topology中接受数据然后执行处理的组件。Bolt可以执行过滤，函数操作、合并、写数据库等任何操作。Bolt是一个被动的角色，其接口中有个execute（Tupleinput）函数，在接收到消息后会调用此函数，用户可以在其中执行自己想要的操作。
- **Tuple**：一次消息传递的基本单元。本来应该是一个key-value的map，但是由于各个组件间传递的tuple的字段名称已经事先定义好，所以tuple中只要按序填入各个value就行了，所以就是一个value list。
- **Stream**：源源不断传递的tuple就组成了stream。
---
