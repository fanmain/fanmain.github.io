# 01_unity_math

# [UnityShader数学基础篇](https://www.cnblogs.com/feng-Ling/p/18146805 "发布于 2024-05-15 21:56")

### Mathf

#### Mathf和Math

1、Math是C#中封装好的用于数学计算的工具类，位于System命名空间中。  
2、Mathf是Unity中封装好的用于数学计算的工具结构体，位于UnityEngine命名空间中。

#### Mathf中的常用方法

**1.π - PI**  
`print(Mathf.PI);`  
**2.取绝对值 - Abs**

```scss
  print(Mathf.Abs(-10.5f));//10.5
  print(Mathf.Abs(-86));//86
```

**3.向上取整 - CeilToInt**

```scss
  print(Mathf.CeilToInt(1.001f));//2
  print(Mathf.CeilToInt(5.6f));//6
```

**4.向下取整 - FloorToInt**

```scss
  print(Mathf.FloorToInt(2.999f));//2
  print(Mathf.FloorToInt(1.04f));//1
```

**5.钳制函数 - Clamp (传入的数据 ，数据传出最小值 ，数据传出最大值)**

```dart
  int num = 18；
  print(Mathf.Clamp(num, 13, 32));//13
  print(Mathf.Clamp(num, 13, 32));//32
  print(Mathf.Clamp(num, 13, 32));//18
```

**6.获取最大值 - Max**

```go
  int[] ints = new int[5] {5,9,78,65,23};
  print(Mathf.Max(1, 5, 6, 8, 9, 45));//45
  print(Mathf.Max(ints));//78
```

**7.获取最小值 - Min**

```go
  int[] ints2 = new int[5] {-1,5,86,411,20};
  print(Mathf.Min(ints2));//-1
  print(Mathf.Min(1.2f,5,65,0.86f));//0.86
```

**8.一个数的n次幂 - Pow**

```go
  print("2的6次方" + Mathf.Pow(2, 6));//64
  print("3的4次方" + Mathf.Pow(3, 4));//81
```

**9.四舍五入 - RoundToInt**

```go
  print("四舍五入" + Mathf.RoundToInt(4.6f));//5
  print("四舍五入" + Mathf.RoundToInt(4.3f));//4
```

**10.返回一个数的平方根 - Sqrt**

```go
  print("平方根" + Mathf.Sqrt(4));//2
  print("平方根" + Mathf.Sqrt(9));//3
```

**11.判断一个数是否是2的n次方 - IsPowerOfTwo**

```bash
  print("11:" + Mathf.IsPowerOfTwo(8));//ture
  print("11:" + Mathf.IsPowerOfTwo(9));//false
```

**12.判断正负数 - Sign**

```go
  print("判断正负数" + Mathf.Sign(1));//1
  print("判断正负数" + Mathf.Sign(-2));//-1
```

**13.插值运算 - Lerp**  
1、Lerp函数公式：result = Mathf.Lerp(start, end, t);  
2、t为插值系数,取值范围为0~1：result = start + (end - start)\*t

```csharp
    float start = 0;
    float result = 0;
    float time = 0;

    void Update()
    {                
        //插值运算用法一
        //每帧改变start的值一变化速度先快后慢,位置无限接近,但是不会得到end位置
        start = Mathf.Lerp(start,10,Time.deltaTime);
        
        //插值运算用法二
        //每帧改变t的值一变化速度匀速,位置每帧result接近end,当t>=1时,得到结果
        time += Time.deltaTime;
        result = Mathf.Lerp(start,10,time);
    }
```

* * *

### 三角函数

Unity中都是弧度值，让物体曲线移动。

#### 弧度(radian)、角度相互转化

1、1 rad = (180/π)°=> 1 rad = (180/3.14)°= 57.3°  
2、弧度转角度：弧度 \* 57.3 = 对应的角度  
3、Mathf.Rad2Deg

```php
  float rad = 1;
  float anger = rad * Mathf.Rad2Deg;
  print(anger);//57.3
```

1、1°= (π/180)rad => 1°= (3.14/180)rad = 0.01745 rad  
2、角度转弧度：角度 \* 0.01745 = 对应的弧度  
3、Mathf.Deg2Rad

