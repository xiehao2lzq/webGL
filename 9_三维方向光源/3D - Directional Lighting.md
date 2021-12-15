##　三维方向光源

https://webglfundamentals.org/webgl/lessons/zh_cn/webgl-3d-lighting-directional.html


方向光是指光照均匀地来自某一个方向，晴朗天气下的太阳经常被当作方向光源， 它距离太远所以光线被看作是平行的照到地面上。

计算方向光非常简单，将方向光的方向和面的朝向点乘就可以得到两个方向的余弦值。

如果两点方向刚好相反，点乘结果则为 -1。 如果方向相同结果为 1。

这有什么用呢？如果将三维物体的朝向和光的方向点乘， 结果为 1 则物体朝向和光照方向相同，为 -1 则物体朝向和光照方向相反。

我们可以将颜色值和点乘结果相乘，BOOM！有光了！

##　详见 demo1


你旋转了 F 就会发现，F 虽然旋转了但是光照没变， 我们希望随着 F 的旋转正面总是被照亮的。

为了解决这个问题就需要在物体重定向时重定向法向量， 和位置一样我们也可以将向量和矩阵相乘，这个矩阵显然是 world 矩阵， 现在我们只传了一个矩阵 u_matrix，所以先来改成传递两个矩阵， 一个叫做 u_world 的世界矩阵，另一个叫做 u_worldViewProjection 也就是我们现在的 u_matrix。

attribute vec4 a_position;
attribute vec3 a_normal;
 
uniform mat4 u_worldViewProjection;
uniform mat4 u_world;
 
varying vec3 v_normal;
 
void main() {
  // 将位置和矩阵相乘
  gl_Position = u_worldViewProjection * a_position;
 
  // 重定向法向量并传递给片断着色器
  v_normal = mat3(u_world) * a_normal;
}
注意到我们将 a_normal 与 mat3(u_world) 相乘， 那是因为法向量是方向所以不用关心位移， 矩阵的左上 3x3 部分才是控制姿态的。

找到全局变量

  // 寻找全局变量
  var worldViewProjectionLocation =
      gl.getUniformLocation(program, "u_worldViewProjection");
  var worldLocation = gl.getUniformLocation(program, "u_world");
然后更新它们

// 设置矩阵
gl.uniformMatrix4fv(
    worldViewProjectionLocation, false,
    worldViewProjectionMatrix);
gl.uniformMatrix4fv(worldLocation, false, worldMatrix);
结果

## 详见demo2


我们用 normal 和 u_world 相乘去重定向法向量， 如果世界矩阵被缩放了怎么办？事实是会得到错误的法向量。

解决办法就是对世界矩阵求逆并转置， 用这个矩阵就会得到正确的结果。

## 详见demo3

## 扩展
mat3(u_worldInverseTranspose) * a_normal 的可选方案
之前的着色器中出现了这样的代码

v_normal = mat3(u_worldInverseTranspose) * a_normal;
我们也可以这样做

v_normal = (u_worldInverseTranspose * vec4(a_normal, 0)).xyz;
由于 w 在相乘前被赋值为 0，所以在相乘后负责平移的部分与 0 相乘被移除了。 我认为这可能是更常用的方式，mat3 的做法只是对我来说更简洁但我经常用前一种方法。

当然另一种解决方案是将u_worldInverseTranspose直接构造成mat3。 这有两个不能这么做的理由，第一个是我们可能需要一个完整的 u_worldInverseTranspose 对象传递一个mat4给还要给其他地方使用，另一个是我们在JavaScript中的所有矩阵方法都是针对 4x4 的矩阵，如果没有其他特别的需求，没有必要构造一个3x3的矩阵，或者是把 4x4 转换成 3x3 。