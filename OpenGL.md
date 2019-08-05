# 入门

**图形渲染管线的各个阶段：**

![20190805173025-pipeline.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/20190805173025-pipeline.png?token=ADTLZTUOBZ3GYEPTM3HYH425I73W4)

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
