## 图像处理

在WebGL中绘制图片需要使用纹理。和WebGL渲染时需要裁剪空间坐标相似， 渲染纹理时需要纹理坐标，而不是像素坐标。无论纹理是什么尺寸，纹理坐标范围始终是 0.0 到 1.0 。

因为我们只用画一个矩形（其实是两个三角形），所以需要告诉WebGL矩形中每个顶点对应的纹理坐标。 我们将使用一种特殊的叫做'varying'的变量将纹理坐标从顶点着色器传到片断着色器，它叫做“可变量” 是因为它的值有很多个，WebGL会用顶点着色器中值的进行插值，然后传给对应像素执行的片断着色器。

为什么u_image没有设置还能正常运行？
全局变量默认为 0 所以 u_image 默认使用纹理单元 0 。 纹理单元 0 默认为当前活跃纹理，所以调用 bindTexture 会将纹理绑定到单元 0 。

WebGL有一个纹理单元队列，每个sampler全局变量的值对应着一个纹理单元， 它会从对应的单元寻找纹理数据，你可以将纹理设置到你想用的纹理单元。

例如：

var textureUnitIndex = 6; // 用单元 6.
var u_imageLoc = gl.getUniformLocation(
    program, "u_image");
gl.uniform1i(u_imageLoc, textureUnitIndex);
为了将纹理设置在不同的单元你可以调用gl.activeTexture。 例如

// 绑定纹理到单元 6
gl.activeTexture(gl.TEXTURE6);
gl.bindTexture(gl.TEXTURE_2D, someTexture);
这样也可以

var textureUnitIndex = 6; // 使用纹理单元 6
// 绑定纹理到单元 6
gl.activeTexture(gl.TEXTURE0 + textureUnitIndex);
gl.bindTexture(gl.TEXTURE_2D, someTexture);
所有支持WebGL的环境，在片断着色器中至少有8个纹理单元，顶点着色器中可以是0个。 所以如果你使用超过8个纹理单元就应该调用gl.getParameter(gl.MAX_TEXTURE_IMAGE_UNITS) 查看单元个数，或者调用gl.getParameter(gl.MAX_VERTEX_TEXTURE_IMAGE_UNITS) 查看顶点着色器中可以用几个纹理单元。超过 99% 的机器在顶点着色器中至少有4个纹理单元。

在GLSL中为什么变量的前缀都是 a_, u_ 或 v_ ？
那只是一个命名约定，不是强制要求的。 但是对我来说可以轻松通过名字知道值从哪里来，a_ 代表属性，值从缓冲中提供； u_ 代表全局变量，直接对着色器设置；v_ 代表可变量，是从顶点着色器的顶点中插值来出来的