```lua
   anger = 1;
   rad = anger * Mathf.Deg2Rad;
   print(rad);//0.01745
```

#### 三角函数

**注意:**Mathf中的三角函数相关函数,传入的参数需要弧度值  
Sin() Cos()  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419162619973-70032263.png)

```scss
  print(Mathf.Sin(30 * Mathf.Deg2Rad));//1/2
  print(Mathf.Cos(30 * Mathf.Deg2Rad));//sqrt3/2
  print(Mathf.Sin(30f));//1/2
```

#### 反三角函数

**注意:**反三角函数得到的结果是正弦或者余弦值对应的弧度值  
Asin() Acos()

```scss
  print(Mathf.Asin(0.5f) * Mathf.Rad2Deg);//30
  print(Mathf.Acos(0.5f) * Mathf.Rad2Deg);//60
```

* * *

### Unity坐标系

#### 世界坐标系

```css
  transform.position
  transform.rotation
  transform.eulerAngles
  transform.lossyScale
```

#### 物体坐标系

1、相对父对象的物体坐标系的位置 本地坐标 相对坐标  
2、修改他们会是相对父对象物体坐标系的变化

```css
  transform.localPosition;
  transform.localRotation;
  transform.localEulerAngles;
  transform.localScale;
```

#### 屏幕坐标系

```mipsasm
  Input.mousePosition;
  Screen.width;
  Screen.height;
```

#### 坐标转换

```scss
  //世界转本地
  transform.InverseTransformDirection(Direction); //不受缩放影响（向量）
  transform.InverseTransformVector(Vector);       //受缩放影响
  transform.InverseTransformPoint(pos);

  //本地转世界
  transform.TransformDirection(localDirection);
  transform.TransformVector(localVector);
  transform.TransformPoint(localPos);

  //世界转屏幕
  Camera.main.WorldToScreenPoint(pos);
  //屏幕转世界
  Camera.main.ScreenToWorldPoint(ScreenPos);

  //世界转视口
  Camera.main.WorldToViewportPoint(pos);
  //视口转世界
  Camera.main.ViewportToWorldPoint(ViewportPos);

  //视口转屏幕
  Camera.main.ViewportToScreenPoint(ViewportPos);
  //屏幕转视口
  Camera.main.ScreenToViewportPoint(ScreenPos);
```

* * *

### 向量

1、Vector3这边变量 可以表示一个点 也可以表示一个向量 具体表示什么 是根据我们的具体需求和逻辑决定。  
2、如何在Unity里面得到向量，终点减起点，就可以得到向量。点C也可以代表向量，代表的就是oc向量，o是坐标系原点。  
3、得到了向量就可以利用vector3中提供的成员属性，得到模长和单位向量。  
4、模长相当于可以得到两点之间的距离，单位向量主要是用来进行移动计算的它不会影响我们想要的移动效果。  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419181120094-1167376908.png)

```dart
  Vector3 A = new Vector3(1, 2, 3);
  Vector3 B = new Vector3(5, 4, 7);
  //两点向量
  Vector3 AB = B - A;
  Vector3 BA = A - B;

  //两个物体之间的向量
  Vector3 Vec = Object .position - transform.position;

  //magnitude 向量的模长
  print(Vec.magnitude);
  print(Vector3.Distance(Object.position, transform.position));
  
  //单位向量 normalized
  print(Vec.normalized);
  print(Vec / Vec.magnitude);
```

#### 向量加减乘除运算

![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419182158136-1614517809.png)![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419182205652-2131240019.png)  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419182213895-712073258.png)![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419182220564-241564410.png)

```csharp
  #region 知识点一 向量加法
  transform.position += new Vector3(1, 2, 3);
  #endregion

  #region 知识点二 向量减法
  transform.position -= new Vector3(1,2,3);
  #endregion

  #region 知识点三 向量乘除标量 放大缩小n倍
  transform.localScale *= 2;
  transform.localScale /= 2;
  #endregion
```

#### 向量点乘(dot product)

**公式一：**A•B = (a1,b1,c1)•(a2,b2,c2) = a1a2 + b1b2 +c1c3  
**公式二：**A•B = |A||B|cosβ  
**几何意义：投影，判断前后**  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419210342409-2068848744.png)  
**求角度：**  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419210354489-1848933709.png)

