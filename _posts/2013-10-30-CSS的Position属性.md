---
layout: post
title: CSS的Position属性
---

大多数浏览器的初始(根节点)容器(containing block)是viewport,对于不是根节点的元素,有以下规定:
####static
默认值

**containing block**:最近的block-level,table cell,inline-block ancestor box的content edge

####relative
保持之前的类型以及元素占用的空间

**containing block**:与static相同

**offset**:如果同时设置了top和bottom,或left和right,则bottom=top的负值,right=left的负值

####absolute
设置后元素完全脱离出文档流,之前占用的空间会消失.不论之前的元素是什么类型,都会变为block-level

**containing block**:为最近的position值不为static的ancestor

* ancestor--block level,containing block从ancestor的padding边缘开始,即它以border为边界
* ancestor--inline level,containing block从ancestor的content边缘开始
* 没有ancestors时,containing block为初始容器

**offset**:没有限制width或height且offset设为auto时,自动转换为0

* non-replaced
    1. left,width,right全为auto,左边界为static position的边缘,width尽可能容纳所有内容,right为到右边界的距离
    2. 如果计算的宽度,高度与containing block不一样,则水平方向right的值被覆盖,垂直方向bottom的值被覆盖

* replaced
    1. 如果width为auto,width为元素本身的宽度
    2. left为auto,left为static position的边缘
    3. 如果left或right仍为auto,设置为auto的margin-left或margin-right置为0
    4. margin-left和margin-right都为auto,两个的值设为相等的
    5. 如果还有一个属性为auto,则把containing block长度与当前元素长度的差值赋给它


####fixed
与设置为absolute的表现相同

**containing block**:viewport
