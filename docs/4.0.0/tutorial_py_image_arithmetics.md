# 图像的算术运算

## 目标

* 学习对图像的几种算术运算，如加法，减法，按位运算等。
* 您将学习以下函数：[cv.add()](https://docs.opencv.org/4.0.0/d2/de8/group__core__array.html#ga10ac1bfb180e2cfda1701d06c24fdbd6)，[cv.addWeighted()](https://docs.opencv.org/4.0.0/d2/de8/group__core__array.html#gafafb2513349db3bcff51f54ee5592a19)等。

## 图像加法

您可以通过OpenCV函数，[cv.add()](https://docs.opencv.org/4.0.0/d2/de8/group__core__array.html#ga10ac1bfb180e2cfda1701d06c24fdbd6)或简单地通过numpy操作将两个图像相加，res = img1 + img2。两个图像应该具有相同的深度和类型，或者第二个图像可以是标量值。

> **注意**
OpenCV相加操作和Numpy相加操作之间存在差异。OpenCV添加是饱和操作，而Numpy添加是模运算。

例如，请考虑以下示例：

```python
>>> x = np.uint8([250])
>>> y = np.uint8([10])

>>> print(cv.add(x,y)) #250 + 10 =260 => 255
[[255]]

>>> print(x + y)
[4]
```
在将两个图象相加时会发现Opencv函数能够提供更好的结果，所以尽可能地选择Opencv函数。
## 图像混合

这也是将图像相加，但是对图像赋予不同的权重，从而给出混合感或透明感。图像按以下等式添加：

<div align=center>
<img src="/docs/4.0.0/img/function_1.png">
</div>

通过在0->1之间改变α的值,您可以在一个图像与另一个图像之间执行很酷的过渡。

在这里，我拍了两张图片将它们混合在一起。第一图像的权重为0.7，第二图像的权重为0.3。[cv.addWeighted()](https://docs.opencv.org/4.0.0/d2/de8/group__core__array.html#gafafb2513349db3bcff51f54ee5592a19)在图像上应用以下等式。

<div align=center>
<img src="/docs/4.0.0/img/function_2.png">
</div>

这里γ被视为0.

```Python
img1= cv.imread('ml.png')
img2= cv.imread('opencv-logo.png')

dst = cv.addWeighted(img1,0.7,img2,0.3,0)

cv.imshow('dst',dst)
cv.waitKey(0)
cv.destroyAllWindows()
```
检查以下结果：

<div align=center>
<img src="/docs/4.0.0/img/blending.jpg">
</div>


## 按位操作

这包括按位AND，OR，NOT和XOR运算。它们在提取图像的某一部分（我们将在后面的章节中看到）、定义和使用非矩形ROI等方面非常有用。下面我们将看到如何更改图像的特定区域的示例：

```python
#加载两张图片
img1 = cv.imread（'messi5.jpg'）
img2 = cv.imread（'opencv-logo-white.png'）
#我想在左上角放置标识，所以我创建了一个ROI
rows，cols，channels = img2.shape
roi = img1 [0：rows，0：cols]
#现在创建一个徽标掩码并创建其反转掩码
img2gray = cv.cvtColor（img2，cv.COLOR_BGR2GRAY）
ret，mask = cv.threshold（img2gray，10,255，cv.THRESH_BINARY）
mask_inv = cv.bitwise_not（mask）
#现在使ROI中的徽标区域变黑
img1_bg = cv.bitwise_and（roi，roi，mask = mask_inv）
#仅从徽标图像中获取徽标区域。
img2_fg = cv.bitwise_and（img2，img2，mask = mask）
#在ROI中放置徽标并修改主图像
dst = cv.add（img1_bg，img2_fg）
img1 [0：rows，0：cols] = dst
cv.imshow（'res'，img1）
cv.waitKey（0）
cv.destroyAllWindows（）
```

请参阅下面的结果。左图显示了我们创建的蒙版。右图显示最终结果。为了加深理解，请在上面的代码中显示所有中间图像，尤其是img1_bg和img2_fg。
<div align=center>
<img src="/docs/4.0.0/img/overlay.jpg">
</div>

## 其他资源
## 练习
1. 使用[cv.addWeighted()](https://docs.opencv.org/4.0.0/d2/de8/group__core__array.html#gafafb2513349db3bcff51f54ee5592a19)函数在文件夹中创建图像之间平滑过渡的幻灯片并放映。
