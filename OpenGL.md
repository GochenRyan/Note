# 入门

**图形渲染管线的各个阶段：**

![20190805173025-pipeline.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190805173025-pipeline.png?token=ADTLZTUOBZ3GYEPTM3HYH425I73W4)

**VBO**

顶点缓冲对象(Vertex Buffer Objects)。显卡高速显存的缓冲区，用来保存顶点的信息。

**生成**

```
unsigned int VBO;
glGenBuffers(1, &VBO);
```

**绑定**

```
glBindBuffer(GL_ARRAY_BUFFER, VBO);  
```

**把顶点数据复制到缓冲的内存中**

```
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

glBufferData是一个专门用来把用户定义的数据复制到当前绑定缓冲的函数。

* 第一个参数是目标缓冲的类型：顶点缓冲对象当前绑定到GL_ARRAY_BUFFER目标上。

* 第二个参数指定传输数据的大小(以字节为单位)；用一个简单的`sizeof`计算出顶点数据大小就行。

* 第三个参数是我们希望发送的实际数据。

* 第四个参数指定了我们希望显卡如何管理给定的数据。
  
  * GL_STATIC_DRAW ：数据不会或几乎不会改变。
  
  * GL_DYNAMIC_DRAW：数据会被改变很多。
  
  * GL_STREAM_DRAW ：数据每次绘制时都会改变。

# 光照

## 颜色

颜色可以数字化的由红色(Red)、绿色(Green)和蓝色(Blue)三个分量组成，它们通常被缩写为RGB。

我们在现实生活中看到某一物体的颜色并不是这个物体真正拥有的颜色，而是它所反射的(Reflected)颜色。换句话说，那些不能被物体所吸收(Absorb)的颜色（被拒绝的颜色）就是我们能够感知到的物体的颜色。

物体的颜色为物体从一个光源反射各个颜色分量的大小。

## 基础光照

冯氏光照模型的主要结构由3个分量组成：环境(Ambient)、漫反射(Diffuse)和镜面(Specular)光照。

* 环境光照(Ambient Lighting)：即使在黑暗的情况下，世界上通常也仍然有一些光亮（月亮、远处的光），所以物体几乎永远不会是完全黑暗的。为了模拟这个，我们会使用一个环境光照常量，它永远会给物体一些颜色。

* 漫反射光照(Diffuse Lighting)：模拟光源对物体的方向性影响(Directional Impact)。它是冯氏光照模型中视觉上最显著的分量。物体的某一部分越是正对着光源，它就会越亮。

* 镜面光照(Specular Lighting)：模拟有光泽物体上面出现的亮点。镜面光照的颜色相比于物体的颜色会更倾向于光的颜色。

### 环境光照

镜面光照(Specular Lighting)：模拟有光泽物体上面出现的亮点。镜面光照的颜色相比于物体的颜色会更倾向于光的颜色。

```

void main()

