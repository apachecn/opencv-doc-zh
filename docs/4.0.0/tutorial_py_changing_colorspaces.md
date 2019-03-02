# 目标

在本教程中：

* 你会学到如何将图片从一个颜色空间转换到另一个，例如BGR到Gray，BGR到HSV等。
* 另外，我们会创建一个从视频中提取彩色对象的应用。
* 你会学到如下函数：**[cv.cvtColor()](https://docs.opencv.org/4.0.0/d8/d01/group__imgproc__color__conversions.html#ga397ae87e1288a81d2363b61574eb8cab )**，**[cv.inRange()](https://docs.opencv.org/4.0.0/d2/de8/group__core__array.html#ga48af0ab51e36436c5d04340e036ce981 "Video writer class. ")**

## 改变颜色空间

在OpenCV中有超过150种颜色空间转换的方法。但我们仅需要研究两个最常使用的方法，他们是BGR到Gray，BGR到HSV。

我们使用cv.cvtColor(input_image, flag)函数进行颜色转换，其中 flag 决定了转换的类型。 

对于 BGR到Gray 转换我们令 flag 为 **[cv.COLOR_BGR2GRAY](https://docs.opencv.org/4.0.0/d8/d01/group__imgproc__color__conversions.html#gga4e0972be5de079fed4e3a10e24ef5ef0a353a4b8db9040165db4dacb5bcefb6ea)**。 同样，对于 BGR到HSV, 我们令 flag 为 **[cv.COLOR_BGR2HSV](https://docs.opencv.org/4.0.0/d8/d01/group__imgproc__color__conversions.html#gga4e0972be5de079fed4e3a10e24ef5ef0aa4a7f0ecf2e94150699e48c79139ee12)**。如想得到其他flag值，只需要在Python终端中输入如下命令：

```python
>>> import cv2 as cv
>>> flags = [i for i in dir(cv) if i.startswith('COLOR_')]
>>> print( flags )
```

>**注意**
>对于 HSV,  色调范围为 [0,179], 饱和度范围为 [0,255] ，像素值为 [0,255]. 不同的软件使用不同的比例. 所以如果你想用OpenCV的值与别的软件的值作对比，你需要归一化这些范围。

## 目标追踪

现在我们知道了如何将BGR图片转化为HSV图片，我们可以使用它去提取彩色对象。HSV比BGR在颜色空间上更容易表示颜色。在我们的应用中，我们会尝试提取一个蓝色的彩色对象，方法为：

* 提取每一视频帧。
* 将BGR转化为HSV颜色空间。
* 我们将HSV图片的阈值设为蓝色范围。
* 现在提取出了蓝色对象，我们可以随意处理图片了
。

下面是代码:

```python
import cv2 as cv
import numpy as np
cap = cv.VideoCapture(0)
while(1):
    # Take each frame
    _, frame = cap.read()
    # Convert BGR to HSV
    hsv = cv.cvtColor(frame, cv.COLOR_BGR2HSV)
    # define range of blue color in HSV
    lower_blue = np.array([110,50,50])
    upper_blue = np.array([130,255,255])
    # Threshold the HSV image to get only blue colors
    mask = cv.inRange(hsv, lower_blue, upper_blue)
    # Bitwise-AND mask and original image
    res = cv.bitwise_and(frame,frame, mask= mask)
    cv.imshow('frame',frame)
    cv.imshow('mask',mask)
    cv.imshow('res',res)
    k = cv.waitKey(5) & 0xFF
    if k == 27:
        break
cv.destroyAllWindows()
```

接下来的图片显示了追踪蓝色对象:

![图片](./img/frame.jpg)

> **注意**
> 在图片中有一些噪声，在之后的章节中我们会学习如何消除。
> 这是目标追踪最简单的例子。当你学会了轮廓函数，你就可以做的更多，例如找到对象的质心并且使用它来追踪对象，只需在相机和前面移动你的手就可以绘制图表，还有许多其他有趣的东西。

## 如何找到 HSV 值去追踪?
这在 **[stackoverflow.com](http://www.stackoverflow.com/)** 中是经常见到的问题。 这个问题非常简单，你可以使用相同的函数：**[cv.cvtColor()](https://docs.opencv.org/4.0.0/d8/d01/group__imgproc__color__conversions.html#ga397ae87e1288a81d2363b61574eb8cab )**。 不需要输入图片，你只需要输入你需要的 BGR 值即可. 例如, 为了找到绿色的 HSV 值, 可以在Python终端中输入以下代码:

```python
>>> green = np.uint8([[[0,255,0 ]]])
>>> hsv_green = cv.cvtColor(green,cv.COLOR_BGR2HSV)
>>> print( hsv_green )
[[[ 60 255 255]]]
```

现在你可以取 [H-10, 100,100] 和 [H+10, 255, 255] 分别作为上界和下界. 除此之外，你可以使用任何图像编辑工具（如GIMP）或任何在线转换器来查找这些值，但不要忘记调整HSV范围。

## 其他资源

## 练习

1、尝试找到一种方法来提取多个彩色对象，例如，同时提取红色、蓝色、绿色对象。
