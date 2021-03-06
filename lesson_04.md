## 教程4：线性滤波

### 1. 基本概念

线性滤波可以说是图像处理最基本的方法，它可以允许我们对图像进行处理，产生很多不同的效果。做法非常简单，首先，有一个二维的滤波器矩阵（有个高大上的名字叫做**卷积核**，熟悉深度学习和卷积神经网络的同学肯定会比较熟悉，哈哈哈）和一个待处理的二维图像。

然后，对于图像中每一个像素点，计算它的邻域像素和滤波器矩阵对应元素的乘积，然后加起来，作为该像素位置的值，这样就完成了滤波过程。

![](C:\Users\Administrator\Desktop\6deb72a3ly1fwny4ycinaj20g70csafb.jpg)





对图像和滤波矩阵进行逐个元素相乘再求和的操作就相当于将一个二维的函数移动到另一个二维函数的所有位置，这个操作就叫卷积（Convolution）。

Convolution可以说是图像处理最基本的操作，但却非常有用。这个操作有两个非常关键的特点：第一，它是线性的；第二，具有平移不变性（shift-invariant）。线性的指这个操作是我们用每个像素的邻域的线性组合来代替这个像素。平移不变性表示我们在图像的每个位置都执行相同的操作。这两个特点使得这个操作非常简单，因为线性操作是最简单的，然后在所有地方都做同样的操作就更简单了。

图像卷积需要4个嵌套循环，所以它并不快，除非我们使用非常小的卷积核。这里一般使用3X3，或者5X5。而且，对于滤波器，也有一定的规则要求：

（1）滤波器的大小应该是奇数，这样它才有一个中心，例如3X3、5X5，或者7X7。

（2）滤波器所有元素之和应该是1，这是为了保持滤波前后图像的亮度保持不变。

（3）如果滤波矩阵所有元素之和大于 1，那么滤波后的图像就会比原图像亮。反之，如果小于1，那么得到的图像就会变暗。

（4）对于滤波后的结构，可能会出现负数或者大于255的数值，对于这种情况，我们将他们直接截断至0和255之间即可。



### 2. 神奇的卷积核

这个小小的卷积核到底有哪些魔法，能让一个图像从惨不忍睹变得秀色可餐？下面一起来领略下这些简约但不简单的卷积核魔法。

#### 2.1 图像锐化滤波器 Sharpness Filter

图像的锐化和边缘检测很像，首先找到边缘，然后把边缘加到原来的图像上面，这样就强化了图像的边缘，使图像看起来更加锐利了。这两者操作统一起来就是锐化滤波器了，也就是在边缘检测滤波器的基础上，再在中心的位置加1，这样滤波后的图像就会和原始的图像具有同样的亮度了，但是会更加锐利。

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1cuu006j20ld06aajc.jpg)

同时，可以把核加大，就可以得到更加精细的锐化结果。

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1dm3tfjj20le05kah6.jpg)

主要是强调图像的细节。最简单的3x3的锐化滤波器如下： 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1eqyyzoj20l903caaf.jpg)

基本原理是计算当前点和周围点的差别，然后把这个差别加到原来的位置上。



#### 2.2 边缘检测 Edge detection

我们要找水平的边缘：需要注意的是，这里矩阵的元素和是0，所以滤波后的图像会很暗，只有边缘的地方是有亮度的。 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1g91zs4j20la05in3r.jpg)

为什么这个滤波器可以寻找到水平边缘呢？因为用这个滤波器卷积相当于求导的离散版本：你将当前的像素值减去前一个像素值，这样你就可以得到这个函数在这两个位置的差别或者斜率。下面的滤波器可以找到垂直方向的边缘，这里像素上和下的像素值都使用： 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1gy66omj20lc05o7bc.jpg)

再下面这个滤波器可以找到45度的边缘：取-2不为了什么，只是为了让矩阵的元素和为0而已。 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1lp5vjnj20lg05un54.jpg)

那下面这个滤波器就可以检测所有方向的边缘: 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1m2ulndj20lc06aguf.jpg)

 为了检测边缘，我们需要在图像对应的方向计算梯度。用下面的卷积核来卷积图像，就可以了。但在实际中，这种简单的方法会把噪声也放大了。另外，需要注意的是，矩阵所有的值加起来要是0. 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1igkjmij20jn03h0t7.jpg)



#### 2.3 浮雕 Embossing filter

浮雕滤波器可以给图像一种3D阴影的效果。只要将中心一边的像素减去另一边的像素就可以了。这时候，像素值有可能是负数，我们将负数当成阴影，将正数当成光，然后我们对结果图像加上128的偏移。这时候，图像大部分就变成灰色了。

下面是45度的浮雕滤波器：

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1jhawsaj20lb069tho.jpg)



我们要加大滤波器，就可以得到更加夸张的效果：

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1jzgjsrj20lb05un4l.jpg)

这种效果非常的漂亮，就像是将一副图像雕刻在一块石头上面一样，然后从一个方向照亮它。它和前面的滤波器不同，它是非对称的。另外，它会产生负数值，所以我们需要将结果偏移，以得到图像灰度的范围。 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1mhbu64j20lb0fsqij.jpg)

A：原图像。B：锐化。C：边缘检测。D：浮雕 



#### 2.4 均值模糊 Box filter (或者 Average filter)

 我们可以将当前像素和它的四邻域的像素一起取平均，然后再除以5，或者直接在滤波器的5个地方取0.2的值即可，如下图： 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1nlw8rkj20lf06a7bu.jpg)

可以看到，这个模糊还是比较温柔的，我们可以把滤波器变大，这样就会变得粗暴了：注意要将和再除以13. 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1o38aq3j20ld05p7aw.jpg)

所以，如果你想要更模糊的效果，加大滤波器的大小即可。或者对图像应用多次模糊也可以。 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1olqs8zj20bt03sdg7.jpg)

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1p0ahpqj20l0097n32.jpg)



#### 2.5 高斯模糊

均值模糊很简单，但不是很平滑。高斯模糊就有这个优点，所以被广泛用在图像降噪上。特别是在边缘检测之前，都会用来移除细节。高斯滤波器是一个低通滤波器。 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1py5ubaj20kp095mzl.jpg)

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1q95fbuj20l809hjyn.jpg)



#### 2.6 运动模糊

运动模糊可以通过只在一个方向模糊达到，例如下面9x9的运动模糊滤波器。注意，求和结果要除以9。 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1sb2gbpj20ks07wafi.jpg)

### 3. 卷积运算

卷积就是一开始说的那样，对于图像的每一个像素点，计算它的邻域像素和滤波器矩阵的对应元素的乘积，然后加起来，作为该像素位置的值。 

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo1ts0im0j20et0dlq4s.jpg)

直接的实现也称为暴力实现brute force，因为它严格按照定义来实现，没有任何优化。当然了，在并行实现里面，它也是比较灵活的。另外，也存在一个优化版本，如果我们的kernel是separable可分的，那么就可以得到一个快5倍左右的卷积方法。 

那卷积核遇到图像边缘怎么办？例如图像顶部的像素，它的上面已经没有像素了，那么它的值如何计算？目前有多种方法~~~ 在我们的作业中，可以采取最简单的做法，即忽略边界像素，直接置0即可。



### 4. 本周作业

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo23bb045j20740740ty.jpg)

对于上面的图像（lena.jpg），使用GDAL编程，分别使用下面的核进行卷积，并观察效果，说明结果是哪一种滤波。

![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo2w4e7huj20go0ehabl.jpg)



![](http://ww1.sinaimg.cn/large/6deb72a3ly1fwo2x14c50j20gl05z0tf.jpg)









