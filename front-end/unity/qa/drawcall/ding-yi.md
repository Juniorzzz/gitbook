# 定义

在unity中，每次CPU准备数据并通知GPU的过程就称之为一个DrawCall。

具体过程就是：设置颜色-->绘图方式-->顶点坐标-->绘制-->结束，所以在绘制过程中，如果能在一次DrawCall完成所有绘制就会大大提高运行效率，进而达到优化的目的。
