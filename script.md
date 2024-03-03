# CQRS 


Chào các bạn, nhóm tụi mình bao gồm Nguyễn Đắc Quán và Trịnh Văn Khánh, hôm nay tụi mình sẽ trình bày về pattern CQRS, viết tắt cho Command and Query Responsibility Segregation
<h1>Bối cảnh </h1>
Trước khi đi sâu vào CQRS là gì thì chúng ta nên đi qua một ví dụ: <br>
Team bọn mình hiện đang phát triển một tựa game MOBA trên di động với tham vọng soán ngôi liên quân mobile. Và dĩ nhiên game MOBA phải có chế độ multiplayer tức là cần đến một server. <br>

Đây là một model client-server đơn giản. Ở mỗi frame server sẽ update trạng thái của bản đồ và người chơi dựa trên hành động của họ tại frame trước đó. Còn với player, mỗi frame họ sẽ gửi cho server hành động của player và render toàn bộ GameObject nằm trong view của player đó
<img src="/img/pic1.png">

Model trên có thể coi là tạm chấp nhận được khi game còn trong giai đoạn beta khi số lượng player còn nhỏ và nhiều tướng 200 năm. Tuy nhiên khi game được phát hành, code như trên sẽ gặp phải rất nhiều vấn đề
<ol>
<li> Khi game phát triển hơn, người chơi không còn là nhân tố tác động duy nhất đến bản đồ<br>
=> Ta cần cập nhật dữ liệu đó chính xác mặc dù không trực tiếp tác động đến player tại frame đó</li>
<li> Việc cập nhật trạng thái player tuần tự có thể gây ra hiện tượng hành động của họ thiếu nhất quán với thời gian thực, đây là dấu hiệu của data race <br>
Ví dụ trong thời gian thực A né được skill của B, nhưng do cập nhật B trước A nên kết quả trả về là A vẫn trúng skill của B </li>
<li> Thiếu cơ chế bảo mật để kiểm tra tính đúng đắn các action của player <br>
=> tiềm ẩn nguy cơ hack, cheat </li>
<li> dữ liệu trả về cho player là dữ liệu thô của cả bản đồ
=> player đó có thể lợi dụng và sinh ra tình trạng đá map</li>
<li> Việc kiểm tra các yếu tố anti cheat này có thể rất lằng nhằng có thể làm giảm hiệu năng<br>
=> làm giảm fps của game và ảnh hưởng trải nghiệm game </li>
</ol>

Đây chỉ là một trong số ít những vấn đề có thể phát sinh. Và thực tế tại các phần mềm có quy mô lớn và nhiều người dùng, tác vụ đọc và xử lý dữ liệu trên server thường độc lập và có những yêu cầu khác nhau, nên việc tích hợp vào data model có thể khiến model đó trở nên vô cùng phức tạp gây khó khăn cho việc maintainancy, scalability,...

=> Pattern CQRS được sinh ra để giải quyết cho vấn đề này bằng việc tách việc client đọc dữ liệu và server xử lý dữ liệu thành 2 model riêng

<h1> Cách giải quyết của CQRS </h1>
CQRS sẽ chia data model thành 2 model con là "command" dùng để thay đổi dữ liệu và "query" dùng để lấy dữ liệu, chúng hoạt động dựa trên một số nguyên tắc sau
<ol>
<li> Module command cần quan tâm về mục tiêu của task thay vì tập trung vào data . Ví dụ các hàm nên là grantPlayer(buff) thay vì setPlayerStat(buff.effect) </li>
<li> Ta nên xem xét việc thay đổi logic "command" đó sao cho hiện tượng dữ liệu sai với logic xảy ra ít thường xuyên hơn. Ví dụ bằng cách cài tích hợp phần mềm anti cheat để kiểm tra action của player trước khi gửi chúng lên server, việc này sẽ đảm bảo server luôn có action hợp lệ của người dùng.</li>
<li> Command nên được đặt vào 1 queue để có thể xử lý một cách không đồng bộ thay vì tuần tự </li>
<li> Query không được làm thay đổi dữ liệu trên server và database, nên đóng gói dữ liệu trả về từ query </li>
</ol>
<img src="/img/pic2.png">

<h1> Lợi ích của CQRS </h1>
Vậy thì liệu pattern CQRS có lợi ích gì, ta hãy nhìn vào ví dụ bên trên sau khi áp dụng pattern CQRS, khi đó ta sẽ có 2 model
<figure>
    <img src="/img/read.png">
    <em>model query</em>

    
</figure>
<figure>
    <img src="/img/write.png">
    <em>model command</em>
</figure>


Mô hình này đã giải quyết được phần nhiều những vấn đề tụi mình đặt ra ở phần trước đó và thực tế trong các ứng dụng có quy mô lớn, pattern CQRS có thể đem lại một số lợi ích như:
<ol> 
<li> Đầu tiên thì giống như những lợi ích khi chia các modules nói chung, pattern này sẽ giúp gia <b> tăng khả năng independent scaling và maintainablity</b> của model đọc và model, tại vì để phát triển hoặc bảo trì một cái thì mình không cần quan tâm cái còn lại </li>
<li> Tiếp theo là <b>tối ưu hoá schema cho từng kiểu model</b>, ví dụ bên command có thể dùng schema tôt nhất cho việc thay đổi dữ liệu còn bên query sử dụng schema khác hoạt động tốt hơn cho việc đọc dữ liệu </li>
<li> <b> Tăng cường bảo mật</b>, dễ dàng để kiểm tra tính đúng đắn của dữ liệu server nhận và đảm bảo chỉ bên có thẩm quyền mới được ghi vào cơ sở dữ liệu </li>
<li> <b> Các truy vấn csdl trở nên đơn giản hơn </b> </li>
</ol>
Nói chung, các điểm mạnh mà pattern này mang lại đều nằm ở việc phân tách rõ ràng giữa việc đọc dữ liệu và thay đổi dữ liệu


Dẫu có một số điểm mạnh thì pattern này không phải là không có mặt hạn chế
<h1> Hạn chế của CQRS </h1>
<ol>
<li>Ý tưởng của CQRS đơn giản, Tuy nhiên vẫn <b>phức tạp hơn so với cách làm truyền thống </b>, đặc biệt khi sử dụng CQRS kết hợp với nhiều pattern khác</li>
<li> <b> Dễ gây ra sự thiếu nhất quán trong dữ liệu </b> do phân tách việc đọc và thay đổi dữ liệu ra thành 2 module nên rất dễ gây ra tình trạn dữ liệu thiếu nhất quán, command đã thay đổi dữ liệu nhưng query vẫn trả về dữ liệu cũ </li>
</ol>
<h1> CQRS nên dùng cho trường hợp nào ? </h1>
Vậy thì với những điểm mạnh và hạn chế như trên thì CQRS nên được dùng như thế nào

Ta <b>không nên áp dụng nếu thao tác đọc và thay đổi dữ liệu của ứng dụng đơn giản</b> do chúng sẽ chỉ làm tăng độ phức tạp data model chứ không phát huy được nhiều điểm mạnh của pattern này

Tuy nhiên khi <b>ứng dụng có quy mô lớn, việc thao tác trên dữ liệu trở nên phức tạp</b> với nhiều người dùng cùng lúc thì CQRS nên được áp dụng để việc phát triển model đọc và viết dữ liệu trở nên dễ dàng hơn.
