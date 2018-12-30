---
layout: post
title: How to detect lines in image using Hough Line Transform (with code example)
---

Line detection has many applications in image processing, computer vision, and machine vision. A very simple example of line detection's application is SUDOKU Solver. If you want to solve SUDOKU which locates in an image, first, you have to detect SUDOKU grid and extract digits to recognize those numbers. Hough Line Transform is one of the popular techniques to detect lines in images.  
This article will explain how to detect lines in an image using Hough Line Transform with OpenCV library and Python code example.

<p align="center">
<img src="https://4.bp.blogspot.com/-LpibN9JZlPA/WMUON-LS8PI/AAAAAAAADUA/jVztsFF9uDYmY6BrwHa_IS67yQgn0PngwCLcB/s400/lines_detected.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Detecting line on a SUDOKU grid
</span>
</p>
Note that we only can use Hough Line Transform after we detected edges on the image. The detected edges image is the input of Hough Line Transform. There are many techniques to detect edges on the image such as Sobel or Canny algorithms. I will explain the idea of these techniques when I have enough time :). But here are very good explanation video of edges detection on Computerphile Channel (Check the link below)

1. [Finding the Edges (Sobel Operator)](https://www.youtube.com/watch?v=uihBwtPIBxM)  
2. [Canny Edge Detector](https://www.youtube.com/watch?v=sRFM5IEqR2w)

The image after detect edge will be like this (Check the image below). This image will be a grayscale image. Every pixel on the edge will have a value of 255 or 1(white lines), otherwise, the pixel not located on the edge will have a value of 0 (black area).

<p align="center">
<img src="https://3.bp.blogspot.com/-ZhmTf2fth34/WMVQLxOnJhI/AAAAAAAADUg/Ugkv8d6B1P8BIa2WeVJJcX_rgnfi9mAKwCLcB/s400/lines_detected.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Image after apply edge detection
</span>
</p>
Now, let's see how Hough Line Transform can detect lines in the image.

## Theory
<p align="center">
<img src="https://3.bp.blogspot.com/-pbru1ykUXCw/WMUU9XBFvsI/AAAAAAAADUQ/9yqq-4EgkgU-nChiof9rfTK7Yobx9Le_ACLcB/s320/2017-03-12_182658.jpg">
</p>
A line can be represented as $$\rho = x\cos \theta + y\sin \theta$$ where $$\rho$$ is the perpendicular distance from the origin to the line, and $$\theta$$ is the angle formed by this perpendicular line and horizontal axis. That direction varies on how you represent the coordinate system. This representation above is used in OpenCV. Any vertical line will have $$0$$ degree and horizontal lines will have 90 degrees of $$\theta$$. Normally, the origin point will be the top-left point in the image so the maximum $$\theta$$ is $$90$$ degrees and minimum $$\theta$$ is $$0$$ degree.

We know that every point on the line always satisfies this equation: $$\rho = x\cos \theta + y\sin \theta$$  (1). If we know a pair of ($$\rho, \theta$$), we always can draw a line.  

So, the idea of Hough Transform is creating a 2D array $$M$$ as a matrix to hold the values of two parameters ($$\rho$$ and $$\theta$$).  
Let rows denote the $$\rho$$ and columns denote the $$\theta$$. We assume there exists a line $$d$$ with $$\rho=\rho_1$$ and $$\theta=\theta_1$$ on the image. If a point $$A(x_1,y_1)$$ lies on $$d$$ satisfies (1) $$\rho_1=x_1\cos \theta_1+y_1\sin \theta_1$$, we increase the value of $$M[\rho_1][\theta_1]$$ by $$1$$.  
We will continue this process for every point on the line, increasing the value of $$M[\rho_1][\theta_1]$$ by $$1$$ if that point satisfies equation (1). In the end, the value of $$M[\rho_1][\theta_1]$$ will be must bigger than another cell in 2D array $$M$$. And the value of $$M[\rho_1][\theta_1]$$ will be also the length of the line.

The size of the 2D array depends on the accuracy you need. Suppose you want the accuracy of angles to be $$1$$ degree, you need $$180$$ columns. For $$\rho$$, the maximum distance possible is the diagonal length of the image. So taking one pixel accuracy, the number of rows can be the diagonal length of the image.

## Example
Suppose we have an image with 20x20 pixels and contain 2 lines (black lines - d1 and d2) like this.

<p align="center">
<img src="https://2.bp.blogspot.com/-MSu3_sGr4lA/WNIjImmKXMI/AAAAAAAADWM/0wEJ1k4qd38-dH0Fo3YlbpKrzlDGtivpgCLcB/s400/2017-03-21_161938.jpg">
</p>
The perpendicular distance between $$d1$$ and the origin is $$\rho=7$$ and the angle between this perpendicular line and the horizontal axis ($$Ox$$) is $$0$$ degrees and the length of $$d1$$ is $$15$$ (pixels). Similarly with $$d2$$, $$\rho=12$$, $$\theta=90$$, and the length is $$8$$ (pixels).  
If you want the accuracy of angles to be $$1$$ degree and the maximum distance possible is the diagonal length of the image, you will need a 2D array with $$180$$ columns and $$28$$ rows ($$20\sqrt{2}\approx28$$).

After running Hough Line Transform on that image, we will have $$M[7][0]=15$$ and $$M[12][90]=8$$ corresponding to the length of $$d1$$ and $$d2$$.  
So now, let's implement a simple Python code to see how Hough Line Transform actually work.

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;"><span style="color: green; font-weight: bold;">import</span> <span style="color: blue; font-weight: bold;">numpy</span> <span style="color: green; font-weight: bold;">as</span> <span style="color: blue; font-weight: bold;">np</span>
<span style="color: green; font-weight: bold;">from</span> <span style="color: blue; font-weight: bold;">sympy</span> <span style="color: green; font-weight: bold;">import</span> <span style="color: #666666;">*</span>

<span style="color: #408080; font-style: italic;"># Create image with 20x20 pixel</span>
img <span style="color: #666666;">=</span> np<span style="color: #666666;">.</span>zeros((<span style="color: #666666;">20</span>,<span style="color: #666666;">20</span>), dtype<span style="color: #666666;">=</span>np<span style="color: #666666;">.</span>int)
<span style="color: #408080; font-style: italic;"># Draw 2 lines correspond with above image</span>
<span style="color: #408080; font-style: italic;"># Every pixel lies on a line will have value of 1</span>
<span style="color: #408080; font-style: italic;"># Draw line d1, length = 15</span>
<span style="color: green; font-weight: bold;">for</span> i <span style="color: #aa22ff; font-weight: bold;">in</span> <span style="color: green;">range</span>(<span style="color: #666666;">0</span>,<span style="color: #666666;">15</span>):
    img[i<span style="color: #666666;">+1</span>][<span style="color: #666666;">7</span>]<span style="color: #666666;">=1</span>
<span style="color: #408080; font-style: italic;"># Draw line d2, length = 8</span>
<span style="color: green; font-weight: bold;">for</span> i <span style="color: #aa22ff; font-weight: bold;">in</span> <span style="color: green;">range</span>(<span style="color: #666666;">11</span>,<span style="color: #666666;">19</span>):
    img[<span style="color: #666666;">12</span>][i]<span style="color: #666666;">=1</span>

<span style="color: #408080; font-style: italic;"># Create a 2D array to holds the value of rho and theta</span>
M <span style="color: #666666;">=</span> np<span style="color: #666666;">.</span>zeros((<span style="color: #666666;">28</span>,<span style="color: #666666;">91</span>), dtype<span style="color: #666666;">=</span>np<span style="color: #666666;">.</span>int)

<span style="color: #408080; font-style: italic;"># Scan image to find some lines</span>
<span style="color: green; font-weight: bold;">for</span> x <span style="color: #aa22ff; font-weight: bold;">in</span> <span style="color: green;">range</span>(<span style="color: #666666;">20</span>):
    <span style="color: green; font-weight: bold;">for</span> y <span style="color: #aa22ff; font-weight: bold;">in</span> <span style="color: green;">range</span>(<span style="color: #666666;">20</span>):
        <span style="color: green; font-weight: bold;">if</span>(img[x][y]<span style="color: #666666;">==1</span>):
            <span style="color: green; font-weight: bold;">for</span> rho <span style="color: #aa22ff; font-weight: bold;">in</span> <span style="color: green;">range</span>(<span style="color: #666666;">28</span>):
                <span style="color: green; font-weight: bold;">for</span> theta <span style="color: #aa22ff; font-weight: bold;">in</span> <span style="color: green;">range</span>(<span style="color: #666666;">0</span>,<span style="color: #666666;">91</span>):
                    <span style="color: green; font-weight: bold;">if</span>(rho<span style="color: #666666;">==</span>y<span style="color: #666666;">*</span>cos(theta<span style="color: #666666;">*</span>pi<span style="color: #666666;">/180</span>) <span style="color: #666666;">+</span> x<span style="color: #666666;">*</span>sin(theta<span style="color: #666666;">*</span>pi<span style="color: #666666;">/180</span>)
                        M[rho][theta] <span style="color: #666666;">+=</span> <span style="color: #666666;">1</span>

<span style="color: #408080; font-style: italic;"># Print the value of M[7][0] and M[12][90] to see the length of d1 and d2</span>
<span style="color: green; font-weight: bold;">print</span> M[<span style="color: #666666;">7</span>][<span style="color: #666666;">0</span>]
<span style="color: #408080; font-style: italic;"># Result: 15</span>
<span style="color: green; font-weight: bold;">print</span> M[<span style="color: #666666;">12</span>][<span style="color: #666666;">90</span>]
<span style="color: #408080; font-style: italic;"># Result: 8</span>
</pre>
</div>
Note that we will use $$\cos$$, $$\sin$$ method and $$\pi$$ in **_sympy_** library instead of using **_numpy_** because $$\pi$$ in **_numpy_** (numpy.pi) is not the actual $$\pi$$ value. So, you will get the wrong result when using **`numpy.cos(pi/2)`**.

Try to run by yourself and see how Hough Line Transform works.

## Hough Line Transform in OpenCV
In real problem, there are too many lines in image and quite complex to write code and detect line by yourself. I prefer to use OpenCV - A strong Computer Vision library to deal with Line Detection problem.  
We can simply use Hough Line Transform in OpenCV with cv2.HoughLines() function. It will return an array of ($$\rho,\theta$$) values. $$\rho$$ is measured in pixels and $$\theta$$ is measured in radians.  
The first parameter of **`cv2.HoughLines()`** function is a binary image, so apply thresholding or use canny edge detection before finding applying Hough Line Transform. The second and third parameters are $$\rho$$ and $$\theta$$ accuracies respectively. The final parameter is the threshold, which means the minimum length of a line to be detected.

Here is the code I took from OpenCV online documents website with adding some explanations.

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;"><span style="color: green; font-weight: bold;">import</span> <span style="color: blue; font-weight: bold;">cv2</span>
<span style="color: green; font-weight: bold;">import</span> <span style="color: blue; font-weight: bold;">numpy</span> <span style="color: green; font-weight: bold;">as</span> <span style="color: blue; font-weight: bold;">np</span>

<span style="color: #408080; font-style: italic;"># Loading image contains lines</span>
img <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>imread(<span style="color: #ba2121;">'lines.jpg'</span>)
<span style="color: #408080; font-style: italic;"># Convert to grayscale</span>
gray <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>cvtColor(img,cv2<span style="color: #666666;">.</span>COLOR_BGR2GRAY)
<span style="color: #408080; font-style: italic;"># Apply Canny edge detection, return will be a binary image</span>
edges <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>Canny(gray,<span style="color: #666666;">50</span>,<span style="color: #666666;">100</span>,apertureSize <span style="color: #666666;">=</span> <span style="color: #666666;">3</span>)
<span style="color: #408080; font-style: italic;"># Apply Hough Line Transform, minimum lenght of line is 200 pixels</span>
lines <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>HoughLines(edges,<span style="color: #666666;">1</span>,np<span style="color: #666666;">.</span>pi<span style="color: #666666;">/180</span>,<span style="color: #666666;">200</span>)
<span style="color: #408080; font-style: italic;"># Print and draw line on the original image</span>
<span style="color: green; font-weight: bold;">for</span> rho,theta <span style="color: #aa22ff; font-weight: bold;">in</span> lines[<span style="color: #666666;">0</span>]:
 <span style="color: green; font-weight: bold;">print</span>(rho, theta)
 a <span style="color: #666666;">=</span> np<span style="color: #666666;">.</span>cos(theta)
 b <span style="color: #666666;">=</span> np<span style="color: #666666;">.</span>sin(theta)
 x0 <span style="color: #666666;">=</span> a<span style="color: #666666;">*</span>rho
 y0 <span style="color: #666666;">=</span> b<span style="color: #666666;">*</span>rho
 x1 <span style="color: #666666;">=</span> <span style="color: green;">int</span>(x0 <span style="color: #666666;">+</span> <span style="color: #666666;">1000*</span>(<span style="color: #666666;">-</span>b))
 y1 <span style="color: #666666;">=</span> <span style="color: green;">int</span>(y0 <span style="color: #666666;">+</span> <span style="color: #666666;">1000*</span>(a))
 x2 <span style="color: #666666;">=</span> <span style="color: green;">int</span>(x0 <span style="color: #666666;">-</span> <span style="color: #666666;">1000*</span>(<span style="color: #666666;">-</span>b))
 y2 <span style="color: #666666;">=</span> <span style="color: green;">int</span>(y0 <span style="color: #666666;">-</span> <span style="color: #666666;">1000*</span>(a))
 cv2<span style="color: #666666;">.</span>line(img,(x1,y1),(x2,y2),(<span style="color: #666666;">0</span>,<span style="color: #666666;">0</span>,<span style="color: #666666;">255</span>),<span style="color: #666666;">2</span>)

<span style="color: #408080; font-style: italic;"># Show the result</span>
cv2<span style="color: #666666;">.</span>imshow(<span style="color: #ba2121;">"Line Detection"</span>, img)
cv2<span style="color: #666666;">.</span>waitKey(<span style="color: #666666;">0</span>)
cv2<span style="color: #666666;">.</span>destroyAllWindows()</pre>
</div>
I used this image to run Hough Line Transform:

<p align="center">
<img src="https://2.bp.blogspot.com/-R7G52qwewfA/WNIyGna-IgI/AAAAAAAADWc/EENQNqRiZPQ6F9JtxTmF29XAQyuQPohIQCLcB/s400/lines.jpg">
</p>
And here is the result:

<p align="center">
<img src="https://4.bp.blogspot.com/-LpibN9JZlPA/WMUON-LS8PI/AAAAAAAADUA/jVztsFF9uDYmY6BrwHa_IS67yQgn0PngwCLcB/s400/lines_detected.jpg">
</p>

## References
[1] OpenCV: Hough Line Transform - [http://docs.opencv.org/3.1.0/d6/d10/tutorial_py_houghlines.html](http://docs.opencv.org/3.1.0/d6/d10/tutorial_py_houghlines.html)  
[2] Hough Line Transform - Wikipedia - [https://en.wikipedia.org/wiki/Hough_transform](https://en.wikipedia.org/wiki/Hough_transform)

