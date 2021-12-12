## WebGL工作原理

https://webglfundamentals.org/webgl/lessons/zh_cn/webgl-how-it-works.html

WebGL每次绘制需要两个着色器， 一个顶点着色器和一个片断着色器，每一个着色器都是一个方法。 一个顶点着色器和一个片断着色器链接在一起放入一个着色程序中（或者只叫程序）。 一个典型的WebGL应用会有多个着色程序。

WebGL在GPU上的工作基本上分为两部分，第一部分是将顶点（或数据流）转换到裁剪空间坐标， 第二部分是基于第一部分的结果绘制像素点。
当你调用
var primitiveType = gl.TRIANGLES;
var offset = 0;
var count = 9;
gl.drawArrays(primitiveType, offset, count);
这里的9表示“处理9个顶点”，所以将会有9个顶点被转换。

vertexAttribPointer 中的 normalizeFlag 参数是什么意思？
标准化标记（normalizeFlag）适用于所有非浮点型数据。如果传递false就解读原数据类型。 BYTE 类型的范围是从 -128 到 127，UNSIGNED_BYTE 类型的范围是从 0 到 255， SHORT 类型的范围是从 -32768 到 32767，等等...

如果标准化标记设为true，BYTE 数据的值(-128 to 127)将会转换到 -1.0 到 +1.0 之间， UNSIGNED_BYTE (0 to 255) 变为 0.0 到 +1.0 之间，SHORT 也是转换到 -1.0 到 +1.0 之间， 但比 BYTE 精确度高。

最常用的是标准化颜色数据。大多数情况颜色值范围为 0.0 到 +1.0。 使用4个浮点型数据存储红，绿，蓝和阿尔法通道数据时，每个顶点的颜色将会占用16字节空间， 如果你有复杂的几何体将会占用很多内存。代替的做法是将颜色数据转换为四个 UNSIGNED_BYTE ， 其中 0 表示 0.0，255 表示 1.0。现在每个顶点只需要四个字节存储颜色值，省了 75% 空间。