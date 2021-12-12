## GLSL
GLSL全称是 Graphics Library Shader Language （图形库着色器语言），是着色器使用的语言。 它有一些不同于JavaScript的特性，主要目的是为栅格化图形提供常用的计算功能。 所以它内建的数据类型例如vec2, vec3和 vec4分别代表两个值，三个值和四个值， 类似的还有mat2, mat3 和 mat4 分别代表 2x2, 3x3 和 4x4 矩阵。 你可以做一些运算例如常量和矢量的乘法。

vec4 a = vec4(1, 2, 3, 4);
vec4 b = a * 2.0;
// b 现在是 vec4(2, 4, 6, 8);
它同样可以做矩阵乘法以及矢量和矩阵的乘法

mat4 a = ???
mat4 b = ???
mat4 c = a * b;
 
vec4 v = ???
vec4 y = c * v;
他还为矢量数据提供多种分量选择器，例如 vec4

vec4 v;
v.x 和 v.s 以及 v.r ， v[0] 表达的是同一个分量。
v.y 和 v.t 以及 v.g ， v[1] 表达的是同一个分量。
v.z 和 v.p 以及 v.b ， v[2] 表达的是同一个分量。
v.w 和 v.q 以及 v.a ， v[3] 表达的是同一个分量。
它还支持矢量调制，意味者你可以交换或重复分量。

v.yyyy
和

vec4(v.y, v.y, v.y, v.y)
是一样的

同样的

v.bgra
和

vec4(v.b, v.g, v.r, v.a)
等价

当构造一个矢量或矩阵时可以一次提供多个分量，例如

vec4(v.rgb, 1)
和

vec4(v.r, v.g, v.b, 1)
是一样的

同样

vec4(1)
和

vec4(1, 1, 1, 1)
相同

值得注意的是GLSL是一个强类型的语言。

float f = 1;  // 错误，1是int类型，不能将int型赋值给float
正确的方式是

float f = 1.0;      // 使用float
float f = float(1)  // 转换integer为float
上例中 vec4(v.rgb, 1) 不会因为 1 报错，因为 vec4 内部进行了转换类似 float(1) 。

GLSL有一系列内置方法，其中大多数运算支持多种数据类型，并且一次可以运算多个分量，例如

T sin(T angle)
T可以是 float, vec2, vec3 或 vec4 。如果你传的是 vec4 返回的也是 vec4, 返回结果对应每个分量的正弦值。换句话说如果 v 是 vec4 类型。那么

vec4 s = sin(v);
和

vec4 s = vec4(sin(v.x), sin(v.y), sin(v.z), sin(v.w));
是一样的

有时一个参数是浮点型而剩下的都是 T ，意思是那个浮点数据会作为所有其他参数的一个新分量。 例如如果 v1 和 v2 是 vec4 同时 f 是浮点型，那么

vec4 m = mix(v1, v2, f);
和

vec4 m = vec4(
  mix(v1.x, v2.x, f),
  mix(v1.y, v2.y, f),
  mix(v1.z, v2.z, f),
  mix(v1.w, v2.w, f));
等价



这是当前系列文章的重点。WebGL的全部内容就是创建不同的着色器，向着色器提供数据然后调用gl.drawArrays 或 gl.drawElements 让WebGL调用当前顶点着色器处理每个顶点，调用当前片断着色器渲染每个像素。