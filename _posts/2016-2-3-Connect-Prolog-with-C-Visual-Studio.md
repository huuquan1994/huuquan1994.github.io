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

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;">s--&gt;np,vp.
np--&gt;nn,pp.
np--&gt;nnp.
np--&gt;nn.
np--&gt;nn,nn.
vp--&gt;rb,vb,pp.
vp--&gt;vb,pp.
vp--&gt;rb,vb,np.
vp--&gt;vb,vp.
vp--&gt;vb,np.
pp--&gt;in,nnp.
pp--&gt;in,nn.
pp--&gt;in,np.

nn--&gt;[gia,đình].
nn--&gt;[tỉnh].
nn--&gt;[nhà].
nn--&gt;[ngành].
nn--&gt;[công,nghệ,thông,tin].
nn--&gt;[thành,phố].
in--&gt;[của].
in--&gt;[ở].
nnp--&gt;[nam].
vb--&gt;[sống].
vb--&gt;[học].
vb--&gt;[về].
vb--&gt;[thích].

rb--&gt;[đang].
rb--&gt;[thường].
</pre>
</div>

Trước tiên, tạo 1 project Windows Form trên C# với giao diện như sau:  
{:.center}
![fig_1](https://3.bp.blogspot.com/-523sEuVn-8s/Vl1K-cHCVjI/AAAAAAAABt8/VjEz8_wBYpI/s320/2015-12-01_094129.jpg)
