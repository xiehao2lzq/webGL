## 三位聚光灯

实现原理和点光源类似
详见 https://webglfundamentals.org/webgl/lessons/zh_cn/webgl-3d-lighting-spot.html

把点光源想象成一个点，光线从那个点照向所有方向。 实现聚光灯只需要以那个点为起点选择一个方向，作为聚光灯的方向， 然后将其他光线方向与所选方向点乘，然后随意选择一个限定范围， 然后判断光线是否在限定范围内，如果不在就不照亮。

指定一个方向方向表示聚光灯的方向，选择一个限定（上方以度为单位）。 通过限定我们计算一个点乘限定，只需对限定值取余弦就可以得到。如果与选定聚光灯方向的点乘大于这个点乘限定，就照亮，否则不照亮。

换一种方式解释，假设限定是 20 度，我们可以将它转换为弧度，然后取余弦值得到 -1 到 1 之间的数， 暂且把它称作点乘空间。或者可以用这个表格表示限定值的转换。

           限制值
   角度   |   弧度   | 点乘空间
 --------+---------+----------
    0    |   0.0   |    1.0
    22   |    .38  |     .93
    45   |    .79  |     .71
    67   |   1.17  |     .39
    90   |   1.57  |    0.0
   180   |   3.14  |   -1.0