```csharp
        #region 知识点一 通过点乘判断对象的方位
        //Vector3 提供了计算点乘的方法
        Debug.DrawRay(transform.position, transform.forward, Color.blue);
        Debug.DrawRay(transform.position, Target.position - transform.position, Color.green);

        if (Vector3.Dot(transform.forward, Target.position - transform.position) >= 0)
        {
            print("目标在前方");
        }
        else
            print("目标在后方");
        #endregion

        #region 知识点二 通过点乘推导公式算出夹角
        //公式: 角度 = Acos（单位向量 • 单位向量）
        //1、用单位向量算出点乘结果
        float DotResult = Vector3.Dot(transform.forward, (Target.position - transform.position).normalized);
        //2、用反三角函数得出弧度,然后转为角度
        print("角度：" + Mathf.Acos(DotResult) * Mathf.Rad2Deg);

        //Vector3中提供了 得到两个向量之间夹角的方法
        print("角度：" + Vector3.Angle(transform.forward, Target.position - transform.position));

        //作用
        //怪物范围检测 角度范围内检测
        #endregion
```

#### 向量叉乘(cross product)

**公式：**  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419211954120-1389268196.png)  
**模计算：**|a×b|=|a||b|sinθ，平行四边形面积计算  
**几何意义：判断左右、法向量，三角形面片朝向**

![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240419212010240-439651147.png)

```bash
  #region 知识点一 叉乘计算
  print(Vector3.Cross(A.position, B.position).normalized);
  #endregion
  if (Vector3.Cross(A.position, B.position).y > 0)
      print("B在A的左边");
  else
      print("B在A的右边");
```

* * *

### 矩阵乘法

#### 矩阵概念

矩阵的结构是由 m x n 个标量组成。  
在程序中，我们用于存储矩阵结构的容器类型有很多选择，最常见的的为：  
1、数组（一维、二维都可以）  
2、嵌套列表（两个List嵌套）  
3、开发工具提供的类或结构体（Unity中的Matrix4x4、Matrix3x2结构体）

#### 矩阵和标量的乘法

矩阵(M)中的每一个标量和标量(k)相乘即可  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240427165641434-525454919.png)

#### 矩阵和矩阵的乘法

1、首先需要判断两个矩阵是否能够相乘  
**判断条件：**左列右行要相等  
2、A和B两个矩阵，AB两个矩阵相乘的结果是C矩阵。  
那么C(11) = A(1n).B(n1)、C(12) = A(1n).B(n2)、C(13) = A(1n).B(n3)  
**解读：**C矩阵中的第一行第一列的值等于A中第一行点乘B中第一列。  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240427170000728-2018484953.png)

##### 矩阵之间的乘法

1、不满足交换律  
AB ≠ BA  
2、满足结合律  
(AB)C = A(BC)  
ABCDE = (AB)(CD)E = A((BC)D)E

* * *

### 特殊矩阵

+   方块矩阵 —— 行列数相等的矩阵。
+   对角矩阵 —— 只有主对角线有值，其余元素全为零的方阵。
+   单位矩阵 —— 主对角线上的元素均为1 的对角矩阵。
+   数量矩阵 —— 主对角线上的元素为同一值的对角矩阵。
+   转置矩阵 —— 将原始矩阵的行和列互换得到的新矩阵。
    +   矩阵转置的转置等于原矩阵 (MT)T = M
    +   矩阵串接的转置，等于反向串接各个矩阵的转置 (AB)T =BTAT

#### 逆矩阵

+   逆矩阵必须是一个方阵，并且不是所有矩阵都有逆矩阵。
    +   假设一个方阵 M ，它的逆矩阵用 M\-1 表示。
    +   那么存在 **MM\-1 = M\-1M = E（单位矩阵）**
+   如果一个矩阵存在对应的逆矩阵，我们就说该矩阵是可逆的（或称非奇异的）。
+   如果不存在，那么该矩阵为不可逆的（或称奇异的）。
+   判断方式:行列式不为0，那么可逆。

##### 行列式的计算方式

假设矩阵为M，|M| 表示M矩阵的行列式，行列式是一个标量（数值）  
**计算方法：**  
1、左下左上画对角，线上数值都相乘，数值数量为行列，数量不够对岸取  
2、左下分组加，左上分组减  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240427171928257-740805183.png)

