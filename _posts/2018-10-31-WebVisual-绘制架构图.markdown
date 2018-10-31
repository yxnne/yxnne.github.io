---
layout: post
title:  可视化实践之工作中系统架构图
date:   2018-10-31
categories: WebVisual
tags: [WebVisual]
comment: true
---

# 需求

部门或者组织的业务系统往往不只一个，而是多个系统相互依赖，存在系统间相互调用(接口方法，数据相互调用等行为)。

1. 这样系统间其实在逻辑上是有层级的（假设调用者在高级，被调用者在低级），但同时，如果系统的足够复杂，那么这种层级关系又无法被清晰的认知。比方说A,B,C三个系统，假设A调用B，B调用C那么层次关系显而易见就是A > B > C， 但是，假设C也调用了A，那么关系图中将会形成闭环，这时候的问题就是，你如何划分层级，如果一定需要层次关系展示的话，势必要暂时取舍一些关系，那么如何取舍？这个是需要介入一定的业务规则的。

* **本案例第一个需求就是：清晰的展示系统间的层级关系**

2. 系统间的调用关系，在更微观的尺度可以表现为系统中接口（或者方法）间的调用。 假设A系统中有方法a1(), a2() ,a1()调用B系统的b1() , a2()调用C系统的c1()，那么,因为在方法层表现的关系为 a1 -> b1, a2 -> c1, 所以可以认为在系统层次表现的关系为A->B, A->C 。其实可以这么理解，正是因为有了微观上方法的关系，才有了相对宏观的系统关系。

* **本案例的第二个需求：清晰展示系统间方法调用的关系**

3. 系统集群中存在这一类特殊的角色系统--网关，网关有这几个特点：1） 网关的接口很多； 2） 网关是很多系统间方法调用的“源头”， 因为网关是针对外部系统集群的"总接口"，是流量的总闸门。
因为网关是总接口，那么针对网关上某一个方法做一定的特殊处理势必会影响该接口的“下游方法”，整个调用链路的方法都会被牵连。一个典型的场景是限流：假设对网关上的某一个接口限流，那么由网关中该方法发起的调用链路的所有方法都会被限制。

* **本案例的第三个需求：双向追溯：对一个系统或者方法，可以向上追溯来源，直到网关，也可以向下追溯去向**
（PS: 本例没有视觉稿）

# 方案
## 概要分析
* 图的类型
首先明确，该案例是展示各种关系的，所以一定是一副关系图。关系图就有节点，有边，所以数据结构明晰下：
* 数据结构
data :{nodes:[{oneNode, itsProps }, ], edges:[{oneEdge, itsProps }], props}
后台的数据是所有系统方法的节点作为nodes，和方法的调用关系作为edges。


## 技术选择
* d3: d3提供了关系图的力导向图算法，在布局上问题不大，但是毕竟d3是比较底层的库，节点，边，各种附加效果都需要自己实现。这在开发资源上是不允许的。

* antv g6:g6中的关系图相对d3要好上手很多，更有很多有用的插件，比如鱼眼插件，缩略图插件，最大生成树插件等。

如果使用d3,几乎所有的东西你都是可控的，但是，核心问题就是时间紧，不太用得起。
使用g6就没有这么灵活了，但是你需要的很多东西都是现成的，需要定制的功能也是有办法解决的。所以选择g6。

## 实现功能点分析

1. 图数据处理
原始数据是后台的数据是所有系统方法的节点作为nodes，和方法的调用关系作为edges。需要处理成节点nodes包括两类：1） 系统节点; 2） 方法节点 ; 边edges也包含两类：1) 系统间调用关系; 2) 方法间调用关系;
这里的核心工作在于: 需要从原始的方法关系中，得到系统的节点和关系并整合在一起。
```
-> 遍历原始数据的边edges：
  -> 得到每一个边的source  和 target 
  -> filter nodes 得到 source 和 target 这些都是方法节点，而方法节点的属性中包含有所属的系统信息。
  -> 将source和target系统的id作为在nodesSet中保存（Set可以去重）
  -> 记录一条关系在edgesSet中（Set可以去重）
```
这里就得到了一个nodesSet， edgesSet都是筛选过并去重的，根据这两个集合重新构造些点和边就能基本完成数据处理的要求。

