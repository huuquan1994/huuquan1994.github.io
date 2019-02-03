---
layout: post
title: "Building a simple SUDOKU Solver from scratch - Part 1: Grid Detection & Digit Extraction"
---

Hi, today I'm gonna explain how to build a simple SUDOKU Solver taken from image step-by-step. You can see the demo of SUDOKU Solver in the GIF image below.

<p align="center">
<img src="https://2.bp.blogspot.com/-R3Nq3Nttrg0/WOnar0LHXWI/AAAAAAAADXg/m63GPD63orcGVE-OBVCQ0aJdMB3qxVB2QCLcB/s1600/ezgif.com-gif-maker_4.gif">
</p>

To build this program, we will go through 4 main steps.

**1. SUDOKU Grid Detection and Digit Extraction**  
**2. Recognize Number with Support Vector Machine (SVM)**  
**3. Solving SUDOKU with Backtracking Algorithm**  
**4. Completing the simple SUDOKU Solver**

This program was built by Python 2.7 and OpenCV 2.4.13 so before you want to follow this post, please install Python 2.7 and OpenCV 2.4.13
**Update**: Since some people asked me to modify the code to work with OpenCV 3.x, please refer to this link if you're using OpenCV 3.x
[SUDOKU_OpenCV3](https://github.com/huuquan1994/Sudoku-Solver/blob/master/Main_SUDOKU_OpenCV3.py)

## 1. SUDOKU Gird Detection and Digit Extraction
The most important thing to solve a SUDOKU board from images is detecting SUDOKU grid (or line). I found many ways to extract digit from SUDOKU on the internet, but I saw this article (http://jponttuset.cat/solving-sudokus-like-a-pro-1/) is the smartest and easy way to extract digit from SUDOKU board. I took the idea from that guy and re-implement myself with Python.
Let's get started!

We will use a famous and simple technique called Hough Line Transform to detect lines on the image. If you're not familiar with Hough Line Transform, please check out my previous article about [**Hough Line Transform**](https://caphuuquan.blogspot.jp/2017/03/how-to-detect-lines-in-image-using.html)

Note: We can visit this website to print any SUDOKU images for practicing: [http://show.websudoku.com/](http://show.websudoku.com/)  

From an RGB frame captured from the webcam, we will convert it to a grayscale image. After that, extract edges using Canny Edge detection and then apply Hough Line Transform to detect lines. We use cv2.HoughLines() with $$\rho$$ unit equal to $$2$$ and minimum length of line to be detected is $$300$$ pixels. It means increasing the accumulator by $$2$$ when a point is satisfied and consider to be a line when the minimum value in the accumulator is $$300$$.
The result will look like this:

<p align="center">
<img src="https://4.bp.blogspot.com/-RPlgbYZQnug/WOnjG-Ky9hI/AAAAAAAADXw/88fVaJQNOKsxfdTavRM54Z8wu4VVa4oUwCLcB/s400/2017-04-09_161517.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 1. Line detection using Hough Line Transform</span>
</p>
The next step is looking for intersections between these lines. When we know the intersection points, we can extract every block that contains SUDOKU digit.  

Hmm ... Look at that! There are too many lines. Synonymous with there are some redundant lines (some lines are overlapped each other).  
But wait, we already know the distance of each line to the origin. It means we can easily remove the overlapped lines if the distance between these lines is close to each other.  
Let's say we will remove the redundant lines if the distance between these lines is less than $$10$$.
Firstly, let's sort these line increasingly by the distance ($$\rho$$) and then check the distance of every $$2$$ lines. If the distance is less than $$10$$, remove the second line.  
You can read the following code

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;"><span style="color: #408080; font-style: italic;"># HOW TO USE</span>
<span style="color: #408080; font-style: italic;"># Use this with a printed SUDOKU Grid</span>
<span style="color: #408080; font-style: italic;"># Press ESC key to Exit</span>
<span style="color: green; font-weight: bold;">import</span> <span style="color: blue; font-weight: bold;">cv2</span>
<span style="color: green; font-weight: bold;">import</span> <span style="color: blue; font-weight: bold;">numpy</span> <span style="color: green; font-weight: bold;">as</span> <span style="color: blue; font-weight: bold;">np</span>

ratio2 <span style="color: #666666;">=</span> <span style="color: #666666;">3</span>
kernel_size <span style="color: #666666;">=</span> <span style="color: #666666;">3</span>
lowThreshold <span style="color: #666666;">=</span> <span style="color: #666666;">30</span>

cv2<span style="color: #666666;">.</span>namedWindow(<span style="color: #ba2121;">"SUDOKU Solver"</span>)
vc <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>VideoCapture(<span style="color: #666666;">0</span>)
<span style="color: green; font-weight: bold;">if</span> vc<span style="color: #666666;">.</span>isOpened(): <span style="color: #408080; font-style: italic;"># try to get the first frame</span>
    rval, frame <span style="color: #666666;">=</span> vc<span style="color: #666666;">.</span>read()
<span style="color: green; font-weight: bold;">else</span>:
    rval <span style="color: #666666;">=</span> <span style="color: green;">False</span>
<span style="color: green; font-weight: bold;">while</span> rval:
 <span style="color: #408080; font-style: italic;"># Preprocess image, convert from RGB to Gray</span>
    sudoku1 <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>cvtColor(frame, cv2<span style="color: #666666;">.</span>COLOR_BGR2GRAY)
    sudoku1 <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>blur(sudoku1, (<span style="color: #666666;">3</span>,<span style="color: #666666;">3</span>))
    <span style="color: #408080; font-style: italic;"># Apply Canny edge detection</span>
    edges <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>Canny(sudoku1, lowThreshold, lowThreshold<span style="color: #666666;">*</span>ratio2, kernel_size)
    <span style="color: #408080; font-style: italic;"># Apply Hough Line Transform, return a list of rho and theta</span>
    lines <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>HoughLines(edges, <span style="color: #666666;">2</span>, cv2<span style="color: #666666;">.</span>cv<span style="color: #666666;">.</span>CV_PI <span style="color: #666666;">/180</span>, <span style="color: #666666;">300</span>, <span style="color: #666666;">0</span>, <span style="color: #666666;">0</span>)
    <span style="color: green; font-weight: bold;">if</span> (lines <span style="color: #aa22ff; font-weight: bold;">is</span> <span style="color: #aa22ff; font-weight: bold;">not</span> <span style="color: green;">None</span>):
        lines <span style="color: #666666;">=</span> lines[<span style="color: #666666;">0</span>]
        lines <span style="color: #666666;">=</span> <span style="color: green;">sorted</span>(lines, key<span style="color: #666666;">=</span><span style="color: green; font-weight: bold;">lambda</span> line:line[<span style="color: #666666;">0</span>])
        <span style="color: #408080; font-style: italic;"># Define the position of horizontal and vertical line</span>
        pos_hori <span style="color: #666666;">=</span> <span style="color: #666666;">0</span>
        pos_vert <span style="color: #666666;">=</span> <span style="color: #666666;">0</span>
        <span style="color: green; font-weight: bold;">for</span> rho,theta <span style="color: #aa22ff; font-weight: bold;">in</span> lines:
         a <span style="color: #666666;">=</span> np<span style="color: #666666;">.</span>cos(theta)
         b <span style="color: #666666;">=</span> np<span style="color: #666666;">.</span>sin(theta)
         x0 <span style="color: #666666;">=</span> a<span style="color: #666666;">*</span>rho
         y0 <span style="color: #666666;">=</span> b<span style="color: #666666;">*</span>rho
         x1 <span style="color: #666666;">=</span> <span style="color: green;">int</span>(x0 <span style="color: #666666;">+</span> <span style="color: #666666;">1000*</span>(<span style="color: #666666;">-</span>b))
         y1 <span style="color: #666666;">=</span> <span style="color: green;">int</span>(y0 <span style="color: #666666;">+</span> <span style="color: #666666;">1000*</span>(a))
         x2 <span style="color: #666666;">=</span> <span style="color: green;">int</span>(x0 <span style="color: #666666;">-</span> <span style="color: #666666;">1000*</span>(<span style="color: #666666;">-</span>b))
         y2 <span style="color: #666666;">=</span> <span style="color: green;">int</span>(y0 <span style="color: #666666;">-</span> <span style="color: #666666;">1000*</span>(a))
         <span style="color: #408080; font-style: italic;"># If b &gt; 0.5, the angle must be greater than 45 degree</span>
         <span style="color: #408080; font-style: italic;"># so we consider that line as a vertical line</span>
         <span style="color: green; font-weight: bold;">if</span> (b<span style="color: #666666;">&gt;0.5</span>):
          <span style="color: #408080; font-style: italic;"># Check the position</span>
          <span style="color: green; font-weight: bold;">if</span>(rho<span style="color: #666666;">-</span>pos_hori<span style="color: #666666;">&gt;10</span>):
           <span style="color: #408080; font-style: italic;"># Update the position</span>
           pos_hori<span style="color: #666666;">=</span>rho
           cv2<span style="color: #666666;">.</span>line(frame,(x1,y1),(x2,y2),(<span style="color: #666666;">0</span>,<span style="color: #666666;">0</span>,<span style="color: #666666;">255</span>),<span style="color: #666666;">2</span>)
         <span style="color: green; font-weight: bold;">else</span>:
          <span style="color: green; font-weight: bold;">if</span>(rho<span style="color: #666666;">-</span>pos_vert<span style="color: #666666;">&gt;10</span>):
           pos_vert<span style="color: #666666;">=</span>rho
           cv2<span style="color: #666666;">.</span>line(frame,(x1,y1),(x2,y2),(<span style="color: #666666;">0</span>,<span style="color: #666666;">0</span>,<span style="color: #666666;">255</span>),<span style="color: #666666;">2</span>)

    <span style="color: #408080; font-style: italic;"># Show the result        </span>
    cv2<span style="color: #666666;">.</span>imshow(<span style="color: #ba2121;">"SUDOKU Solver"</span>, frame)
    rval, frame <span style="color: #666666;">=</span> vc<span style="color: #666666;">.</span>read()
    key <span style="color: #666666;">=</span> cv2<span style="color: #666666;">.</span>waitKey(<span style="color: #666666;">20</span>)
    <span style="color: green; font-weight: bold;">if</span> key <span style="color: #666666;">==</span> <span style="color: #666666;">27</span>: <span style="color: #408080; font-style: italic;"># exit on ESC</span>
     <span style="color: green; font-weight: bold;">break</span>
vc<span style="color: #666666;">.</span>release()
cv2<span style="color: #666666;">.</span>destroyAllWindows()
</pre>
</div>

Now, the result looks better than before.

<p align="center">
<img src="https://4.bp.blogspot.com/-Q0k1aCRuB4M/WOoq0Xqr75I/AAAAAAAADYA/oaV51lhSwakPYV79WCXMDWVELAnhashngCLcB/s400/2017-04-09_212652.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 2. SUDOKU Grid after removing redundancy lines</span>
</p>
Next, we will find the intersection points based on those lines. Just to recap again, every line is satisfied this equation: $$\rho=x\cos \theta+y\sin \theta$$ (1)  
We have $$\rho$$ and $$\theta$$ for each line, so it's easy to find the intersection between 2 lines by solving linear equations. In this post, I use the $$linalg$$ library in Numpy to find the intersection points.
You can check out this link to see how to use $$linalg$$ in Python: [https://docs.scipy.org/doc/numpy/reference/generated/numpy.linalg.solve.html](https://docs.scipy.org/doc/numpy/reference/generated/numpy.linalg.solve.html)

Just change a bit of code and found the intersection points like this

<p align="center">
<img src="https://2.bp.blogspot.com/-2qJJqPnHFpI/WOow0WCEWdI/AAAAAAAADYQ/FlRrUvE1wrE4gO0WgCz8DSyWqd5XapBFgCLcB/s400/2017-04-09_220112.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 3. Intersection points detection</span>
</p>
If we have those points, we also can extract the bouding boxes of the digit number in the SUDOKU board.

<p align="center">
<img src="https://4.bp.blogspot.com/-6ERmA_l-iBk/WOo2A0KUXNI/AAAAAAAADYg/9TDJ75QtxeEcsV1A0mGuKTIbrCe5LxmXwCLcB/s400/result.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 4. Bounding block for each digit</span>
</p>
Here is the code for digit number extraction

<div style="background: #ffffff; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;"><span style="color: #888888;"># HOW TO USE</span>
<span style="color: #888888;"># Use this with a printed SUDOKU Grid</span>
<span style="color: #888888;"># Press ESC key to Exit</span>
<span style="color: #008800; font-weight: bold;">import</span> <span style="color: #0e84b5; font-weight: bold;">cv2</span>
<span style="color: #008800; font-weight: bold;">import</span> <span style="color: #0e84b5; font-weight: bold;">numpy</span> <span style="color: #008800; font-weight: bold;">as</span> <span style="color: #0e84b5; font-weight: bold;">np</span>

ratio2 <span style="color: #333333;">=</span> <span style="color: #0000dd; font-weight: bold;">3</span>
kernel_size <span style="color: #333333;">=</span> <span style="color: #0000dd; font-weight: bold;">3</span>
lowThreshold <span style="color: #333333;">=</span> <span style="color: #0000dd; font-weight: bold;">30</span>

cv2<span style="color: #333333;">.</span>namedWindow(<span style="background-color: #fff0f0;">"SUDOKU Solver"</span>)
vc <span style="color: #333333;">=</span> cv2<span style="color: #333333;">.</span>VideoCapture(<span style="color: #0000dd; font-weight: bold;">0</span>)
<span style="color: #008800; font-weight: bold;">if</span> vc<span style="color: #333333;">.</span>isOpened(): <span style="color: #888888;"># try to get the first frame</span>
    rval, frame <span style="color: #333333;">=</span> vc<span style="color: #333333;">.</span>read()
<span style="color: #008800; font-weight: bold;">else</span>:
    rval <span style="color: #333333;">=</span> <span style="color: #007020;">False</span>
<span style="color: #008800; font-weight: bold;">while</span> rval:
 <span style="color: #888888;"># Preprocess image, convert from RGB to Gray</span>
    sudoku1 <span style="color: #333333;">=</span> cv2<span style="color: #333333;">.</span>cvtColor(frame, cv2<span style="color: #333333;">.</span>COLOR_BGR2GRAY)
    sudoku1 <span style="color: #333333;">=</span> cv2<span style="color: #333333;">.</span>blur(sudoku1, (<span style="color: #0000dd; font-weight: bold;">3</span>,<span style="color: #0000dd; font-weight: bold;">3</span>))
    <span style="color: #888888;"># Apply Canny edge detection</span>
    edges <span style="color: #333333;">=</span> cv2<span style="color: #333333;">.</span>Canny(sudoku1, lowThreshold, lowThreshold<span style="color: #333333;">*</span>ratio2, kernel_size)
    <span style="color: #888888;"># Apply Hough Line Transform, return a list of rho and theta</span>
    lines <span style="color: #333333;">=</span> cv2<span style="color: #333333;">.</span>HoughLines(edges, <span style="color: #0000dd; font-weight: bold;">2</span>, cv2<span style="color: #333333;">.</span>cv<span style="color: #333333;">.</span>CV_PI <span style="color: #333333;">/</span><span style="color: #0000dd; font-weight: bold;">180</span>, <span style="color: #0000dd; font-weight: bold;">300</span>, <span style="color: #0000dd; font-weight: bold;">0</span>, <span style="color: #0000dd; font-weight: bold;">0</span>)
    <span style="color: #008800; font-weight: bold;">if</span> (lines <span style="color: black; font-weight: bold;">is</span> <span style="color: black; font-weight: bold;">not</span> <span style="color: #007020;">None</span>):
        lines <span style="color: #333333;">=</span> lines[<span style="color: #0000dd; font-weight: bold;">0</span>]
        lines <span style="color: #333333;">=</span> <span style="color: #007020;">sorted</span>(lines, key<span style="color: #333333;">=</span><span style="color: #008800; font-weight: bold;">lambda</span> line:line[<span style="color: #0000dd; font-weight: bold;">0</span>])
        <span style="color: #888888;"># Define the position of horizontal and vertical line</span>
        pos_hori <span style="color: #333333;">=</span> <span style="color: #0000dd; font-weight: bold;">0</span>
        pos_vert <span style="color: #333333;">=</span> <span style="color: #0000dd; font-weight: bold;">0</span>
        <span style="color: #888888;"># Create a list to store new bundle of lines</span>
        New_lines <span style="color: #333333;">=</span> []
        <span style="color: #888888;"># Store intersection points</span>
        Points <span style="color: #333333;">=</span> []
        <span style="color: #008800; font-weight: bold;">for</span> rho,theta <span style="color: black; font-weight: bold;">in</span> lines:
         a <span style="color: #333333;">=</span> np<span style="color: #333333;">.</span>cos(theta)
         b <span style="color: #333333;">=</span> np<span style="color: #333333;">.</span>sin(theta)
         x0 <span style="color: #333333;">=</span> a<span style="color: #333333;">*</span>rho
         y0 <span style="color: #333333;">=</span> b<span style="color: #333333;">*</span>rho
         x1 <span style="color: #333333;">=</span> <span style="color: #007020;">int</span>(x0 <span style="color: #333333;">+</span> <span style="color: #0000dd; font-weight: bold;">1000</span><span style="color: #333333;">*</span>(<span style="color: #333333;">-</span>b))
         y1 <span style="color: #333333;">=</span> <span style="color: #007020;">int</span>(y0 <span style="color: #333333;">+</span> <span style="color: #0000dd; font-weight: bold;">1000</span><span style="color: #333333;">*</span>(a))
         x2 <span style="color: #333333;">=</span> <span style="color: #007020;">int</span>(x0 <span style="color: #333333;">-</span> <span style="color: #0000dd; font-weight: bold;">1000</span><span style="color: #333333;">*</span>(<span style="color: #333333;">-</span>b))
         y2 <span style="color: #333333;">=</span> <span style="color: #007020;">int</span>(y0 <span style="color: #333333;">-</span> <span style="color: #0000dd; font-weight: bold;">1000</span><span style="color: #333333;">*</span>(a))
         <span style="color: #888888;"># If b &gt; 0.5, the angle must be greater than 45 degree</span>
         <span style="color: #888888;"># so we consider that line as a vertical line</span>
         <span style="color: #008800; font-weight: bold;">if</span> (b<span style="color: #333333;">&gt;</span><span style="color: #6600ee; font-weight: bold;">0.5</span>):
          <span style="color: #888888;"># Check the position</span>
          <span style="color: #008800; font-weight: bold;">if</span>(rho<span style="color: #333333;">-</span>pos_hori<span style="color: #333333;">&gt;</span><span style="color: #0000dd; font-weight: bold;">10</span>):
           <span style="color: #888888;"># Update the position</span>
           pos_hori<span style="color: #333333;">=</span>rho
           <span style="color: #888888;"># Saving new line, 0 is horizontal line, 1 is vertical line</span>
           New_lines<span style="color: #333333;">.</span>append([rho,theta, <span style="color: #0000dd; font-weight: bold;">0</span>])
         <span style="color: #008800; font-weight: bold;">else</span>:
          <span style="color: #008800; font-weight: bold;">if</span>(rho<span style="color: #333333;">-</span>pos_vert<span style="color: #333333;">&gt;</span><span style="color: #0000dd; font-weight: bold;">10</span>):
           pos_vert<span style="color: #333333;">=</span>rho
           New_lines<span style="color: #333333;">.</span>append([rho,theta, <span style="color: #0000dd; font-weight: bold;">1</span>])
        <span style="color: #008800; font-weight: bold;">for</span> i <span style="color: black; font-weight: bold;">in</span> <span style="color: #007020;">range</span>(<span style="color: #007020;">len</span>(New_lines)):
            <span style="color: #008800; font-weight: bold;">if</span>(New_lines[i][<span style="color: #0000dd; font-weight: bold;">2</span>] <span style="color: #333333;">==</span> <span style="color: #0000dd; font-weight: bold;">0</span>):
                <span style="color: #008800; font-weight: bold;">for</span> j <span style="color: black; font-weight: bold;">in</span> <span style="color: #007020;">range</span>(<span style="color: #007020;">len</span>(New_lines)):
                    <span style="color: #008800; font-weight: bold;">if</span> (New_lines[j][<span style="color: #0000dd; font-weight: bold;">2</span>]<span style="color: #333333;">==</span><span style="color: #0000dd; font-weight: bold;">1</span>):
                        theta1<span style="color: #333333;">=</span>New_lines[i][<span style="color: #0000dd; font-weight: bold;">1</span>]
                        theta2<span style="color: #333333;">=</span>New_lines[j][<span style="color: #0000dd; font-weight: bold;">1</span>]
                        p1<span style="color: #333333;">=</span>New_lines[i][<span style="color: #0000dd; font-weight: bold;">0</span>]
                        p2<span style="color: #333333;">=</span>New_lines[j][<span style="color: #0000dd; font-weight: bold;">0</span>]
                        xy <span style="color: #333333;">=</span> np<span style="color: #333333;">.</span>array([[np<span style="color: #333333;">.</span>cos(theta1), np<span style="color: #333333;">.</span>sin(theta1)], [np<span style="color: #333333;">.</span>cos(theta2), np<span style="color: #333333;">.</span>sin(theta2)]])
                        p <span style="color: #333333;">=</span> np<span style="color: #333333;">.</span>array([p1,p2])
                        res <span style="color: #333333;">=</span> np<span style="color: #333333;">.</span>linalg<span style="color: #333333;">.</span>solve(xy, p)
                        Points<span style="color: #333333;">.</span>append(res)
        <span style="color: #008800; font-weight: bold;">if</span>(<span style="color: #007020;">len</span>(Points)<span style="color: #333333;">==</span><span style="color: #0000dd; font-weight: bold;">100</span>):
            sudoku1 <span style="color: #333333;">=</span> cv2<span style="color: #333333;">.</span>adaptiveThreshold(sudoku1, <span style="color: #0000dd; font-weight: bold;">255</span>, cv2<span style="color: #333333;">.</span>ADAPTIVE_THRESH_GAUSSIAN_C,cv2<span style="color: #333333;">.</span>THRESH_BINARY_INV, <span style="color: #0000dd; font-weight: bold;">101</span>, <span style="color: #0000dd; font-weight: bold;">1</span>)
            <span style="color: #008800; font-weight: bold;">for</span> i <span style="color: black; font-weight: bold;">in</span> <span style="color: #007020;">range</span>(<span style="color: #0000dd; font-weight: bold;">0</span>,<span style="color: #0000dd; font-weight: bold;">9</span>):
                <span style="color: #008800; font-weight: bold;">for</span> j <span style="color: black; font-weight: bold;">in</span> <span style="color: #007020;">range</span>(<span style="color: #0000dd; font-weight: bold;">0</span>,<span style="color: #0000dd; font-weight: bold;">9</span>):
                    y1<span style="color: #333333;">=</span><span style="color: #007020;">int</span>(Points[j<span style="color: #333333;">+</span>i<span style="color: #333333;">*</span><span style="color: #0000dd; font-weight: bold;">10</span>][<span style="color: #0000dd; font-weight: bold;">1</span>]<span style="color: #333333;">+</span><span style="color: #0000dd; font-weight: bold;">5</span>)
                    y2<span style="color: #333333;">=</span><span style="color: #007020;">int</span>(Points[j<span style="color: #333333;">+</span>i<span style="color: #333333;">*</span><span style="color: #0000dd; font-weight: bold;">10</span><span style="color: #333333;">+</span><span style="color: #0000dd; font-weight: bold;">11</span>][<span style="color: #0000dd; font-weight: bold;">1</span>]<span style="color: #333333;">-</span><span style="color: #0000dd; font-weight: bold;">5</span>)
                    x1<span style="color: #333333;">=</span><span style="color: #007020;">int</span>(Points[j<span style="color: #333333;">+</span>i<span style="color: #333333;">*</span><span style="color: #0000dd; font-weight: bold;">10</span>][<span style="color: #0000dd; font-weight: bold;">0</span>]<span style="color: #333333;">+</span><span style="color: #0000dd; font-weight: bold;">5</span>)
                    x2<span style="color: #333333;">=</span><span style="color: #007020;">int</span>(Points[j<span style="color: #333333;">+</span>i<span style="color: #333333;">*</span><span style="color: #0000dd; font-weight: bold;">10</span><span style="color: #333333;">+</span><span style="color: #0000dd; font-weight: bold;">11</span>][<span style="color: #0000dd; font-weight: bold;">0</span>]<span style="color: #333333;">-</span><span style="color: #0000dd; font-weight: bold;">5</span>)
                    <span style="color: #888888;"># Saving extracted block for training, uncomment for saving digit blocks</span>
                    <span style="color: #888888;"># cv2.imwrite(str((i+1)*(j+1))+".jpg", sudoku1[y1: y2,</span>
                    <span style="color: #888888;">#                                            x1: x2])</span>
                    cv2<span style="color: #333333;">.</span>rectangle(frame,(x1,y1),
                                  (x2, y2),(<span style="color: #0000dd; font-weight: bold;">0</span>,<span style="color: #0000dd; font-weight: bold;">255</span>,<span style="color: #0000dd; font-weight: bold;">0</span>),<span style="color: #0000dd; font-weight: bold;">2</span>)
    <span style="color: #888888;"># Show the result        </span>
    cv2<span style="color: #333333;">.</span>imshow(<span style="background-color: #fff0f0;">"SUDOKU Solver"</span>, frame)
    rval, frame <span style="color: #333333;">=</span> vc<span style="color: #333333;">.</span>read()
    key <span style="color: #333333;">=</span> cv2<span style="color: #333333;">.</span>waitKey(<span style="color: #0000dd; font-weight: bold;">20</span>)
    <span style="color: #008800; font-weight: bold;">if</span> key <span style="color: #333333;">==</span> <span style="color: #0000dd; font-weight: bold;">27</span>: <span style="color: #888888;"># exit on ESC</span>
     <span style="color: #008800; font-weight: bold;">break</span>
vc<span style="color: #333333;">.</span>release()
cv2<span style="color: #333333;">.</span>destroyAllWindows()
</pre>
</div>
To make sure that we extract the correct digit blocks, I set the condition which when the number of intersection points is equal to 100, we start to extract the digits. You can check out the above code.

Bravo! Now we know how to extract digit number block from SUDOKU board. In the next article, we will recognize those digit numbers with Support Vector Machine algorithm :)
