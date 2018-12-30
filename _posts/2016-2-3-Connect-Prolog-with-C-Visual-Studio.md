---
layout: post
title: Connect Prolog with C# on Visual Studio
---

Prolog là một ngôn ngữ mạnh mẽ chuyên phục vụ cho Trí Tuệ Nhân Tạo và Xử Lý Ngôn Ngữ Tự Nhiên.
Đôi lúc, chúng ta cũng phải kết hợp Prolog với các ngôn ngữ khác để tăng sự trực quan cho ứng dụng.
Bài viết này sẽ đề cập đến việc kết nối C# vào Prolog để lấy kết quả trả về từ Prolog.  
Vào chuyên môn luôn nào :)
Các yêu cầu để hỗ trợ kết nối:  
1. Prolog phiên bản 32 bits 6.6.5
2. SwiPlCs: Thư viện kết nối C# và Prolog phiên bản 1.1.60605.0
3. Visual Studio bản 2008 trở lên.

Hai phần mềm **Prolog 32 bits 6.6.5** và **SwiPlCs** tương ứng có thể tải [**tại đây**](https://drive.google.com/file/d/0BxW8WySRlblWOUprQmZtbldPek0/view)  
Visual Studio thì các bạn tự cài nhé :)

<u>**Lưu ý**</u>: Vì thư viện SwiPlCs ngưng phát triển nên nó chỉ hỗ trợ phiên bản Prolog tương ứng là Prolog 32bits 6.6.5. Vậy nên, những ai đang chạy phiên bản Prolog khác thì phải gỡ và cài đúng bản 32 bits 6.6.5 thì mới kết nối được.

Sau khi chuẩn bị xong, ta sẽ đi qua bước kết nối.
Trong phần này, tôi sẽ demo để truy vấn 1 file prolog có nội dung như sau:

```s-->np,vp.
np-->nn,pp.
np-->nnp.
np-->nn.
np-->nn,nn.
vp-->rb,vb,pp.
vp-->vb,pp.
vp-->rb,vb,np.
vp-->vb,vp.
vp-->vb,np.
pp-->in,nnp.
pp-->in,nn.
pp-->in,np.

nn-->[gia,đình].
nn-->[tỉnh].
nn-->[nhà].
nn-->[ngành].
nn-->[công,nghệ,thông,tin].
nn-->[thành,phố].
in-->[của].
in-->[ở].
nnp-->[nam].
vb-->[sống].
vb-->[học].
vb-->[về].
vb-->[thích].

rb-->[đang].
rb-->[thường].```
{: style="text-align:center"}
Trước tiên, tạo 1 project Windows Form trên C# với giao diện như sau:  
![fig_1](https://3.bp.blogspot.com/-523sEuVn-8s/Vl1K-cHCVjI/AAAAAAAABt8/VjEz8_wBYpI/s320/2015-12-01_094129.jpg)