2. 关系图布局实现
此图应是反应系统间的关系，同时反应系统和其方法的关系，以及方法和方法间的关系图。
系统与系统有一定的层次关系，系统与方法间有一定的从属关系。
这里的想法是，将节点划分成两类：系统节点和方法节点，让方法节点环绕系统节点来表现从属关系，将体现方法节点间关系的边暂时先隐藏，在需要查看时显示。
系统节点作为关系图的主要节点，系统调用边作为主要边，使用力导向图布局算法来布局这些主要节点。
不过只是使用力导向算法的图行会很杂乱，根本无法看清边，无法看清层级关系。
解决这个问题考虑使用g6封装的插件--最大生成树插件解决无法看清边的问题，使用dagre布局算法解决层级问题。

3. 关系追溯的实现
追溯关系，不只是要追溯上级，最好能追溯到源头，比如追溯某个方法直到网关上的某个接口。这里考虑递归沿着链路向上查找实现。
# 实现

## 点布局的实现
### 系统节点的布局
a. 利用最大生成树
系统节点的布局乃是本图的主题结构，布局基本使用g6最大生成树插件： >plugin.template.maxSpanningForest

此插件主要作用是，利用深度优先遍历，将错综复杂的图关系变成最大生成森林，原有关系当然保留，只是展示的时候按照最大生成森林的关系展示，这样就去除了一大批干扰视线的交织的关系线，使得图形更加清晰可见。这些清晰的关系插件中采用的是力导向算法布局的。
另外，这个插件除了布局，其实还是一个功能集合包括：

* 鱼眼放大镜；
* 鼠标进入节点上高亮该节点；
* 鼠标移出节点时高亮消失；
* 鼠标进入边时高亮该边；
* 鼠标移出边时高亮消失；
* 鼠标右击节点出现菜单；
* 根据菜单上的选择提取和高亮相关子图；

