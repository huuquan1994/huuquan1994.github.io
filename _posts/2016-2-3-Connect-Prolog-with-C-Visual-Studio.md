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
 
<p align="center">
<img src="https://3.bp.blogspot.com/-523sEuVn-8s/Vl1K-cHCVjI/AAAAAAAABt8/VjEz8_wBYpI/s320/2015-12-01_094129.jpg">
</p>
Form có tên là **Demo_Prolog** và lớp chính mang tên **Demo_Prolog.cs**. Hai button **Load** và **Query** dùng để mở file prolog và truy vấn sau khi đã gõ lệnh truy vấn. Sau đó ta Build project bằng cách chọn **Build -> Build Solution** hoặc nhấn tổ hợp phím tắt **Ctrl + Shift + B**.
Sau khi build thành công project, ta có thêm thư mục **Debug** trong thư mục **bin** của project.

<p align="center">
<img src="https://2.bp.blogspot.com/-yPGYoehmKJQ/Vl1Q8_WddzI/AAAAAAAABuY/Hr2I1hBDYK4/s320/2015-12-01_144844.jpg">
</p>
Lúc này, ta copy toàn bộ file trong thư mục **SwiPlCs_1.1.60605.0** vào thư mục **Debug** nêu ở trên của Project.
Tiếp theo là bước quan trọng nhất. Ta sẽ thêm thư viện SwiPlCs vào project bằng cách từ giao diện project của Visual Studio chọn chuột phải vào **References --> Add references....** Sau đó chọn **Browse...** rồi trỏ tới thư mục **Debug** của project và thêm 2 file **SwiPlCs.dll** và **nunit.framework.dll** vào **References**

<p align="center">
<img src="https://2.bp.blogspot.com/-4_X7csDchM0/Vl1SlZXxtrI/AAAAAAAABuk/PxqiTbPSypI/s320/2015-12-01_145545.jpg">
</p>
Sau khi thêm thư viện, sẽ tới phần code.
Ta tạo 1 class **Connect_Prolog.cs** để load file và kết nối với C#. Code như sau

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;"><span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Collections.Generic</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Linq</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Text</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Threading.Tasks</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">SbsSW.SwiPlCs</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Text.RegularExpressions</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">NUnit.Framework</span>;


<span style="color: green; font-weight: bold;">namespace</span> <span style="color: blue; font-weight: bold;">Connect_Prolog</span>
{
    <span style="color: green; font-weight: bold;">class</span> <span style="color: blue; font-weight: bold;">ConnectProlog</span>
    {
        <span style="color: #408080; font-style: italic;">//Load prolog file from hard disk</span>
        <span style="color: green; font-weight: bold;">public</span> <span style="color: green; font-weight: bold;">void</span> <span style="color: blue;">Load_file</span>(<span style="color: #b00040;">string</span> s)
        {
            s = s.Replace(<span style="color: #ba2121;">"\\"</span>, <span style="color: #ba2121;">"//"</span>);
            s = <span style="color: #ba2121;">"consult('"</span> + s + <span style="color: #ba2121;">"')"</span>;
            <span style="color: #b00040;">string</span> query = s.Replace(<span style="color: #ba2121;">"\\"</span>, <span style="color: #ba2121;">"//"</span>);
            <span style="color: #408080; font-style: italic;">//string[] p = { "-q", "-f", query };</span>
            <span style="color: #408080; font-style: italic;">//PlEngine.Initialize(p);</span>
            <span style="color: green; font-weight: bold;">try</span>
            {
                PlQuery q = <span style="color: green; font-weight: bold;">new</span> PlQuery(query);
                Assert.IsTrue(q.NextSolution());
            }
            <span style="color: green; font-weight: bold;">catch</span> (SbsSW.SwiPlCs.Exceptions.PlException e)
            {
                System.Windows.Forms.MessageBox.Show(e.ToString(), <span style="color: #ba2121;">"Error"</span>);
            }
        }

        <span style="color: #408080; font-style: italic;">// Prosessing a query</span>
        <span style="color: green; font-weight: bold;">public</span> <span style="color: #b00040;">string</span> <span style="color: blue;">Query</span>(<span style="color: #b00040;">string</span> s)
        {
            s.Trim();
            Regex r = <span style="color: green; font-weight: bold;">new</span> Regex(<span style="color: #ba2121;">@"[A-Z_][a-zA-Z_]*"</span>);
            MatchCollection matches = r.Matches(s);
            <span style="color: #b00040;">string</span> result = <span style="color: #ba2121;">""</span>;
            <span style="color: green; font-weight: bold;">try</span>
            {
                PlQuery q = <span style="color: green; font-weight: bold;">new</span> PlQuery(s);
                <span style="color: #b00040;">bool</span> HasSolution = <span style="color: green; font-weight: bold;">false</span>;
                <span style="color: green; font-weight: bold;">foreach</span> (PlQueryVariables v <span style="color: green; font-weight: bold;">in</span> q.SolutionVariables)
                {
                    HasSolution = <span style="color: green; font-weight: bold;">true</span>;
                    <span style="color: green; font-weight: bold;">foreach</span> (Match match <span style="color: green; font-weight: bold;">in</span> matches)
                    {
                        result += v[match.ToString()].ToString() + <span style="color: #ba2121;">" ; "</span>;
                    }
                }
                <span style="color: green; font-weight: bold;">if</span> (matches.Count == <span style="color: #666666;">0</span>)
                    <span style="color: green; font-weight: bold;">return</span> HasSolution ? <span style="color: #ba2121;">"true"</span> : <span style="color: #ba2121;">"false"</span>;
                <span style="color: green; font-weight: bold;">return</span> result;
            }
            <span style="color: green; font-weight: bold;">catch</span> (SbsSW.SwiPlCs.Exceptions.PlException ex)
            {
                <span style="color: green; font-weight: bold;">return</span> <span style="color: #ba2121;">"Error query: "</span> + ex.Message;
            }
        }
    }
}
</pre>
</div>
Tiếp theo trong file **Demo_Prolog.cs** có nội dung như sau:

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;"><span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Data</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Drawing</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Linq</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Text</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Text.RegularExpressions</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Threading.Tasks</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Windows.Forms</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">SbsSW.SwiPlCs</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">NUnit.Framework</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">Connect_Prolog</span>;

