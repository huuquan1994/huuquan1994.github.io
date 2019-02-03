---
layout: post
title: "Building a simple SUDOKU Solver from scratch - Part 2: Digit Number Recognition using SVM"
---

In this post, we will continue the previous tutorial on recognizing digital number were extracted from SUDOKU board. [**See part 1**](https://caphuuquan.com/sudoku-solver-part-1-grid-detection/)  
In part 1 of this series, we learned how to detect lines and extract the digit numbers from the SUDOKU board. It's easy to extract digit numbers and save it as images (Uncomment the code from Part 1 to save your own digit blocks). Now, let's talk about training those extracted digital numbers with SVM

With the code from Part 1, we can easily save more than 1,000 digit images within a second.

<p align="center">
<img src="https://1.bp.blogspot.com/-A4jHmyrZBS8/WPyn_E5gvvI/AAAAAAAADZU/i1MSc47MAigk4veC7ORSbnQDggWlu4BDACLcB/s640/2017-04-23_221104.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 1. The extracted digit images</span>
</p>
To train the dataset with a supervised machine learning method like SVM, we need to label the dataset beforehand. With this dataset, we have 10 labels (from 0 -> 9). So, the idea is that we will create 10 folders with the name from 0 to 9 and put those images into the corresponding folder.  
It's time-consuming because we have a lot of images :(  
Don't worry! I already created a labeled dataset for you. Check out my GitHub repository to download the dataset: [https://github.com/huuquan1994/Sudoku-Solver](https://github.com/huuquan1994/Sudoku-Solver)  
I divided it into 2 parts, training set, and testing set. 5000 images in the training set (500 images in each folder) and ~3000 test images in the testing set.

<p align="center">
<img src="https://2.bp.blogspot.com/-yoOnN8P8Pwk/WPyu4T5czfI/AAAAAAAADZk/iSSV3y3ff3U9gZBbYTXz4OFyMpaefqhQACLcB/s640/2017-04-23_223515.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 2. We need to create 10 folders to store the images</span>
</p>

<p align="center">
<img src="https://1.bp.blogspot.com/-ZisrNO8LS4g/WPyvuJ79RKI/AAAAAAAADZs/i4kmsM62QR8eaFTxKgA6jRrqHdPb4X9cgCLcB/s640/2017-04-23_224413.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 3. Folder with the images labeled "1"</span>
</p>
We have all we need! Now, let's train SVM to recognize digital number.
We will use LinearSVM in Sklearn to train the dataset, using **`joblib`** to save the SVM model after training.
For training, first, we resize the size of each training image to $$36\times36$$. After that, we train those resized images with LinearSVM. You can see the code below
