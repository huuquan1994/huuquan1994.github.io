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