这样，图就变成类似这篇文章中讲的模样[plugin.template.maxSpanningForest 介绍](https://www.yuque.com/antv/g6/plugin.template.maxspanningforest)

b. 系统间层次关系
使用力导向算法布局，无法展示主节点（系统与系统）间的层次关系。
要想表现层次关系，最好是使用树形布局。因为数据结构并不是树图的数据结构（data:{props, child:{ props, child:{ ...}}}），所以无法利用现成的针对“树”的布局算法（g6中树的布局来自d3。
但是依然存在面向图数据结构布局，而表现出一定层级关系的开源算法（本质不是一棵树，但长相近似一棵树），比如Dagre算法。
g6中也有该算法的插件：plugin.layout.dagre（向图层次布局）

（既要使用最大生成树树插件，也要使用有向图层次布局插件的方法是：在构造G6对象的时候，同时引入两个插件）

### 方法节点的布局
现在，系统节点间的布局已经基本实现了，是图的数据，但是表现有树的特征。
这一步要做的是，将方法节点分布在系统节点周围。
修改 plugin.layout.dagre 插件，因为此layout插件最终是计算出了每个节点（方法节点）的位置。想法是，在计算出系统节点的位置后，拿到系统节点的从属方法节点，根据这个中心位置，环形布局各个方法节点。
```
    // 这时，主节点已经完成布局，计算出了每一个节点的位置 x, y

  // 得到每一个app对应的方法节点
    // 若 node type是method
    // 维护一个map map的 key为app value是其method节点的引用 
  // 数据结构为 {key:appid,value:[medthdNodes]}
    // 找到method对应的app节点
    // 拿到app的位置和大小
    // 遍历map通过key取到
    // 将method节点放到计算后的位置

    const map = {}
    const appNodeMap = new Map();
    nodes.forEach(oneNode =>{
      if(oneNode.type === TYPE_METHOD){
        const key = oneNode.props?oneNode.props.detail.app:oneNode.app;
        
        if (!map[key]) {
          const list = [];
          list.push(oneNode);
          map[key] = list;
        }else {
          map[key].push(oneNode);
        }
      } else if (oneNode.type === TYPE_APP){
        appNodeMap.set( oneNode.id, oneNode)
      }

    });
    // console.log('map', map)

  // 环形分布起来
    for(let key in map){
      // 通过key 拿到APP节点坐标
      const degreeFactor = 0.017453293; // 角度值乘以这个因子转换弧度值
      const appNode = appNodeMap.get(key);
      const baseX = appNode.x;
      const baseY = appNode.y;
      const r1 = 40; // 旋转半径
      const r2 = 56; // 旋转半径2
      const methodsNodes = map[key];
      if(Array.isArray(methodsNodes)){
        // console.log('appNode id', appNode.id, ' has ', methodsNodes.length, 'sub methods')
        methodsNodes.forEach((oneMethod, index) => {
          
          if(index < 12){
            const degree = index * 30 * degreeFactor;
            oneMethod.x = baseX + Math.sin(degree) * r1;
            oneMethod.y = baseY + Math.cos(degree) * r1;
          } else {
            const degree = (index * 30 - 15) * degreeFactor ; // 为了错位 起始角度不同
            oneMethod.x = baseX + Math.sin(degree) * r2;
            oneMethod.y = baseY + Math.cos(degree) * r2;
          }
        });
      }
    }
```


## 追溯的实现
利用最大生成树插件中提供了一个菜单功能，此菜单默认可以追溯某个节点的所有来源和去向（所有来源包括了被最大生成树舍弃的所有相关边），但只是上一级，要想追溯到源头，可以利用该方法扩展成一个递归，一级一级向上调用，直至找到源头。

```
  this._sourceNodesAndEdges =[]; // 查找到的所有边和节点
  this._sourceNodesTemp = new Set(); // 防止无限查找,去重

  _findSourceNodeAndEdge(focusNodes, originName){
    // oName 防止环形回调：递归无限调用栈溢出
    let oName = '';
    if (originName === undefined){
      oName = focusNodes[0].model.id
    } else {
      oName = originName;
    }

    const graph = this.graph;
    const {
      reNodes,
      reEdges
    } = Util.extract('in', focusNodes);
    if(reEdges[0] && reEdges[0].model){
      
      reEdges.forEach(oneEdge =>{
        const sourceName = oneEdge.model.source;
        const sourceNode = graph.find(sourceName);
        this._sourceNodesAndEdges = [...this._sourceNodesAndEdges, ...reEdges, ...reNodes];
        const hasOrigin = this._sourceNodesTemp.has(oName);
        if(hasOrigin){
          return ;
        }
        this._sourceNodesTemp.add(oName);
        this._findSourceNodeAndEdge([sourceNode], oName);
      });      
    } 
  }
  
```
PS: 这里，一定要注意写好退出条件，否则会调用栈溢出。

## 效果
* 层次布局
![层次布局效果](https://cdn.nlark.com/lark/0/2018/png/140030/1540955470076-5a686b60-3ba5-4fb1-8988-b08e28fbe244.png) 

* 向上追溯查找方法调用源头
![向上追溯查找方法调用源头](https://cdn.nlark.com/lark/0/2018/png/140030/1540955483975-1033f35a-0b67-4581-a96f-3c942044f137.png) 

* 向上追溯系统调用关系
![向上追溯系统调用关系](https://cdn.nlark.com/lark/0/2018/png/140030/1540955492964-ac0ae034-5d74-47ac-b1ef-bd0aa13845d9.png) 


# 总结

## 哪些可以优化
时间紧张，能力有限，可以优化的地方还是很多：
1. 布局上，目前这张图太扁平，但是从业务的角度上来讲这又是对的，所以针对扁平的情况有一种图叫径向系统图，就是将这些扁平的子系统环形布局，达到空间上美观以及更好的体验。

2. 性能上，代码还需要更加精炼和高效；

3. 代码与业务耦合度目前有点高，如何减小耦合？


## 哪些需要深入
1. 什么是最大生成树，最小生成树，深度优先遍历，广度优先遍历？图的遍历算法，最短路径问题...这些课本上的图论中的知识，不趁机复习一波更待何时？

2. 力导向算法的原理？d3中的实现，应用时那些参数的含义？

3. dagre算法同上？

4. g6的代码研究透彻了？它的插件机制，有没有除了修改插件代码而更灵活的扩展机制？

5. React中使用d3如何充分发挥react的特点，让它们更加高效的运转？

6. 代码设计或者模块设计中，架构上更加灵活和可配置？

这些都是值得深入研究的点~ 期待自己百尺竿头！

# 参考

[g6:有向图分层布局](https://www.yuque.com/antv/g6/plugin.layout.dagre)

[g6:最大生成树](https://www.yuque.com/antv/g6/plugin.template.maxspanningforest)

[g6仓库代码](https://github.com/antvis/)

[Dagre算法Wiki](https://github.com/dagrejs/dagre/wiki)

__宝剑锋从磨砺出,梅花香自苦寒来__