<span style="color: green; font-weight: bold;">namespace</span> <span style="color: blue; font-weight: bold;">Demo_Prolog</span>
{
    <span style="color: green; font-weight: bold;">public</span> <span style="color: green; font-weight: bold;">partial</span> <span style="color: green; font-weight: bold;">class</span> <span style="color: blue; font-weight: bold;">Demo_Prolog</span> : Form
    {
        Connect_Prolog.ConnectProlog connect;
        <span style="color: green; font-weight: bold;">public</span> <span style="color: blue;">Demo_Prolog</span>()
        {
            connect = <span style="color: green; font-weight: bold;">new</span> ConnectProlog();
            InitializeComponent();
        }

        <span style="color: green; font-weight: bold;">private</span> <span style="color: green; font-weight: bold;">void</span> <span style="color: blue;">btn_Load_Click</span>(<span style="color: #b00040;">object</span> sender, System.EventArgs e)
        {
            OpenFileDialog op = <span style="color: green; font-weight: bold;">new</span> OpenFileDialog();
            op.Filter = <span style="color: #ba2121;">"Prolog file|*.pl"</span>;
            op.ShowDialog();
            String FilePath = op.FileName;
            connect.Load_file(FilePath);
            MessageBox.Show(<span style="color: #ba2121;">"Load file success !"</span>);
            <span style="color: green; font-weight: bold;">this</span>.btn_Query.Enabled = <span style="color: green; font-weight: bold;">true</span>;
        }

        <span style="color: green; font-weight: bold;">private</span> <span style="color: green; font-weight: bold;">void</span> <span style="color: blue;">btn_Query_Click</span>(<span style="color: #b00040;">object</span> sender, EventArgs e)
        {
            <span style="color: green; font-weight: bold;">if</span> (<span style="color: green; font-weight: bold;">this</span>.txt_Query.Text != <span style="color: green; font-weight: bold;">null</span>)
            {
                String s = connect.Query(<span style="color: green; font-weight: bold;">this</span>.txt_Query.Text);
                <span style="color: green; font-weight: bold;">this</span>.txt_Result.Text = s;
            }
            <span style="color: green; font-weight: bold;">else</span>
            {
                MessageBox.Show(<span style="color: #ba2121;">"Please enter query !"</span>);
            }
        }
    }
}
</pre>
</div>
Phần quan trọng nhất là điều chỉnh môi trường trong file **Program.cs**. Nội dung file **Program.cs** như sau:

<div style="background: #f8f8f8; border-width: 0.1em 0.1em 0.1em 0.8em; border: solid gray; overflow: auto; padding: 0.2em 0.6em; width: auto;">
<pre style="line-height: 125%; margin: 0;"><span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Collections.Generic</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Linq</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Threading.Tasks</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">System.Windows.Forms</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">SbsSW.SwiPlCs</span>;
<span style="color: green; font-weight: bold;">using</span> <span style="color: blue; font-weight: bold;">NUnit.Framework</span>;

<span style="color: green; font-weight: bold;">namespace</span> <span style="color: blue; font-weight: bold;">Demo_Prolog</span>
{
    <span style="color: green; font-weight: bold;">static</span> <span style="color: green; font-weight: bold;">class</span> <span style="color: blue; font-weight: bold;">Program</span>
    {
        <span style="color: #408080; font-style: italic;">/// </span>

        <span style="color: #408080; font-style: italic;">/// The main entry point for the application.</span>
        <span style="color: #408080; font-style: italic;">/// </span>

<span style="color: #7d9029;">        [STAThread]</span>
        <span style="color: green; font-weight: bold;">static</span> <span style="color: green; font-weight: bold;">void</span> <span style="color: blue;">Main</span>()
        {
            Environment.SetEnvironmentVariable(<span style="color: #ba2121;">"SWI_HOME_DIR"</span>, <span style="color: #ba2121;">@"C:\Program Files (x86)\swipl"</span>);
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(<span style="color: green; font-weight: bold;">false</span>);
            <span style="color: green; font-weight: bold;">if</span> (!PlEngine.IsInitialized)
            {
                String[] empty_param = { <span style="color: #ba2121;">""</span> };
                PlEngine.Initialize(empty_param);
                Application.Run(<span style="color: green; font-weight: bold;">new</span> Demo_Prolog());
                PlEngine.PlCleanup();
            }
        }
    }
}
</pre>
</div>
Lưu ý: **Environment.SetEnvironmentVariable("SWI_HOME_DIR", @"C:\Program Files (x86)\swipl")**; dùng để set môi trường cho việc kết nối bằng cách trỏ tới thư mục cài Prolog. Phải điền đúng đường dẫn vào thư mục cài đặt Prolog tùy thuộc vào hệ máy (32 hoặc 64 bits).

Xong rồi, giờ hãy chạy demo để xem kết quả.
Câu khi truy vấn ta cho kết quả như sau:

<p align="center">
<img src="https://2.bp.blogspot.com/-6H0bUPxgMgs/Vl1PjXG8t6I/AAAAAAAABuI/mVCUN5300Zg/s1600/2015-12-01_144300.jpg">
</p>
Hi vọng bài viết có thể giúp đỡ được các bạn. Chúc các bạn thành công !