{

float ambientStrength = 0.1;

vec3 ambient = ambientStrength * lightColor;



vec3 result = ambient * objectColor;

FragColor = vec4(result, 1.0);

}
```

### 漫反射光照

漫反射光照使物体上与光线方向越接近的片段能从光源处获得更多的亮度。

计算漫反射光照需要：

* 法向量：一个垂直于顶点表面的向量。

* 定向的光线：作为光源的位置与片段的位置之间向量差的方向向量。为了计算这个光线，我们需要光的位置向量和片段的位置向量。

**法向量：** 法向量是一个垂直于顶点表面的（单位）向量。由于顶点本身并没有表面（它只是空间中一个独立的点），我们利用它周围的顶点来计算出这个顶点的表面。

可以用这三个分量来定义一个材质颜色(Material Color)：环境光照(Ambient Lighting)、漫反射光照(Diffuse Lighting)和镜面光照(Specular Lighting)，再添加反光度(Shininess)这个分量到上述的三个颜色中，就有需要的所有材质属性了

## 光照贴图

### 漫反射贴图

它是一个表现了物体所有的漫反射颜色的纹理图像

![20190805212039-container2.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190805212039-container2.png?token=ADTLZTWH4V3EBRJFRITKFCC5JAWWW)

`sampler2D`是所谓的不透明类型(Opaque Type)，也就是说我们不能将它实例化，只能通过uniform来定义它。如果我们使用除uniform以外的方法（比如函数的参数）实例化这个结构体，GLSL会抛出一些奇怪的错误。这同样也适用于任何封装了不透明类型的结构体。

### 镜面光贴图

镜面光贴图是一个黑白的（如果你想得话也可以是彩色的）纹理，来定义物体每部分的镜面光强度的纹理图像

镜面高光的强度可以通过图像每个像素的亮度来获取。镜面光贴图上的每个像素都可以由一个颜色向量来表示，比如说黑色代表颜色向量`vec3(0.0)`，灰色代表颜色向量`vec3(0.5)`。在片段着色器中，我们接下来会取样对应的颜色值并将它乘以光源的镜面强度。一个像素越「白」，乘积就会越大，物体的镜面光分量就会越亮。

![20190805212045-container2_specular.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190805212045-container2_specular.png?token=ADTLZTSSBMW4EKN2QKEWM7S5JAWXE)ular.png)

## 放射光贴图

放射光贴图是一个储存了每个片段的发光值(Emission Value)的贴图。发光值是一个包含（假设）光源的物体发光(Emit)时可能显现的颜色，这样的话物体就能够忽略光照条件进行发光(Glow)。

![20190808202411-matrix.jpg](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190808202411-matrix.jpg)

# 投光物

将光**投射**(Cast)到物体的光源叫做投光物(Light Caster)

## 平行光

**定向光**：当我们使用一个假设光源处于**无限**远处的模型时，它就被称为定向光，因为它的所有光线都有着相同的方向，它与光源的位置是没有关系的。

![20190809141327-image.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190809141327-image.png)

定义一个光线方向向量而不是位置向量来模拟一个定向光。着色器的计算基本保持不变，但这次将直接使用光的direction向量而不是通过direction来计算lightDir向量。

```
struct Light {
    // vec3 position; // 使用定向光就不再需要了
    vec3 direction;

    vec3 ambient;
    vec3 diffuse;
    vec3 specular;
};
...
void main()
{
  vec3 lightDir = normalize(-light.direction);
  ...
}
```

## 点光源

点光源是处于世界中某一个位置的光源，它会朝着所有方向发光，但光线会随着距离逐渐衰减。想象作为投光物的灯泡和火把，它们都是点光源。

### 衰减

随着光线传播距离的增长逐渐削减光的强度通常叫做衰减(Attenuation)。

随距离减少光强度的一种方式是使用一个线性方程，这样的方程能够随着距离的增长线性地减少光的强度，从而让远处的物体更暗。然而，这样的线性方程通常会看起来比较假。在现实世界中，灯在近处通常会非常亮，但随着距离的增加光源的亮度一开始会下降非常快，但在远处时剩余的光强度就会下降的非常缓慢了。

下面这个公式根据片段距光源的距离计算了衰减值，之后我们会将它乘以光的强度向量：

$$
F_{att} = \frac{1.0}{K_{c}+K_{l}*d+K_{q}*d^{2}}
$$

在这里$d$代表了片段距光源的距离。接下来为了计算衰减值，我们定义3个（可配置的）项：常数项$K_{c}$、一次项$K_{l}$和二次项$K_{q}$。

- 常数项通常保持为1.0，它的主要作用是保证分母永远不会比1小，否则的话在某些距离上它反而会增加强度，这肯定不是我们想要的效果。
- 一次项会与距离值相乘，以线性的方式减少强度。
- 二次项会与距离的平方相乘，让光源以二次递减的方式减少强度。二次项在距离比较小的时候影响会比一次项小很多，但当距离值比较大的时候它就会比一次项更大了。

下面这张图显示了在100的距离内衰减的效果：

![20190809160412-image.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190809160412-image.png)

[选择正确的值](http://wiki.ogre3d.org/tiki-index.php?page=-Point+Light+Attenuation)

实现衰减

```
struct Light {
    vec3 position;  

