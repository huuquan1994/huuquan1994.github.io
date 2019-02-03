---
layout: post
title: "Building a simple SUDOKU Solver from scratch - Part 3: Putting in together"
---

In part 2 of this series ([See part 2](https://caphuuquan.com/sudoku-solver-part-2-train-SVM/)), we've discussed how to recognize number from each cell in SUDOKU grid. With the help of SVM, we can easily recognize the digit number from SUDOKU image. Base on that recognition result, the SUDOKU grid can be solved by using the backtracking method.

First, let's reform the recognition result to the matrix for the easier intuitive.
The recognition result of a SUDOKU picture will be formed as a matrix like an example below.

<p align="center">
<img src="https://3.bp.blogspot.com/-Nwh6i8Vj8ig/WY8auEITopI/AAAAAAAAEP4/Lgqff5gFoAIofWvm1iSaQMkTTQQuae8jwCLcBGAs/s400/WIN_20170813_00_09_42_Pro.jpg">
</p>

[0  9  7  0  5  0  2  1  0]  
[0  0  0  0  8  0  1  0  0]  
[0  0  2  0  9  7  0  6  3]  
[0  6  0  0  0  0  3  0  9]  
[3  0  0  4  1  9  0  0  6]  
[7  0  9  0  0  0  0  2  0]  
[8  3  0  2  4  0  9  0  0]  
[0  0  6  0  3  0  0  0  0]  
[0  5  4  0  7  0  6  3  0]  
We assume the matrix of result after recognition (0 means the cell is empty)
{: style="text-align: center"}

We now have the "raw" recognition result as a matrix. The easiest solution to solve SUDOKU matrix is using the Backtracking algorithm.
The SUDOKU can be solved by assigning every number to an empty cell. We check whether if it is safe to assign that number. After checking, we recursively check whether this assignment leads to a solution or not. If the assignment doesn’t lead to a solution, then we try the next number for current empty cell.

This post used the idea from [http://www.geeksforgeeks.org/backtracking-set-7-suduku/](http://www.geeksforgeeks.org/backtracking-set-7-suduku/) to solve the SUDOKU problem.  
Here is the code for solving SUDOKU problem

<!-- HTML generated using hilite.me --><div style="background: #f8f8f8; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">numpy</span> <span style="color: #008000; font-weight: bold">as</span> <span style="color: #0000FF; font-weight: bold">np</span>
<span style="color: #408080; font-style: italic">#Finding unsigned cell</span>
<span style="color: #408080; font-style: italic">#File name: SUDOKU.py</span>
<span style="color: #008000; font-weight: bold">def</span> <span style="color: #0000FF">FindUnsignedLocation</span>(Board, l):
    <span style="color: #008000; font-weight: bold">for</span> row <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">9</span>):
        <span style="color: #008000; font-weight: bold">for</span> col <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">9</span>):
            <span style="color: #008000; font-weight: bold">if</span> (Board[row][col] <span style="color: #666666">==</span> <span style="color: #666666">0</span>):
                l[<span style="color: #666666">0</span>] <span style="color: #666666">=</span> row
                l[<span style="color: #666666">1</span>] <span style="color: #666666">=</span> col
                <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">True</span>
    <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">False</span>

<span style="color: #408080; font-style: italic"># Check the safety of each box in a row</span>
<span style="color: #008000; font-weight: bold">def</span> <span style="color: #0000FF">InRow</span>(Board, row, num):
    <span style="color: #008000; font-weight: bold">for</span> i <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">9</span>):
        <span style="color: #008000; font-weight: bold">if</span> (Board[row][i] <span style="color: #666666">==</span> num):
            <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">True</span>
    <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">False</span>

<span style="color: #408080; font-style: italic"># Check the safety of each box in a column</span>
<span style="color: #008000; font-weight: bold">def</span> <span style="color: #0000FF">InCol</span>(Board, col, num):
    <span style="color: #008000; font-weight: bold">for</span> i <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">9</span>):
        <span style="color: #008000; font-weight: bold">if</span> (Board[i][col] <span style="color: #666666">==</span> num):
            <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">True</span>
    <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">False</span>

<span style="color: #408080; font-style: italic"># Check the safety of a 3x3 box</span>
<span style="color: #008000; font-weight: bold">def</span> <span style="color: #0000FF">InBox</span>(Board, row, col, num):
    <span style="color: #008000; font-weight: bold">for</span> i <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">3</span>):
        <span style="color: #008000; font-weight: bold">for</span> j <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">3</span>):
            <span style="color: #008000; font-weight: bold">if</span> (Board[i <span style="color: #666666">+</span> row][j <span style="color: #666666">+</span> col] <span style="color: #666666">==</span> num):
                <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">True</span>
    <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">False</span>

<span style="color: #408080; font-style: italic"># Check the safety of a position</span>
<span style="color: #008000; font-weight: bold">def</span> <span style="color: #0000FF">isSafe</span>(Board, row, col, num):
    <span style="color: #008000; font-weight: bold">return</span> <span style="color: #AA22FF; font-weight: bold">not</span> InCol(Board, col, num) <span style="color: #AA22FF; font-weight: bold">and</span> <span style="color: #AA22FF; font-weight: bold">not</span> InRow(Board, row, num) <span style="color: #AA22FF; font-weight: bold">and</span> <span style="color: #AA22FF; font-weight: bold">not</span> InBox(Board, row <span style="color: #666666">-</span> row <span style="color: #666666">%</span> <span style="color: #666666">3</span>, col <span style="color: #666666">-</span> col <span style="color: #666666">%</span> <span style="color: #666666">3</span>, num)

<span style="color: #008000; font-weight: bold">def</span> <span style="color: #0000FF">SolveSudoku</span>(Board):
    l<span style="color: #666666">=</span>[<span style="color: #666666">0</span>,<span style="color: #666666">0</span>]
    <span style="color: #008000; font-weight: bold">if</span> (<span style="color: #AA22FF; font-weight: bold">not</span> FindUnsignedLocation(Board, l)):
        <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">True</span>
    row <span style="color: #666666">=</span> l[<span style="color: #666666">0</span>]
    col <span style="color: #666666">=</span> l[<span style="color: #666666">1</span>]
    <span style="color: #008000; font-weight: bold">for</span> num <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">1</span>,<span style="color: #666666">10</span>):
        <span style="color: #008000; font-weight: bold">if</span> (isSafe(Board, row, col, num)):
            Board[row][col] <span style="color: #666666">=</span> num
            <span style="color: #008000; font-weight: bold">if</span> (SolveSudoku(Board)):
                <span style="color: #008000">print</span> Board
                <span style="color: #008000; font-weight: bold">break</span>
            Board[row][col] <span style="color: #666666">=</span> <span style="color: #666666">0</span>
    <span style="color: #008000; font-weight: bold">return</span> <span style="color: #008000; font-weight: bold">False</span>

Board <span style="color: #666666">=</span> [[<span style="color: #666666">0</span>, <span style="color: #666666">9</span>, <span style="color: #666666">7</span>, <span style="color: #666666">0</span>, <span style="color: #666666">5</span>, <span style="color: #666666">0</span>, <span style="color: #666666">2</span>, <span style="color: #666666">1</span>, <span style="color: #666666">0</span>],
 [<span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">8</span>, <span style="color: #666666">0</span>, <span style="color: #666666">4</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>],
 [<span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">2</span>, <span style="color: #666666">0</span>, <span style="color: #666666">9</span>, <span style="color: #666666">7</span>, <span style="color: #666666">0</span>, <span style="color: #666666">6</span>, <span style="color: #666666">3</span>],
 [<span style="color: #666666">0</span>, <span style="color: #666666">6</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">3</span>, <span style="color: #666666">0</span>, <span style="color: #666666">9</span>],
 [<span style="color: #666666">3</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">4</span>, <span style="color: #666666">1</span>, <span style="color: #666666">9</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">6</span>],
 [<span style="color: #666666">7</span>, <span style="color: #666666">0</span>, <span style="color: #666666">9</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">2</span>, <span style="color: #666666">0</span>],
 [<span style="color: #666666">8</span>, <span style="color: #666666">3</span>, <span style="color: #666666">0</span>, <span style="color: #666666">2</span>, <span style="color: #666666">4</span>, <span style="color: #666666">0</span>, <span style="color: #666666">9</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>],
 [<span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">6</span>, <span style="color: #666666">0</span>, <span style="color: #666666">3</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>],
 [<span style="color: #666666">0</span>, <span style="color: #666666">5</span>, <span style="color: #666666">4</span>, <span style="color: #666666">0</span>, <span style="color: #666666">7</span>, <span style="color: #666666">0</span>, <span style="color: #666666">6</span>, <span style="color: #666666">3</span>, <span style="color: #666666">0</span>]]

SolveSudoku(Board)
</pre></div>

We use the $$9x9$$ matrix as an input of the above code. You can test with your own matrix with the above code to see the code runs properly.

Combining with the code from Part 2, we can now solve the SUDOKU and show the result like this.

<p align="center">
<img src="https://2.bp.blogspot.com/-cvgsg8usRyE/WY8kN7bFcHI/AAAAAAAAEQI/R5XHEH2hpQQ-druz_SA5J5YmPCvZBxLOACLcBGAs/s400/2017-08-13_005131.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 1. Result of the final SUDOKU Solver</span>
</p>

Here is the final code

<!-- HTML generated using hilite.me --><div style="background: #f8f8f8; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #408080; font-style: italic">## Simple SUDOKU Solver for OpenCV 2.4.13</span>
<span style="color: #408080; font-style: italic">## Visit this link for the version with OpenCV 3.x: https://github.com/huuquan1994/Sudoku-Solver/blob/master/Main_SUDOKU_OpenCV3.py</span>

<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">cv2</span>
<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">numpy</span> <span style="color: #008000; font-weight: bold">as</span> <span style="color: #0000FF; font-weight: bold">np</span>
<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">joblib</span>
<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">SUDOKU</span>

font <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>FONT_HERSHEY_SIMPLEX
ratio2 <span style="color: #666666">=</span> <span style="color: #666666">3</span>
kernel_size <span style="color: #666666">=</span> <span style="color: #666666">3</span>
lowThreshold <span style="color: #666666">=</span> <span style="color: #666666">30</span>
count <span style="color: #666666">=</span> <span style="color: #666666">1</span>
<span style="color: #408080; font-style: italic"># Load the pre-trained SVM model.</span>
<span style="color: #408080; font-style: italic"># Please note that you will need to train a new classifier if you run this code under Python 3.x</span>
clf <span style="color: #666666">=</span> joblib<span style="color: #666666">.</span>load(<span style="color: #BA2121">&#39;classifier.pkl&#39;</span>)

is_print <span style="color: #666666">=</span> <span style="color: #008000; font-weight: bold">True</span>

cv2<span style="color: #666666">.</span>namedWindow(<span style="color: #BA2121">&quot;SUDOKU Solver&quot;</span>)
vc <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>VideoCapture(<span style="color: #666666">0</span>)
<span style="color: #008000; font-weight: bold">if</span> vc<span style="color: #666666">.</span>isOpened(): <span style="color: #408080; font-style: italic"># try to get the first frame</span>
    rval, frame <span style="color: #666666">=</span> vc<span style="color: #666666">.</span>read()
<span style="color: #008000; font-weight: bold">else</span>:
    rval <span style="color: #666666">=</span> <span style="color: #008000; font-weight: bold">False</span>
<span style="color: #008000; font-weight: bold">while</span> rval:
    sudoku1 <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>cvtColor(frame, cv2<span style="color: #666666">.</span>COLOR_BGR2GRAY)
    sudoku1 <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>blur(sudoku1, (<span style="color: #666666">1</span>,<span style="color: #666666">1</span>))
    edges <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>Canny(sudoku1, lowThreshold, lowThreshold<span style="color: #666666">*</span>ratio2, kernel_size)
    lines <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>HoughLines(edges, <span style="color: #666666">2</span>, cv2<span style="color: #666666">.</span>cv<span style="color: #666666">.</span>CV_PI <span style="color: #666666">/180</span>, <span style="color: #666666">300</span>, <span style="color: #666666">0</span>, <span style="color: #666666">0</span>)
    <span style="color: #008000; font-weight: bold">if</span> (lines <span style="color: #AA22FF; font-weight: bold">is</span> <span style="color: #AA22FF; font-weight: bold">not</span> <span style="color: #008000; font-weight: bold">None</span>):
        lines <span style="color: #666666">=</span> lines[<span style="color: #666666">0</span>]
        lines <span style="color: #666666">=</span> <span style="color: #008000">sorted</span>(lines, key<span style="color: #666666">=</span><span style="color: #008000; font-weight: bold">lambda</span> line:line[<span style="color: #666666">0</span>])
        diff_ngang <span style="color: #666666">=</span> <span style="color: #666666">0</span>
        diff_doc <span style="color: #666666">=</span> <span style="color: #666666">0</span>
        lines_1<span style="color: #666666">=</span>[]
        Points<span style="color: #666666">=</span>[]
        <span style="color: #008000; font-weight: bold">for</span> rho,theta <span style="color: #AA22FF; font-weight: bold">in</span> lines:
            a <span style="color: #666666">=</span> np<span style="color: #666666">.</span>cos(theta)
            b <span style="color: #666666">=</span> np<span style="color: #666666">.</span>sin(theta)
            x0 <span style="color: #666666">=</span> a<span style="color: #666666">*</span>rho
            y0 <span style="color: #666666">=</span> b<span style="color: #666666">*</span>rho
            x1 <span style="color: #666666">=</span> <span style="color: #008000">int</span>(x0 <span style="color: #666666">+</span> <span style="color: #666666">1000*</span>(<span style="color: #666666">-</span>b))
            y1 <span style="color: #666666">=</span> <span style="color: #008000">int</span>(y0 <span style="color: #666666">+</span> <span style="color: #666666">1000*</span>(a))
            x2 <span style="color: #666666">=</span> <span style="color: #008000">int</span>(x0 <span style="color: #666666">-</span> <span style="color: #666666">1000*</span>(<span style="color: #666666">-</span>b))
            y2 <span style="color: #666666">=</span> <span style="color: #008000">int</span>(y0 <span style="color: #666666">-</span> <span style="color: #666666">1000*</span>(a))
            <span style="color: #008000; font-weight: bold">if</span> (b<span style="color: #666666">&gt;0.5</span>):
                <span style="color: #008000; font-weight: bold">if</span>(rho<span style="color: #666666">-</span>diff_ngang<span style="color: #666666">&gt;10</span>):
                    diff_ngang<span style="color: #666666">=</span>rho
                    lines_1<span style="color: #666666">.</span>append([rho,theta, <span style="color: #666666">0</span>])
            <span style="color: #008000; font-weight: bold">else</span>:
                <span style="color: #008000; font-weight: bold">if</span>(rho<span style="color: #666666">-</span>diff_doc<span style="color: #666666">&gt;10</span>):
                    diff_doc<span style="color: #666666">=</span>rho
                    lines_1<span style="color: #666666">.</span>append([rho,theta, <span style="color: #666666">1</span>])
        <span style="color: #008000; font-weight: bold">for</span> i <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #008000">len</span>(lines_1)):
            <span style="color: #008000; font-weight: bold">if</span>(lines_1[i][<span style="color: #666666">2</span>] <span style="color: #666666">==</span> <span style="color: #666666">0</span>):
                <span style="color: #008000; font-weight: bold">for</span> j <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #008000">len</span>(lines_1)):
                    <span style="color: #008000; font-weight: bold">if</span> (lines_1[j][<span style="color: #666666">2</span>]<span style="color: #666666">==1</span>):
                        theta1<span style="color: #666666">=</span>lines_1[i][<span style="color: #666666">1</span>]
                        theta2<span style="color: #666666">=</span>lines_1[j][<span style="color: #666666">1</span>]
                        p1<span style="color: #666666">=</span>lines_1[i][<span style="color: #666666">0</span>]
                        p2<span style="color: #666666">=</span>lines_1[j][<span style="color: #666666">0</span>]
                        xy <span style="color: #666666">=</span> np<span style="color: #666666">.</span>array([[np<span style="color: #666666">.</span>cos(theta1), np<span style="color: #666666">.</span>sin(theta1)], [np<span style="color: #666666">.</span>cos(theta2), np<span style="color: #666666">.</span>sin(theta2)]])
                        p <span style="color: #666666">=</span> np<span style="color: #666666">.</span>array([p1,p2])
                        res <span style="color: #666666">=</span> np<span style="color: #666666">.</span>linalg<span style="color: #666666">.</span>solve(xy, p)
                        Points<span style="color: #666666">.</span>append(res)

        <span style="color: #008000; font-weight: bold">if</span>(<span style="color: #008000">len</span>(Points)<span style="color: #666666">==100</span>):
            result <span style="color: #666666">=</span> []
            board <span style="color: #666666">=</span> []
            sudoku1 <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>adaptiveThreshold(sudoku1, <span style="color: #666666">255</span>, cv2<span style="color: #666666">.</span>ADAPTIVE_THRESH_GAUSSIAN_C,cv2<span style="color: #666666">.</span>THRESH_BINARY_INV, <span style="color: #666666">101</span>, <span style="color: #666666">1</span>)
            <span style="color: #008000; font-weight: bold">for</span> i <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">9</span>):
                <span style="color: #008000; font-weight: bold">for</span> j <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">9</span>):
                    y1<span style="color: #666666">=</span><span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10</span>][<span style="color: #666666">1</span>]<span style="color: #666666">+5</span>)
                    y2<span style="color: #666666">=</span><span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10+11</span>][<span style="color: #666666">1</span>]<span style="color: #666666">-5</span>)
                    x1<span style="color: #666666">=</span><span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10</span>][<span style="color: #666666">0</span>]<span style="color: #666666">+5</span>)
                    x2<span style="color: #666666">=</span><span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10+11</span>][<span style="color: #666666">0</span>]<span style="color: #666666">-5</span>)
                    X <span style="color: #666666">=</span> sudoku1[y1:y2,x1:x2]
                    <span style="color: #008000; font-weight: bold">if</span>(X<span style="color: #666666">.</span>size<span style="color: #666666">!=0</span>):
                        X <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>resize(X, (<span style="color: #666666">36</span>,<span style="color: #666666">36</span>))
                        num <span style="color: #666666">=</span> clf<span style="color: #666666">.</span>predict(np<span style="color: #666666">.</span>reshape(X, (<span style="color: #666666">1</span>,<span style="color: #666666">-1</span>)))
                        
                        <span style="color: #408080; font-style: italic">#Collect the result</span>
                        result<span style="color: #666666">.</span>append(num)
                        board<span style="color: #666666">.</span>append(num)

            <span style="color: #408080; font-style: italic"># Reshape to 9x9 matrix</span>
            result <span style="color: #666666">=</span> np<span style="color: #666666">.</span>reshape(result, (<span style="color: #666666">9</span>,<span style="color: #666666">9</span>))
            board <span style="color: #666666">=</span> np<span style="color: #666666">.</span>reshape(board, (<span style="color: #666666">9</span>,<span style="color: #666666">9</span>))
            
            <span style="color: #408080; font-style: italic"># Solve the SUDOKU grid</span>
            <span style="color: #008000; font-weight: bold">if</span>(SUDOKU<span style="color: #666666">.</span>SolveSudoku(result)):
                <span style="color: #408080; font-style: italic"># If it can solve SUDOKU matrix, show the result</span>
                <span style="color: #008000; font-weight: bold">for</span> i <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">9</span>):
                    <span style="color: #008000; font-weight: bold">for</span> j <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">9</span>):
                        <span style="color: #008000; font-weight: bold">if</span>(array[i][j]<span style="color: #666666">==0</span>):
                            cv2<span style="color: #666666">.</span>putText(frame,<span style="color: #008000">str</span>(result[i][j]),(<span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10+10</span>][<span style="color: #666666">0</span>]<span style="color: #666666">+15</span>), 
                                                                 <span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10+10</span>][<span style="color: #666666">1</span>]<span style="color: #666666">-10</span>)),font,<span style="color: #666666">1</span>,(<span style="color: #666666">225</span>,<span style="color: #666666">0</span>,<span style="color: #666666">0</span>),<span style="color: #666666">2</span>)
                <span style="color: #408080; font-style: italic"># Show the result picture</span>
                cv2<span style="color: #666666">.</span>imshow(<span style="color: #BA2121">&quot;Result&quot;</span>, frame)
                
    cv2<span style="color: #666666">.</span>imshow(<span style="color: #BA2121">&quot;SUDOKU Solver&quot;</span>, frame)
    rval, frame <span style="color: #666666">=</span> vc<span style="color: #666666">.</span>read()
    key <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>waitKey(<span style="color: #666666">20</span>)
    <span style="color: #008000; font-weight: bold">if</span> key <span style="color: #666666">==</span> <span style="color: #666666">27</span>: <span style="color: #408080; font-style: italic"># exit on ESC</span>
        <span style="color: #008000; font-weight: bold">break</span>
vc<span style="color: #666666">.</span>release()
cv2<span style="color: #666666">.</span>destroyAllWindows()
</pre></div>

Here is the end of this SUDOKU Solver series.
It's just the simple SUDOKU solver from taken images. I hope that you can improve this code or think about the new ideas.

If you have any questions or comments, please feel free to contact me by email.
All the training data and source code can be found in this GitHub link: [https://github.com/huuquan1994/Sudoku-Solver](https://github.com/huuquan1994/Sudoku-Solver)

Enjoy your time!  
Quan
