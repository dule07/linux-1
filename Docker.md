# Quẩy với Docker cho nó Hot Trend
## Lý thuyết
- Docker sẽ đóng gói toàn bộ môi trường máy tính vào 1 cái gọi lại image và dễ dàng đem nó chạy trên các máy tính khác.
- Image khi được chạy lên sẽ khởi tạo 1 instance của image và nó được gọi là container
- Vậy đại khái là 1 giải pháp để chạy máy ảo trong một máy khác  tuy nhiên nó nhẹ hơn VM nhiều vì nó chỉ đống gói 1 phần (hơi chung chung) và dùng chung rất nhiều tài nguyên. Bình thường mỗi máy ảo sẽ có 1 kernel riêng, thư viện riêng nhưng Container thì #, nó sài chung luôn ==> giảm được tài nguyên. Hoặc suy nghĩ là VM là ảo hóa mức OS, Docker là ảo hóa mức APP ==> không nên chạy nhiều App trong 1 container
 ![img](images/dockervsvm.png)
##
