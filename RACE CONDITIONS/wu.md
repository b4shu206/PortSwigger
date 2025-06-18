# Race conditions
https://portswigger.net/web-security/race-conditions
## Race conditions là gì?
Đây là 1 lỗ hổng xảy ra khi website xử lý các yêu cầu cùng lúc mà không có các biện pháp bảo vệ đầy đủ. Điều này dẫn tới việc nhiều luồng xử lý riêng biệt và khiến hệ thống không hoạt động như mong đợi.
## Race condition limit overrun
Đây là loại race condition phổ biến nhất cho phép bạn vượt một giới hạn nào đó được áp dụng bởi logic của dịch vụ
Ví dụ khi 1 trang web bán hàng cho phép sử dụng mã giảm giá và chỉ giảm 1 lần duy nhất cho mã đó. Với quy trình kiểm tra như sau
- Kiểm tra xem bạn đã sử dụng mã giảm giá hay chưa
- Áp dụng giảm giá vào tổng giá trị đơn hàng
- Cập nhật dữ liệu để phản ánh rằng bạn đã sử dụng mã giảm giá này

Nếu bạn sử dụng lại mã giảm giá đó thì sẽ bị chặn. Tuy nhiên nếu như bạn cố gắng sử dụng mã giảm giá này 2 lần CÙNG một lúc và khi lúc này nó kiểm tra đã áp hãy chưa thì vẫn là chưa và áp dụng giảm giá cả 2 lần đè lên nhau.

Các biến thể của nó có thể là:
- Dùng lại thẻ quà tặng nhiều lần
- Đánh giá một sản phẩm nhiều lần
- Rút hoặc chuyển tiền vượt quá số dư tài khoản
- Dùng lại đáp án CAPTCHA một lần nữa
- Vượt qua giới hạn tấn công brute-force

## Lab: Limit overrun race conditions
Mục tiêu là mua được sản phẩm: __Lightweight L33t Leather Jacket__
Ở đây thì ta có mã giảm giá `PROMO20` áp dụng giảm giá 20%
![image](https://hackmd.io/_uploads/rJUCtGm-eg.png)
Giá sản phẩm tận 1337$ nhưng ta chỉ có 50$
![image](https://hackmd.io/_uploads/S1HX9z7-gx.png)
Thử apply coupon và bắt request lại
![image](https://hackmd.io/_uploads/ByfP9GmWgg.png)
Ở đây nó giảm 267$ cho 1 lần
![image](https://hackmd.io/_uploads/HJzOcG7Wxx.png)
Vậy để giảm hết 1337$ thì cần khoảng 5 lần áp dụng là được.
Thì trong burpsuite có chức năng send group (parallel) sẽ giúp ta gửi request hàng loạt
![image](https://hackmd.io/_uploads/SkbTqzQble.png)
Thử với 5 request thì có vẻ chỉ dính 3 request
![image](https://hackmd.io/_uploads/HyNR9f7Wxx.png)
Giờ ta tăng số request lên và thử lại thì nó đã giảm xuống còn 37$ vậy giờ mua thôi
![image](https://hackmd.io/_uploads/HJJyhf7blx.png)
Và solve được lab
![image](https://hackmd.io/_uploads/Hkrx2fXWxx.png)

## Lab: Bypassing rate limits via race conditions
Mục tiêu là bypass limit bằng race và login với tài khoản admin sau đó xóa acc carlos.
Ở đây cũng cho sẵn list password của admin nên mình lưu nó về file pass.txt sau đó dùng script python để này race và bruteforce