    vec3 ambient;
    vec3 diffuse;
    vec3 specular;

    float constant;
    float linear;
    float quadratic;
};
...
float distance    = length(light.position - FragPos);float attenuation = 1.0 / (light.constant + light.linear * distance +                 light.quadratic * (distance * distance));
...
ambient  *= attenuation; 
diffuse  *= attenuation;
specular *= attenuation;
```

## 聚光

聚光是位于环境中某个位置的光源，它只朝一个特定方向而不是所有方向照射光线。这样的结果就是只有在聚光方向的特定半径内的物体才会被照亮，其它的物体都会保持黑暗。聚光很好的例子就是路灯或手电筒。

OpenGL中聚光是用一个世界空间位置、一个方向和一个切光角(Cutoff Angle)来表示的。

![20190809171219-image.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190809171219-image.png)

- `LightDir`：从片段指向光源的向量。
- `SpotDir`：聚光所指向的方向。
- `Phi`$\phi$：指定了聚光半径的切光角。落在这个角度之外的物体都不会被这个聚光所照亮。
- `Theta`$\theta$：LightDir向量和SpotDir向量之间的夹角。在聚光内部的话$\theta$值应该比$\phi$值小。

所以我们要做的就是计算LightDir向量和SpotDir向量之间的点积，并将它与切光角$\phi$值对比。

### 手电筒

手电筒(Flashlight)是一个位于观察者位置的聚光，通常它都会瞄准玩家视角的正前方。基本上说，手电筒就是普通的聚光，但它的位置和方向会随着玩家的位置和朝向不断更新。

在片段着色器中我们需要的值有聚光的位置向量（来计算光的方向向量）、聚光的方向向量和一个切光角。

```
struct Light {
    vec3  position;
    vec3  direction;
    float cutOff;
    ...
};
...
lightingShader.setVec3("light.position",  camera.Position);
lightingShader.setVec3("light.direction", camera.Front);
lightingShader.setFloat("light.cutOff",   glm::cos(glm::radians(12.5f)));
```

### 平滑/软化边缘

为了创建一种看起来边缘平滑的聚光，需要模拟聚光有一个内圆锥(Inner Cone)和一个外圆锥(Outer Cone)。将内圆锥设置为上一部分中的那个圆锥，也需要一个外圆锥，来让光从内圆锥逐渐减暗，直到外圆锥的边界。

为了创建一个外圆锥，只需要再定义一个余弦值来代表聚光方向向量和外圆锥向量（等于它的半径）的夹角。然后，如果一个片段处于内外圆锥之间，将会给它计算出一个0.0到1.0之间的强度值。如果片段在内圆锥之内，它的强度就是1.0，如果在外圆锥之外强度值就是0.0。

可以用下面这个公式来计算这个值：

$$
I = \frac{\theta - \gamma}{\epsilon}
$$

这里$\epsilon$(Epsilon)是内（$\phi$）和外圆锥（$\gamma$）之间的余弦值差。最终的II值就是在当前片段聚光的强度。

现在有了一个在聚光外是负的，在内圆锥内大于1.0的，在边缘处于两者之间的强度值了。如果正确地约束(Clamp)这个值，在片段着色器中就不再需要`if-else`了，能够使用计算出来的强度值直接乘以光照分量：

```
float theta     = dot(lightDir, normalize(-light.direction));
float epsilon   = light.cutOff - light.outerCutOff;
float intensity = clamp((theta - light.outerCutOff) / epsilon, 0.0, 1.0);    
...
// 将不对环境光做出影响，让它总是能有一点光
diffuse  *= intensity;
specular *= intensity;
...
```
