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

![image](https://hackmd-prod-images.s3-ap-northeast-1.amazonaws.com/uploads/upload_e5d4f76b7e4790af3de2f8a5556eb8e1.png?AWSAccessKeyId=AKIA3XSAAW6AWSKNINWO&Expires=1750222984&Signature=5BpoDhvYS04hhtFWSMYdReDDroc%3D)

Giá sản phẩm tận 1337$ nhưng ta chỉ có 50$

![image](https://hackmd-prod-images.s3-ap-northeast-1.amazonaws.com/uploads/upload_0a38f83653cffcbc3f7702d8de265e42.png?AWSAccessKeyId=AKIA3XSAAW6AWSKNINWO&Expires=1750222996&Signature=MBAG3ieWvPfwqQgo6%2Bx68ZIJnIo%3D)

Thử apply coupon và bắt request lại

![image](https://hackmd-prod-images.s3-ap-northeast-1.amazonaws.com/uploads/upload_66466a43e7af45c503e903dd4607f88e.png?AWSAccessKeyId=AKIA3XSAAW6AWSKNINWO&Expires=1750223005&Signature=aP3rKIt5WF%2FZznolSxEUb2EnLCQ%3D)

Ở đây nó giảm 267$ cho 1 lần
![image](https://hackmd.io/_uploads/HJzOcG7Wxx.png)
Vậy để giảm hết 1337$ thì cần khoảng 5 lần áp dụng là được.
Thì trong burpsuite có chức năng send group (parallel) sẽ giúp ta gửi request hàng loạt

![image](https://hackmd-prod-images.s3-ap-northeast-1.amazonaws.com/uploads/upload_f7fcc5b4767fa5df9a0953af82542b9d.png?AWSAccessKeyId=AKIA3XSAAW6AWSKNINWO&Expires=1750223024&Signature=Pyi%2Fb72iYoV2rGS%2BhfM8Yei2Ya0%3D)

Thử với 5 request thì có vẻ chỉ dính 3 request

![image](https://hackmd-prod-images.s3-ap-northeast-1.amazonaws.com/uploads/upload_7de64793b7172f558b3ef3fd0357da68.png?AWSAccessKeyId=AKIA3XSAAW6AWSKNINWO&Expires=1750223031&Signature=AUPTcfVGKCDClkfAtcZbkjX6DPg%3D)

Giờ ta tăng số request lên và thử lại thì nó đã giảm xuống còn 37$ vậy giờ mua thôi

![image](https://hackmd-prod-images.s3-ap-northeast-1.amazonaws.com/uploads/upload_2cddd2fc0ae229213f0e9459428a0311.png?AWSAccessKeyId=AKIA3XSAAW6AWSKNINWO&Expires=1750223040&Signature=pPhU4qIwC1FgBDr9nY6RKlHI99A%3D)

Và solve được lab

![image](https://hackmd-prod-images.s3-ap-northeast-1.amazonaws.com/uploads/upload_6b44e81df562d4a4dc24605ffab80ff9.png?AWSAccessKeyId=AKIA3XSAAW6AWSKNINWO&Expires=1750223047&Signature=%2FrMHSfGe4mRpEkFiv7PPDGrexjU%3D)

## Lab: Bypassing rate limits via race conditions
Mục tiêu là bypass limit bằng race và login với tài khoản admin sau đó xóa acc carlos.
Ở đây cũng cho sẵn list password của admin nên mình lưu nó về file pass.txt sau đó dùng script python để này race và bruteforce
