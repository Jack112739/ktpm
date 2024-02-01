# bài tập tết kiến trúc phần mềm
<h1> 1. Docker </h1> 
<b> Docker </b> là một phần mềm ảo hoá, cho phép giả lập môi trường của một máy tính khác, để kiểm thử, triển khai ứng dụng. Khác với Virtual machine, Docker sẽ không ảo hoá các tập lệnh phần cứng mà chỉ ảo hoá hệ điều hành với không gian bộ nhớ độc lập với máy tính host Docker đó. <br>
<b>docker-composer</b>  là công cụ tạo multicontainer-environment hỗ trợ việc chạy ứng dụng trên nhiều hệ máy khác nhau
<h1> 2. Unix </h1>
Các hệ điều hành như Mac, Linux, BSD, ... hay *nix nói chung đều phát triển từ hệ điều hành Unix
<h1> 3. Linux distro </h1>
Alphine và Ubuntu là các phiên bản khác nhau cùng sử dụng core của linux, cả 2 đều hỗ trợ quản lý package, Ubuntu hỗ trợ nhiều chức năng hơn nhưng image ủa alpine nhẹ và đơn giản hơn
<h1> 4. VNC</h1>
Virtual computing netwwork (VNC) là một phần mềm hỗ trợ chia sẻ quyền điều khiển một máy tính từ xa. Phần mềm truyền tải GUI, input như chuột, bàn phím từ máy này qua máy khác thông qua internet
<h1> 5. Cài đặt vnc remote</h1>
pull image trên hub <a href="https://hub.docker.com/repository/docker/consol/debian-xfce-vnc"> </a>
<img src="/img/pull.png">
run vnc-server
<img src="/img/run.png">
kết nối trên thiết bị noVnc thông qua cổng 6901
<img src="/img/novnc.png">
truy cập vnc server qua vnc-viwer
<img src="/img/connect">
đồng bộ 2 màn hình 
<img src="/img/sync.png>