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

<!-- HTML generated using hilite.me --><div style="background: #f8f8f8; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">numpy</span> <span style="color: #008000; font-weight: bold">as</span> <span style="color: #0000FF; font-weight: bold">np</span>
<span style="color: #008000; font-weight: bold">from</span> <span style="color: #0000FF; font-weight: bold">sklearn.svm</span> <span style="color: #008000; font-weight: bold">import</span> LinearSVC
<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">os</span>
<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">cv2</span>
<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">joblib</span>

<span style="color: #408080; font-style: italic"># Generate training set</span>
TRAIN_PATH <span style="color: #666666">=</span> <span style="color: #BA2121">&quot;Dataset\Train&quot;</span>
list_folder <span style="color: #666666">=</span> os<span style="color: #666666">.</span>listdir(TRAIN_PATH)
trainset <span style="color: #666666">=</span> []
<span style="color: #008000; font-weight: bold">for</span> folder <span style="color: #AA22FF; font-weight: bold">in</span> list_folder:
    flist <span style="color: #666666">=</span> os<span style="color: #666666">.</span>listdir(os<span style="color: #666666">.</span>path<span style="color: #666666">.</span>join(TRAIN_PATH, folder))
    <span style="color: #008000; font-weight: bold">for</span> f <span style="color: #AA22FF; font-weight: bold">in</span> flist:
        im <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>imread(os<span style="color: #666666">.</span>path<span style="color: #666666">.</span>join(TRAIN_PATH, folder, f))
        im <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>cvtColor(im, cv2<span style="color: #666666">.</span>COLOR_RGB2GRAY )
        im <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>resize(im, (<span style="color: #666666">36</span>,<span style="color: #666666">36</span>))
        trainset<span style="color: #666666">.</span>append(im)
<span style="color: #408080; font-style: italic"># Labeling for trainset</span>
train_label <span style="color: #666666">=</span> []
<span style="color: #008000; font-weight: bold">for</span> i <span style="color: #AA22FF; font-weight: bold">in</span> <span style="color: #008000">range</span>(<span style="color: #666666">0</span>,<span style="color: #666666">10</span>):
    temp <span style="color: #666666">=</span> <span style="color: #666666">500*</span>[i]
    train_label <span style="color: #666666">+=</span> temp

<span style="color: #408080; font-style: italic"># Generate testing set</span>
TEST_PATH <span style="color: #666666">=</span> <span style="color: #BA2121">&quot;Dataset\Test&quot;</span>
list_folder <span style="color: #666666">=</span> os<span style="color: #666666">.</span>listdir(TEST_PATH)
testset <span style="color: #666666">=</span> []
test_label <span style="color: #666666">=</span> []
<span style="color: #008000; font-weight: bold">for</span> folder <span style="color: #AA22FF; font-weight: bold">in</span> list_folder:
    flist <span style="color: #666666">=</span> os<span style="color: #666666">.</span>listdir(os<span style="color: #666666">.</span>path<span style="color: #666666">.</span>join(TEST_PATH, folder))
    <span style="color: #008000; font-weight: bold">for</span> f <span style="color: #AA22FF; font-weight: bold">in</span> flist:
        im <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>imread(os<span style="color: #666666">.</span>path<span style="color: #666666">.</span>join(TEST_PATH, folder, f))
        im <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>cvtColor(im, cv2<span style="color: #666666">.</span>COLOR_RGB2GRAY )
        im <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>resize(im, (<span style="color: #666666">36</span>,<span style="color: #666666">36</span>))
        testset<span style="color: #666666">.</span>append(im)
        test_label<span style="color: #666666">.</span>append(<span style="color: #008000">int</span>(folder))
trainset <span style="color: #666666">=</span> np<span style="color: #666666">.</span>reshape(trainset, (<span style="color: #666666">5000</span>, <span style="color: #666666">-1</span>))

<span style="color: #408080; font-style: italic"># Create an linear SVM object</span>
clf <span style="color: #666666">=</span> LinearSVC()

<span style="color: #408080; font-style: italic"># Perform the training</span>
clf<span style="color: #666666">.</span>fit(trainset, train_label)
<span style="color: #008000">print</span>(<span style="color: #BA2121">&quot;Training finished successfully&quot;</span>)

