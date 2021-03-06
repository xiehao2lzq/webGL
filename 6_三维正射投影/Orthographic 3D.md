## 三维正射投影

https://webglfundamentals.org/webgl/lessons/zh_cn/webgl-3d-orthographic.html

上一篇文章概述了二维矩阵的工作原理，我们讲到了如何平移， 旋转，缩放甚至从像素空间投影到裁剪空间，并且将这些操作通过一个矩阵实现， 做三维只需要再迈出一小步。

二维例子中的二维点 (x, y) 与 3x3 的矩阵相乘， 在三维中我们需要三维点 (x, y, z) 与 4x4 的矩阵相乘。

WebGL中的三角形有正反面的概念，正面三角形的顶点顺序是逆时针方向， 反面三角形是顺时针方向。



WebGL可以只绘制正面或反面三角形，可以这样开启

  gl.enable(gl.CULL_FACE);
将它放在 drawScene 方法里，开启这个特性后WebGL默认“剔除”背面三角形， "剔除"在这里是“不用绘制”的花哨叫法。

对于WebGL而言，一个三角形是顺时针还是逆时针是根据裁剪空间中的顶点顺序判断的， 换句话说，WebGL是根据你在顶点着色器中运算后提供的结果来判定的， 这就意味着如果你把一个顺时针的三角形沿 X 轴缩放 -1 ，它将会变成逆时针， 或者将顺时针的三角形旋转180度后变成逆时针。由于我们没有开启 CULL_FACE， 所以可以同时看到顺时针（正面）和逆时针（反面）三角形。现在开启了， 任何时候正面三角形无论是缩放还是旋转的原因导致翻转了，WebGL就不会绘制它。 这件事很有用，因为通常情况下你只需要看到你正面对的面。

接触 DEPTH BUFFER（深度缓冲）。

深度缓冲有时也叫 Z-Buffer，是一个存储像素深度的矩形， 一个深度像素对应一个着色像素，在绘制图像时组合使用。 当WebGL绘制每个着色像素时也会写入深度像素， 它的值基于顶点着色器返回的Z值，就像我们将 X 和 Y 转换到裁剪空间一样， Z 也在裁剪空间或者 (-1 到 +1) 。这个值会被转换到深度空间( 0 到 +1)， WebGL绘制一个着色像素之前会检查对应的深度像素， 如果对应的深度像素中的深度值小于当前像素的深度值，WebGL就不会绘制新的颜色。 反之它会绘制片断着色器提供的新颜色并更新深度像素中的深度值。 这也意味着在其他像素后面的像素不会被绘制。

我们可以像这样开启这个特性

  gl.enable(gl.DEPTH_TEST);
在开始绘制前还需要清除深度缓冲为 1.0 。

  // 绘制场景
  function drawScene() {
    ...
 
    // 清空画布和深度缓冲
    gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
 
    ...