##### 代数余子式矩阵

![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240427172139328-1500061767.png)

##### 标准伴随矩阵

标准伴随矩阵为原矩阵的代数余子式矩阵的转置矩阵。  
![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240427172306673-1194552808.png)

##### 逆矩阵的计算

**1、逆矩阵 = 标准伴随矩阵 / 行列式**  
M\-1 = CT / |M|  
**2、初等变换**  
(A E)->(E A\-1)

##### 逆矩阵的重要性质

1.逆矩阵的逆矩阵是原矩阵本身 (M\-1)\-1 = M  
2.矩阵乘以自己的逆矩阵等于单位矩阵 MM\-1 = M\-1M = E  
3.单位矩阵的逆矩阵是它本身 E\-1 = E  
4.转置矩阵的逆矩阵是逆矩阵的转置 (MT)\-1 = (M\-1)T  
5.矩阵串接相乘后的逆矩阵 等于 反向串接各个矩阵的逆矩阵 相乘 (AB)\-1 = B\-1A\-1  
6.逆矩阵可以计算矩阵变换的反向变换（M为矩阵，v为一个矢量）  
M\-1(Mv) = (M\-1M)v = Ev = v

#### 正交矩阵

正交矩阵是一种特殊的方阵，正交的意思是垂直  
**它的特点是：**  
1、一个方阵和它的转置矩阵相乘为单位矩阵，那么它就是正交矩阵  
MMT = MTM = E  
2、通过正交矩阵的这一性质，再根据上节课学习的逆矩阵的一个重要性质  
MM\-1 = M\-1M = E  
3、我们可以推导出：如果一个矩阵是正交的，那么它的逆矩阵等于其转置矩阵  
MT = M\-1  
4、如果一个矩阵是正交矩阵，那么它的转置矩阵也是正交矩阵

##### 判断是否为正交矩阵

![](https://img2024.cnblogs.com/blog/2800810/202404/2800810-20240427181138514-2019748689.png)  
根据正交矩阵的基本概念，我们可以总结出判断一个矩阵是否是正交矩阵的方式有：

+   判断MMT = MTM = E ，满足则为正交矩阵
+   判断矩阵的每一行（列）是否是单位向量
    +   判断矩阵的行（列）向量是否彼此正交（垂直）

* * *

### 行列矩阵

**一、列矩阵和行矩阵的基本概念**  
1、列矩阵就是只有一列的矩阵；行矩阵就是只有一行的矩阵。他们一般用于表示向量  
2、把向量作为列矩阵和行矩阵与矩阵进行乘法运算时，计算顺序（列在后，行在前）和结果是不同的  
**二、列矩阵和行矩阵在Unity中的使用规则**  
1、在Unity的Shader开发中，我们采用**列**矩阵的形式进行向量计算，利用结合律，我们可以从右往左阅读  
CBAv = C(B(Av))  
2、如果想要使用**行**矩阵计算出和列矩阵相同的结果，我们可以乘以变换矩阵的转置矩阵  
vATBTCT = (((vAT)BT)CT)

* * *

### 矩阵的几何意义

#### 点和向量能在图像中画出来，那么矩阵可以吗？

**矩阵的可视化结果就是：**变换  
在游戏开发中，如果你看到了一个矩阵，那么基本上你可以认为你看到的是一个变换，这些变换一般包含：平移、旋转、缩放。  
**比如：**我们想要将一个点、一个向量进行一种变换（平移、旋转、缩放）。那么我们可以利用矩阵来进行数学计算，从而达到变换的目的。

#### 我们可以利用矩阵相关知识做什么？

对三维空间中的向量进行平移、旋转、缩放、坐标变换、投影等等计算，这样我们就可以对Shader中的数据进行处理，让其最终在屏幕上的效果是按照我们的需求来呈现的。

#### 什么是变换？

**线性变换：**指可以保留矢量加和标量乘的变换。缩放、旋转、错切、镜像、正交投影等。  
**仿射变换：**指合并线性变换和平移变换的变换类型。齐次坐标。

* * *

*持续更新中*

分类: [UnityShader](https://www.cnblogs.com/feng-Ling/category/2385021.html)

