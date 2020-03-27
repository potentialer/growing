# 基本使用

前提概要，理解`TensorFlow`。

- 使用图（graph）来表示计算任务.
- 使用会话（Session）和上下文（context）执行图.
- 使用tensor表示数据.
- 使用变量（Variable）维护状态
- 使用 feed 和 fetch 可以为任意的操作赋值或者获取数据.

## 综述

`TensorFlow`是一个编程系统，使用图来表示计算任务.途中的节点被称之为OP(operation)。一个OP获得0个或者多个 `Tensor`执行计算，产生0个或多个 `Tensor`.每个 `Tensor`是一个类型化的多维数组。

一个 `Tensor Flow`图描述了计算的过程。为了进行计算，图必须在会话里被启动。会话将图的OP分发到诸如CPU或者FGPU之类的设备上，同时提供执行OP的方法，这些方法执行后，将产生的 `tensor`返回。在Python语言中返回值为`numpy:nddarray`对象。

## 计算图

`TensorFlow`程序通常分为：构建阶段和执行阶段。

在构建阶段，op的执行步骤被描述成一个图。在执行阶段，使用会话执行执行图中的op。

EG：构建图训练神经网络程序，在执行图中反复执行训练图中的op。

# 构建图

构建图的第一步是创建源op（source op）。源op不需要输入。

详情可查看[构建图数据结构](./Appendix/ClassGraph.md)













































