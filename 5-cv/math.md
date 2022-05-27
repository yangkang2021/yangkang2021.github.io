
1. ##### RGB图像理解,一个通道可以看作：
- 空间几何：它是一个矩形范围的，高度0-255范围内的，曲面。
- 统计学：以一行(列)为元素的一组数据，可计算协方差矩阵。
- 高等数学：
    -   二元函数f(x,y)：x，y是像素位置。
    -   求导可以具体到变量的每个具体值。
    -   可求二阶偏导海森矩阵。得四个矩阵或者四通道的一个矩阵。
    -   多个一元函数f(x)或者f(y)，可求雅可比矩阵。

1. ##### 阿尔法融合 -- 线性融合
$$
dst(x,y)=\alpha src_0(x,y) + (1-\alpha)src_1(x,y) \qquad (\alpha \in [0.0,1.0])
$$

2. ##### 图像线性变换 -- 调整图像亮度与对比度
$$
dst(x,y)=\alpha src(x,y) +  \beta \qquad (\alpha>0)
$$

3. ##### 图像卷积公式 -- 各种模糊，滤波，梯度等
$$
dst(x,y)= \sum_{k}\sum_{l} src(x+k,y+l) kernel(k,l) \qquad (中心点的k和l为0)
$$
    
4. ##### 形态学
$$
dilate(x,y) = \max_{}src(x+k,y+l)

erode(x,y) = \min_{}src(x+k,y+l)

open(x,y) = dilate(erode(x,y))

clos(x,y) = erode(dilate(x,y))

morph-grad(x,y) = dilate(x,y) - erode(x,y)

tophat= src(x,y)-open(x,y)

blackhat = close(x,y) - src(x,y)

$$

5. #####  固定阈值
THRESH_BINARY
$$
dst(x,y) = \begin{cases}  
maxval &  if \ src(x,y)> thresh\\
0      & otherwise
\end{cases}
$$

THRESH_BINARY_INV
$$
dst(x,y) = \begin{cases}  
0       &  if \ src(x,y)> thresh\\
maxval  & otherwise
\end{cases}
$$

THRESH_TRUNC
$$
dst(x,y) = \begin{cases}  
thresh &  if \ src(x,y)> thresh\\
0      & otherwise
\end{cases}
$$

THRESH_TOZERO
$$
dst(x,y) = \begin{cases}  
src(x,y)  &  if \ src(x,y)> thresh\\
0         & otherwise
\end{cases}
$$

THRESH_TOZERO_INV
$$
dst(x,y) = \begin{cases}  
0         &  if \ src(x,y)> thresh\\
src(x,y)  & otherwise
\end{cases}
$$

6. #####  距离公式
欧式距离(L2)

$$
L_2 = \sqrt{\sum_{i=1}^{n}(x_i-y_i)^2}
$$

曼哈顿距离(L1)

$$
L_1 = \sum_{i=1}^{n}|x_i-y_i|
$$

切比雪夫距离
$$
dist=\max_{i=1}^{n}(|x_i-y_i|)
$$

海明距离
$$
两列数据对应位置值不同的次数总和
$$

闵可夫斯基距离

$$
dist = \sqrt[p]{\sum_{i=1}^{n}(x_i-y_i)^p}
$$

巴氏距离
$$
D_B(p,q) = -ln(\sum_{x \ in X} \sqrt{p(x)q(x)})
$$

7. #####  统计学数字特征
均值

$$


\overline{X} = \frac{\sum_{i=1}^nX_i}{n}
$$

方差:偏离均值的程度
$$
S^2 = \frac {\sum_{i=1}^n (X_i-\overline{X})^2} {n或者(n-1)}
$$
标准差(均方差):所有样本到均值的距离之平均

$$
S= \sqrt \frac {\sum_{i=1}^n (X_i-\overline{X})^2} {n或者(n-1)}
$$

协方差:两组数据是否存在联系。 协方差矩阵

$$
Cov(X,Y) = \frac {\sum_{i=1}^n (X_i-\overline{X}) (Y_i-\overline{Y}) } {n或者(n-1)}
$$

相关系数:
$$
\rho(X,Y) = \frac {\sum_{i=1}^n (X_i-\overline{X}) (Y_i-\overline{Y}) } {S(X) S(Y)}
$$

8. #### 高斯函数

$$
一维：
G= \frac {1}{ 2 \pi \sigma}e^{\frac{1}{2} (\frac{x-\mu}{ \sigma})^2} 

二维高斯：
$$

9. #### 矩

$$
空间矩/几何矩:
m_{ij} = \sum_{xy}(src(x,y) x^i y ^j)

重心：
\overline{x} = \frac{m_{10}}{m_{00}},\overline{y} = \frac{m_{01}}{m_{00}}


中心矩：
mu_{ij} = \sum_{xy}(src(x,y) (x-\overline{x})^i   (y-\overline{y}) ^j)

归一化的中心矩
nu_{ij} = \frac{m_{ij}}{m_{00}^{(i+j)/2+1}}


Hu矩(略)
$$

10. #### 积分图

$$
积分图公式：
ii(x,y) = \sum_{x^\prime = 0}^{x} \sum_{y^\prime = 0}^{y} src(x^\prime,y^\prime)

可递推
$$



11. #### 局部均方差滤波--边缘保留滤波

$$


局部均值：
\overline{X(x,y)} =  \frac{1}{(2n+1)(2m+1)} \sum_{k=x-n}^{x+n} \sum_{l=y-m}^{y+m} src(k,l)  \qquad (窗口大小:2n+1,2m+1)

局部方差：
S(x,y)^2 =  \frac{1}{(2n+1)(2m+1)} \sum_{k=x-n}^{x+n} \sum_{l=y-m}^{y+m} (src(k,l) - \overline{X(x,y)} )^2  \qquad (窗口大小:2n+1,2m+1)

局部方差化简：
S(x,y)^2 =  \frac{1}{(2n+1)(2m+1)} (\sum_{k=x-n}^{x+n} \sum_{l=y-m}^{y+m} src(k,l)^2 - \frac{1}{(2n+1)(2m+1)} \overline{X(x,y)} ^2)  \qquad (窗口大小:2n+1,2m+1)


局部均方差滤波：dst(x,y) = (1-k)\overline{X(x,y)}  + ksrc(x,y) \qquad (其中k=\frac{S(x,y)^2}{S(x,y)^2+ \sigma } , \sigma为用户输入的参数)

说明：局部方差S(x,y)^2越小(k趋近于0)说明越平坦则应趋向于取平均值，否则说明是边缘(k趋近于1)则应趋向于取图像原值。
$$



12. ##### 图像空间缩减
$$
dst(x,y)= src(x,y) / N * N  \qquad (从256空间缩小到64空间：N=256/64=4)
$$