---
title: 利用 taup_pierce 计算射线穿透点
date: 2014-11-08
author: SeisMan
categories:
  - 地震学软件
tags:
  - 射线
  - TauP
slug: taup-calculate-pierce-points
---

`taup_pierce` 是 TauP 提供的一个命令行工具，用于计算各种震相在不同深度的穿透点信息。
本文通过几个例子介绍一个 `taup_pierce` 的使用方法。

<!--more-->

下面的所有例子使用如下参数：

-   地球模型：PREM 模型
-   震源深度：200 km
-   地震位置为（0,0）
-   台站位置为（15,15）
-   震中距为 21.025 度
-   方位角为 44.199；
-   反方位角为 226.172

在已知如上参数的前提下，计算内核边界的反射波 PKiKP 震相在不同深度的穿透点信息；

## 震相在不同深度穿透对应的震中距

本例中使用了地震深度和震中距信息，其输出有三列，分别为不同深度处的 ` 震中距 ` 、 ` 深度 ` 和 ` 震相走时 `，
这里的深度是地球模型的主要间断面，比如常见到的 410 间断、660 间断、CMB（2891）和 ICB（5150）。

    $ taup_pierce -ph PKiKP -mod prem -h 200 -deg 21.024
    > PKiKP at 971.23 seconds at 21.02 degrees for a 200.0 km deep source in the prem model with rayParam 0.469 s/deg.
        0.00   200.0     0.0
        0.01   220.0     2.5
        0.07   400.0    23.1
        0.19   670.0    50.8
        2.39  2891.0   230.0
       10.48  5149.5   472.7
       18.57  2891.0   715.5
       20.77   670.0   894.7
       20.89   400.0   922.3
       20.96   220.0   942.9
       20.96   200.0   945.4
       21.02    24.4   967.3
       21.02    15.0   968.6
       21.02     0.0   971.2

从输出中可以看到，PKiKP 震相在 2.39 度处穿过核幔边界进入外核，在 10.48 度处于内核边界处反射，并于 18.57 度时再次穿过核幔边界回到地幔。

## 震相在不同深度处的穿透点位置

有时候不仅仅想要知道震相在某个深度穿透所走过的距离，还想精确的知道是在哪个位置穿透点的。

本例使用事件和台站的经纬度信息，输出为 ` 震中距 ` 、 ` 深度 ` 、 ` 走时 ` 以及
穿透点的 ` 纬度 ` 和 ` 经度 ` 。

    $ taup_pierce -ph PKiKP -mod prem -h 200 -evt 0 0 -sta 15 15
    > PKiKP at 971.26 seconds at 21.09 degrees for a 200.0 km deep source in the prem model with rayParam 0.470 s/deg.
        0.00   200.0     0.0      0.00      0.00
        0.01   220.0     2.5      0.00      0.00
        0.07   400.0    23.1      0.05      0.05
        0.19   670.0    50.8      0.14      0.13
        2.40  2891.0   230.0      1.72      1.67
       10.51  5149.5   472.7      7.54      7.35
       18.63  2891.0   715.5     13.29     13.18
       20.84   670.0   894.7     14.82     14.81
       20.96   400.0   922.3     14.91     14.90
       21.02   220.0   943.0     14.95     14.95
       21.03   200.0   945.5     14.96     14.95
       21.08    24.4   967.3     15.00     15.00
       21.09    15.0   968.7     15.00     15.00
       21.09     0.0   971.3     15.00     15.00

从输出可以看到，PKiKP 震相在 CMB 的入射点位于 (1.67,1.72) 处，出射点位于
(13.18, 13.29)处，在 ICB 的反射点位于 (7.35, 7.54) 处。

还可以用事件位置、震中距和方位角得到同样的结果:

    taup_pierce -ph PKiKP -mod prem -h 200 -evt 0 0 -deg 21.025 -az 44.199

或者用台站位置、震中距、反方位角得到同样的结果:

    taup_pierce -ph PKiKP -mod prem -h 200 -sta 15 15 -deg 21.025 -baz 226.172

## 获取反射点

穿透点分为两种，一种是反射点，另一种是真正意义上的穿透点。比如 PKiKP 震相在 ICB 上是反射点，在 CMB 上只能称为穿透点。

反射点又分为两种，bottom turning points 和 underside reflection points，可以形象的表示为 `v` 和 `^` 。

获取 bottom turing points:

    $ taup_pierce -ph PKiKP -mod prem -h 200 -evt 0 0 -sta 15 15 -turn
    > PKiKP at 971.26 seconds at 21.09 degrees for a 200.0 km deep source in the prem model with rayParam 0.470 s/deg.
       10.51  5149.5   472.7      7.54      7.35

获取 underside reflection points（这里以 sScS 为例）:

    $ taup_pierce -ph sScS -mod prem -h 200 -evt 0 0 -sta 15 15 -under
    > sScS at  1020.00 seconds at    21.09 degrees for a    200.0 km deep source in the prem model with rayParam    3.495 s/deg.
        0.26     0.0    46.9      0.18      0.18
       21.09     0.0  1020.0     15.00     15.00

当然也可以用 `-rev` 选项获取所有反射点。

## 获取特定深度的穿透点位置

默认只输出地球模型主要间断面处的穿透点信息，有些时候会需要震相在特定深度处的穿透点信息，比如深度为 5000km 处:

    $ taup_pierce -ph PKiKP -mod prem -h 200 -evt 0 0 -sta 15 15 -pierce 5000
    > PKiKP at   971.26 seconds at    21.09 degrees for a    200.0 km deep source in the prem model with rayParam    0.470 s/deg.
        0.00   200.0     0.0      0.00      0.00
        0.01   220.0     2.5      0.00      0.00
        0.07   400.0    23.1      0.05      0.05
        0.19   670.0    50.8      0.14      0.13
        2.40  2891.0   230.0      1.72      1.67
        9.06  5000.0   457.9      6.50      6.32
       10.51  5149.5   472.7      7.54      7.35
       11.97  5000.0   487.6      8.58      8.38
       18.63  2891.0   715.5     13.29     13.18
       20.84   670.0   894.7     14.82     14.81
       20.96   400.0   922.3     14.91     14.90
       21.02   220.0   943.0     14.95     14.95
       21.03   200.0   945.5     14.96     14.95
       21.08    24.4   967.3     15.00     15.00
       21.09    15.0   968.7     15.00     15.00
       21.09     0.0   971.3     15.00     15.00

在输出中多了两个 5000km 处的穿透点信息，有时候只想要这个深度的信息，而不需要其他深度的穿透点信息，此时可以使用 `-nodiscon` 选项:

    $ taup_pierce -ph PKiKP -mod prem -h 200 -evt 0 0 -sta 15 15 -pierce 5000 -nodiscon
    > PKiKP at   971.26 seconds at    21.09 degrees for a    200.0 km deep source in the prem model with rayParam    0.470 s/deg.
        9.06  5000.0   457.9      6.50      6.32
       11.97  5000.0   487.6      8.58      8.38