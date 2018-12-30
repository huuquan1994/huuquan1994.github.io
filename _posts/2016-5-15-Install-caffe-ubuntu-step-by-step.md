---
layout: post
title: Install Caffe library on Ubuntu 14.04 Step-by-Step (with images instruction)
---

Deep Learning hiện đang là mô hình hiệu quả để giải quyết rất nhiều vấn đề như Xử Lý Ảnh, Xử Lý Ngôn Ngữ Tự Nhiên..v.v. Việc hiện thực hóa các mô hình Deep Learning bây giờ không mấy khó khăn vì đã có rất nhiều thư viện hỗ trợ việc thiết kế mô hình mạng neural. Caffe là một trong những thư viện hỗ trợ Deep Learning có đông đảo người dùng nhất. Caffe rất mạnh trong việc xử lý ảnh và nó cũng có thể thiết kế các mô hình Deep Learning cho các vấn đề khác. Thư viện này có thể kết hợp với GPU của NVIDIA giúp cải thiện tốc độ huấn luyện mạng thông qua CUDA.  
Bài viết này sẽ hướng dẫn cách cài Caffe chi tiết từng bước cho người mới bắt đầu trên hệ điều hành Ubuntu 14.04 LTS 64 bits.

Trước tiên, bạn cần tải bản mới nhất của Caffe về tại địa chỉ:
[https://github.com/BVLC/caffe/archive/master.zip](https://github.com/BVLC/caffe/archive/master.zip)  
Có thể sử dụng lệnh **`git clone http://github.com/BVLC/caffe.git`** nếu như máy bạn có cài git sẵn.  
Sau khi tải về, các bạn xả nén Caffe vào 1 thư mục nào đó để dễ quản lý. Ở trường hợp này, Caffe sẽ lưu ở đường dẫn **/home/caffe**

<p align="center">
<img src="https://1.bp.blogspot.com/-Yjdc5MiYN0A/Vzf4IggmS0I/AAAAAAAACJg/3A_s4pxJCBkwK5jIw4hV2ND4tiI2kxcPACLcB/s400/1.png">
</p>
Tiếp theo, cài đặt thư viện cần thiết để hỗ trợ compile và build caffe.  
**<u>Lưu ý</u>**: Nếu máy tính không có card màn hình của hãng NVIDIA thì không cần cài thư viện CUDA. Trong lúc huấn luyện sẽ sử dụng CPU.

Bật terminal (Ctrl + Alt + T) và gõ vào Terminal những lệnh sau:

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;">
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
sudo apt-get install libatlas-base-dev
</pre>
</div>
Việc cài đặt sẽ mất một khoảng thời gian từ 5-10 phút tùy vào tốc độ mạng của bạn.  
Sau khi cài đặt thành công những thư viện trên, ta cài đặt gói phần mềm Annaconda.  
Annaconda là gói phần mềm hỗ trợ quản lý và lưu trữ những thư viện cho Python (hỗ trợ luôn cài Python nếu máy chưa cài Python) miễn phí. Nếu bình thường, muốn cài đặt thêm gói tiện ích cho Python, ta phải tìm kiếm và xem thông tin của từng gói phần mềm để tương thích với bản Python của máy mình. Với Annaconda, mọi việc trở nên dễ dàng hơn khi bạn chỉ cần nhớ tên gói thư viện, những việc còn lại Annaconda sẽ xử lý.  
Annaconda chưa hơn 400 packages cần thiết phục vụ cho xử lý liên quan đến Toán học, kỹ thuật, xử lý dữ liệu, xử lý ảnh, phân tích dữ liệu trên Python. Hơn nữa, Caffe hỗ trợ build với Annaconda, điều này khá tiện lợi và dễ dàng cho những ai mới bắt đầu sử dụng Caffe và Python trên Ubuntu.
Tải phiên bản cho Ubuntu 14.04 tại địa chỉ: [https://www.continuum.io/downloads](https://www.continuum.io/downloads)  
Bài viết này sử dụng bản Annaconda 64 bits with Python 2.7
Sau khi tải về, cài đặt Annaconda trên Terminal như sau:
Ở trường hợp này file **Anaconda2-4.0.0-Linux-x86_64.sh** được lưu tại thư mục Downloads nên trước tiên phải trỏ tới thư mục Downloads bằng lệnh sau:  
**`cd /Downloads`**  
Sau đó cài Anaconda bằng lệnh sau:  
**`sudo bash  Anaconda2-4.0.0-Linux-x86_64.sh`**

**<u>Lưu ý</u>**: phải gõ đúng tên file Anaconda.

Sau đó nhấn Enter và đọc kỹ điều khoản để cài Anaconda. Việc cài đặt diễn ra khoảng 5 phút.

<p align="center">
<img src="https://2.bp.blogspot.com/-nyc9Jic6LfU/Vzf6fSZ7VqI/AAAAAAAACJw/mIb6SvNez4wUySlu-jn_7SfDT2K5pri2QCK4B/s640/2.png">
</p>
**<u>Lưu ý</u>**: Khi cài đặt thành công, Anaconda sẽ hỏi bạn muốn thêm đường dẫn cài Anaconda vào đường dẫn hệ thống không. Lúc này hãy gõ "**yes**" và nhấn Enter.

<p align="center">
<img src="https://3.bp.blogspot.com/-tgMye-sM_ZQ/Vzf6t8luwEI/AAAAAAAACJ4/Yt6HFQPdtcUPHFvUQuH1PYLCjSAtAfDLACK4B/s640/3.png">
</p>
Đã hoàn tất quá trình chuẩn bị các gói hỗ trợ cần thiết. Lúc này, ta sẽ sang công đoạn build Caffe.  
Trước hết, để build Caffe, ta phải chỉnh sửa thông tin trong file config.  
Ở cửa sổ Terminal, trỏ tới thư mục Caffe và thực hiện câu lệnh sau để tạo mới 1 file .config:  
**`sudo cp Makefile.config.example Makefile.config`**

Tiếp theo mở file **Makefile.config** mới khỏi tạo này lên bằng trình Text Editor (gedit) và chỉnh sửa các thông tin như sau:
Ở dòng số 8, xóa comment (xóa ký tự #) để sử dụng CPU cho caffe (vì máy mềnh không có GPU NVIDIA nên không thể sử dụng CUDA. Nếu máy tính có card NVIDIA thì xóa comment để sử dụng CUDNN ở dòng **USE_CUDNN := 1**)  
**CPU_ONLY := 1**

<p align="center">
<img src="https://4.bp.blogspot.com/-pqc0ki4x1Lk/Vzf7nGoiJlI/AAAAAAAACKI/Vroxp_2FaVoWBc22wZfpiYGW651X1XSygCK4B/s640/4.png">
</p>
Tiếp theo, nếu có sử dụng Matlab để code Caffe thì xóa comment tại dòng:  
"**MATLAB_DIR := /usr/local**" và thay "**/usr/local**" bằng đường dẫn tới Matlab.  
Ví dụ:  **MATLAB_DIR := /usr/local/MATLAB/R2014b**

<p align="center">
<img src="https://3.bp.blogspot.com/-zP9UmJB8hWw/Vzf8A_J-xCI/AAAAAAAACKQ/uGqjBIuYS3M-fz8wkyDkjBdiyeE1kNnPgCK4B/s640/5.png">
</p>
Tiếp đến, ta chỉnh thông số Anaconda bằng cách bỏ comment và thay thế đường dẫn tới thư mục Anaconda như sau:

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;">
ANACONDA_HOME := $(HOME)/anaconda2
PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
                        $(ANACONDA_HOME)/include/python2.7 \
                        $(ANACONDA_HOME)/lib/python2.7/site-packages/numpy/core/include \
</pre>
</div>

<p align="center">
<img src="https://4.bp.blogspot.com/-ytpNDFECaMw/Vzf8qRqVl9I/AAAAAAAACKc/GuKdIjy09ZQJtYcMnH2nletjfHP5_IqlwCK4B/s640/6.png">
</p>
Đã xong, Save lại file **Makefile.config**.

Tiếp theo ta build Caffe bằng cách sau:  
Trỏ tới thư mục Caffe trên Terminal và gõ lệnh sau:

**`make clean`**  
**`make all`**

<p align="center">
<img src="https://2.bp.blogspot.com/-9CQuJCH33Xc/Vzf88S2rORI/AAAAAAAACKo/6896oiRSiJ4Kvw3PFYr1Hxuw6Ra5yscFACK4B/s640/7.png">
</p>
Sau khi chạy xong lệnh **`make all`**. Ta gõ tiếp lệnh: **`make test`**

<p align="center">
<img src="https://3.bp.blogspot.com/-h8MJm-rIYAU/Vzf9FUDcmFI/AAAAAAAACKw/YTMeH4XraXcwmTX3t_d9wD57wYzmIhJFQCK4B/s640/8.png">
</p>
Tiếp theo gõ lệnh: **`make runtest`** để chạy thử Caffe sau khi build có lỗi hay không.  
Nếu sau khi chạy lệnh này không có lỗi nào thì bạn đã thành công 90% khi build Caffe.

<p align="center">
<img src="https://2.bp.blogspot.com/-8f8x9YB10KQ/Vzf9uuVaE6I/AAAAAAAACK8/IxScEFI91zkmJ-UHrsZJozmWSK1qvRM1QCK4B/s640/9.png">
</p>
Sau khi test thành công, ta chạy lệnh sau để tạo thư viện caffe chạy được trên Python.  
**`make pycaffe`**

Tiếp theo, thêm đường dẫn pycaffe vào đường dẫn hệ thống bằng câu lệnh sau:  
**`sudo echo export PYTHONPATH=<đường dẫn tới thư mục caffe/python>:$PYTHONPATH >> ~/.bashrc`**

**<u>Ví dụ</u>**: **`sudo echo export PYTHONPATH=~/caffe/python:$PYTHONPATH >> ~/.bashrc`**

<p align="center">
<img src="https://3.bp.blogspot.com/-Gnx-U8dPZjk/Vzf_0wrOzAI/AAAAAAAACLU/JknFJFrbZaolTFrd6p1LuX60ZUs3KrtmwCK4B/s640/11.png">
</p>
Tương tự như với pycaffe, ta gõ lệnh: **`make matcaffe`** để biên dịch thư viện chạy trên Malab như sau:

<p align="center">
<img src="https://4.bp.blogspot.com/-59E1Neh80yo/Vzf_b2l30xI/AAAAAAAACLM/zfQLubua-fkOHuUL4TuChGE5zbLWBvWPgCK4B/s640/10.png">
</p>

Để sử dụng caffe cho Matlab, xem bài hướng dẫn **tại đây** (đang cập nhật sau)  
Cuối cùng, ta gõ lệnh : **`make distribute`** để kết thúc quá trình cài đặt.

<p align="center">
<img src="https://3.bp.blogspot.com/-qnmSi6tL9bg/VzgAIYtbViI/AAAAAAAACLg/DyPGTg2F7n8yMSMlWibeYN3ceNKg820oQCK4B/s640/12.png">
</p>
Chúc mừng bạn đã cài đặt thành công thư viên Caffe trên Ubuntu.

**Demo với iPython Notebook (Python IDE base web browser)**  
Để kiểm tra thử xem Caffe có thể dùng được cho Python không. Hãy làm một vài phép thử và biết có lỗi để còn sửa :))

Các bạn mở Terminal và gõ lệnh: **`jupyter notebook`** để mở IDE lên.
Nếu bạn là người mới dùng iPython Notebook thì tại cửa sổ web mở ra bạn chọn **New -> Python 2** để tạo một project python mới.
Tại đây, bạn gõ lệnh : **`import caffe`** rồi nhấn Shift + Enter để import thư viện Caffe vào project Python.  
Nếu xuất hiện lỗi sau:  
**`ImportError: No module named google.protobuf.internal`**

<p align="center">
<img src="https://3.bp.blogspot.com/-gjo8vmUXmQg/Vz-zKvt0HkI/AAAAAAAACL0/sQUAEY02ZAAaBDrEC_t0SypoDvXx7JddACLcB/s640/1.png">
</p>
Lỗi này là do máy tính bạn chưa cài đúng thư viện protobuf của Google (Thư viện cung cấp cơ chế đệm cho việc chạy các phần mềm khác nhanh hơn)

Các bạn mở Terminal mới và gõ lệnh sau để cài protobuf:  
**`conda install -c https://conda.anaconda.org/anaconda protobuf`**

<p align="center">
<img src="https://4.bp.blogspot.com/-TSQx3Yu_ttE/Vz-zqfqPEpI/AAAAAAAACL4/2xx-7_Dz1ugAZZE8zReonR69mBup1sRBwCLcB/s640/2.png">
</p>
**<u>Lưu ý</u>**: Có thể lúc cài protobuf sẽ bị lỗi: 
**`Error: Missing write permissions`** ở thư mục cài anaconda2.

Hãy chạy dòng lệnh sau để sửa lỗi:  
**`sudo chown -R <username hiện tại> <đường dẫn tới anaconda>`**

Ví dụ:  username của Ubuntu hiện tại là **`huuquan`** và đường dẫn vào thư mục anaconda là **`/home/huuquan/anaconda2`**

Thì chạy như sau:  
**`sudo chown -R huuquan ~/anaconda2`**

Sau đó chạy lại lệnh trên để cài protobuf cho python.

<p align="center">
<img src="https://1.bp.blogspot.com/-Flvf6j131mA/Vz-0g5VY7PI/AAAAAAAACMM/IKJ8Lv7dCvc3eN110L7lPP0mkBKlJ2-XQCK4B/s640/3.png">
</p>
Chạy lại iPython và import Caffe lần nữa. Có lẽ sẽ hết lỗi :))

<p align="center">
<img src="https://3.bp.blogspot.com/-qlE3yaC3vsQ/Vz-0vLSxi8I/AAAAAAAACMU/_wYbCiLqlbM0-SdRxurG3Ua08E_OgmexQCK4B/s640/4.png">
</p>
Nếu có vấn đề gì, xin vui lòng bình luận ở dưới hoặc liên hệ qua email cho mình :)
