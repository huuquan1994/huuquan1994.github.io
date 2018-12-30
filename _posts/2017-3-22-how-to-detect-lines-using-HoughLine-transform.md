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
A line can be represented as \(ρ=xcos\theta+ysin\theta\) where ρ is the perpendicular distance from the origin to the line, and θ is the angle formed by this perpendicular line and horizontal axis. That direction varies on how you represent the coordinate system. This representation above is used in OpenCV. Any vertical line will have 0 degree and horizontal lines will have 90 degrees of θ. Normally, the origin point will be the top-left point in the image so the maximum θ is 90 degrees and minimum θ is 0 degree.
