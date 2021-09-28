### 材质系统

Note:创建新的材质会增加着色器的编译时间，所以用现有的材质的材质实例来替代重新创建材质。

### 材质编辑器面板属性：
**Pixel Depth Offset**:
像素深度偏移，利用设置的逻辑控制像素深度，根据对象的场景深度来混合或者淡化对象。
应用：配合DitherTemporalAA实现地表和石头相交的部分混合
![image](https://docs.unrealengine.com/4.27/Images/RenderingAndGraphics/Materials/MaterialInputs/PDO_0.webp)
![image](https://docs.unrealengine.com/4.27/Images/RenderingAndGraphics/Materials/MaterialInputs/PDO_1.webp)
**World Position Offset**：


### 材质函数

**DitherTemporalAA:**


### 材质节点种类

内置输入节点：来自上一个阶段的输出数据 或者 来自引擎内置的uniform Parameter(Time 节点)
内置函数和运算符节点：HLSL原生函数 或者usf定义的标准函数库

### 材质节点

**absolute world position** : 

着色器当前渲染的像素的世界空间中的位置
https://forums.unrealengine.com/t/absolute-world-position-what-exactly-is-it-anyway/115370/5
从摄像机向场景中的像素发射光线，absolute world position 就是被这条光线击中的点

https://www.zhihu.com/question/382810691/answer/1543325159
返回当前二维屏幕像素块对应的模型在三维世界当中的坐标。

**VertexNormalWS**：

顶点法线在世界空间下的位置。
它只能用于在顶点着色器中执行的材质输入，例如 WorldPositionOffset  这对于网格的放大和缩小很有用。沿着法线方向偏移位置会导致几何体沿uv接缝分开。

**Object Position**

输出**对象边界**的世界场景空间中心位置
应用：WorldPosition - object position = local position;

**ActorPosition**

输出一个Vector3（RGB）的数据，表示对象在世界空间下的坐标

**Camera Position**

输出一个三通道的Vector,该值表示摄像机在世界空间中的位置

**Camera Vector**

输出一个三通道的Vector,表示像素到摄像机的方向

**PixelNormalWS**

根据当前法线输出矢量数据，表示像素所面对的方向

**自发光输入： emissive color**

https://docs.unrealengine.com/4.26/en-US/RenderingAndGraphics/Materials/HowTo/EmissiveGlow/

**vertexColor**:

模型顶点颜色

**Screen Position**

当前渲染像素在屏幕空间位置

**Dynamic Parameter**

粒子动态参数，将4个浮点数通过粒子系统的Dynamic模块中对应参数的值传递到材质，控制材质的变化

**Particle Color**

通过粒子系统传入的例子颜色。
### 表达式

**Frac**:
1.当Num>0, 返回Num小数部分
2.当Num < 0, 返回的则是 Num - floor(Num) 例子： Num= -1.1, -1.1 - floor(-1.1) = -1.1 + 2 = 0.9;

**Desaturation**

去饱和度（Desaturation） 表达式对其输入进行去饱和度，即根据特定百分比将其输入的颜色转换为灰色阴影
参数：
Luminance Factors: 亮度系数，指定每个通道去饱和度颜色的影响量，此属性确保去饱和度后 绿色比红色亮，红色比蓝色亮
Fraction： 要应用于输入的去饱和度数量，表示百分比（完全原始颜色到 完全去饱和度）

**DepthFade**

DepthFade用来表示半透明物体和不透明物体相交部分出现的接缝。

Opacity:接收深度消退前的当前不透明度
FadeDistance 消退距离，应该发生消退的全局空间距离
//源码中该表达式的公式为：
Result = Opacity * saturate((SceneDepth - PixelDepth)/ max(FadeDistance, DELTA))//DELTA = 0.00001f