<span style="color: #408080; font-style: italic"># Testing</span>
testset <span style="color: #666666">=</span> np<span style="color: #666666">.</span>reshape(testset, (<span style="color: #008000">len</span>(testset), <span style="color: #666666">-1</span>))
y <span style="color: #666666">=</span> clf<span style="color: #666666">.</span>predict(testset)
<span style="color: #008000">print</span>(<span style="color: #BA2121">&quot;Testing accuracy: &quot;</span> <span style="color: #666666">+</span> <span style="color: #008000">str</span>(clf<span style="color: #666666">.</span>score(testset, test_label)))

joblib<span style="color: #666666">.</span>dump(clf, <span style="color: #BA2121">&quot;classifier.pkl&quot;</span>, compress<span style="color: #666666">=3</span>)
</pre></div>

After training, we will have an SVM model named: **`classifier.pkl`**.  
We can use this file to recognize block images.

<p align="center">
<img src="https://1.bp.blogspot.com/-TeXbaBArSLY/WPy5QqmjgxI/AAAAAAAADZ8/Ly8Dfl_rQI8h-QCnQur21Cze7aeBn3iRACLcB/s640/2017-04-23_232431.jpg">
</p>
<p align="center">
<span style="color: #fffff; font-family: Helvetica; font-size: 9pt;">Fig. 4. Result of recoginited digits from SVM</span>
</p>

See this code for more details:

<!-- HTML generated using hilite.me --><div style="background: #f8f8f8; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">cv2</span>
<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">numpy</span> <span style="color: #008000; font-weight: bold">as</span> <span style="color: #0000FF; font-weight: bold">np</span>
<span style="color: #008000; font-weight: bold">import</span> <span style="color: #0000FF; font-weight: bold">joblib</span>

font <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>FONT_HERSHEY_SIMPLEX
ratio2 <span style="color: #666666">=</span> <span style="color: #666666">3</span>
kernel_size <span style="color: #666666">=</span> <span style="color: #666666">3</span>
lowThreshold <span style="color: #666666">=</span> <span style="color: #666666">30</span>

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
                        <span style="color: #008000; font-weight: bold">if</span> (num[<span style="color: #666666">0</span>] <span style="color: #666666">!=</span> <span style="color: #666666">0</span>):
                            cv2<span style="color: #666666">.</span>putText(frame,<span style="color: #008000">str</span>(num[<span style="color: #666666">0</span>]),(<span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10+10</span>][<span style="color: #666666">0</span>]<span style="color: #666666">+10</span>), 
                                                                     <span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10+10</span>][<span style="color: #666666">1</span>]<span style="color: #666666">-30</span>)),font,<span style="color: #666666">1</span>,(<span style="color: #666666">225</span>,<span style="color: #666666">0</span>,<span style="color: #666666">0</span>),<span style="color: #666666">2</span>)
                        <span style="color: #008000; font-weight: bold">else</span>:
                            cv2<span style="color: #666666">.</span>putText(frame,<span style="color: #008000">str</span>(num[<span style="color: #666666">0</span>]),(<span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10+10</span>][<span style="color: #666666">0</span>]<span style="color: #666666">+10</span>), 
                                                                 <span style="color: #008000">int</span>(Points[j<span style="color: #666666">+</span>i<span style="color: #666666">*10+10</span>][<span style="color: #666666">1</span>]<span style="color: #666666">-15</span>)),font,<span style="color: #666666">1</span>,(<span style="color: #666666">225</span>,<span style="color: #666666">0</span>,<span style="color: #666666">0</span>),<span style="color: #666666">2</span>)
    cv2<span style="color: #666666">.</span>imshow(<span style="color: #BA2121">&quot;SUDOKU Solver&quot;</span>, frame)
    rval, frame <span style="color: #666666">=</span> vc<span style="color: #666666">.</span>read()
    key <span style="color: #666666">=</span> cv2<span style="color: #666666">.</span>waitKey(<span style="color: #666666">20</span>)
    <span style="color: #008000; font-weight: bold">if</span> key <span style="color: #666666">==</span> <span style="color: #666666">27</span>: <span style="color: #408080; font-style: italic"># exit on ESC</span>
        <span style="color: #008000; font-weight: bold">break</span>
vc<span style="color: #666666">.</span>release()
cv2<span style="color: #666666">.</span>destroyAllWindows()
</pre></div>

Oh Yeahhhh! Now we know how to train a machine learning method and recognize a digital image. The next article, we will discuss how to solve a SUDOKU matrix by backtracking algorithm.  
If you have any questions or comments, please let me know. See ya :)
