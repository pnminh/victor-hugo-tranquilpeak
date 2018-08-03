---
title: Tạo một trang blog hoàn toàn miễn phí -Phần 1
thumbnailImage: /images/uploads/blog.jpg
thumbnailImagePosition: left
coverImage: /images/uploads/blog.jpg
metaAlignment: center
coverMeta: out
date: '2018-07-30T20:07:17-05:00'
description: 'Tạo blog miễn phí với Netlify, Hugo, và GitHub'
tags:
  - tutorial
categories:
  - thu thuat
  - tutorials
---
Trong series này, mình sẽ hướng dẫn các bạn tạo 1 trang blog cá nhân với đầy đủ tính năng và hoàn toàn miễn phí. Sau khi hoàn thành tất cả các bước dưới đây, các bạn sẽ có thể tạo 1 trang web của riêng mình để viết blog, hoặc phục vụ những mục đích khác như giao bán hàng hoặc giới thiệu sản phẩm trên mạng.

Do nội dung khá dài, mình sẽ chia nó làm nhiều phần nhỏ tập trung vào một mục riêng biệt. Phần đầu của bài viết sẽ giới thiệu với các bạn về Github, một trang lưu trữ mã nguồn (source codes) quen thuộc của giới lập trình viên.

**I. Tài khoản GitHub**

Khi bạn mở một trang web bất kỳ, ví dụ như Google.com trên một trình duyệt như Chrome hoặc Firefox, trình duyệt sẽ tải nội dung từ máy chủ của Google. Bạn có thể tưởng tượng máy chủ giống như cửa hàng KFC, còn nội dung mà máy chủ cung cấp là những món combo gà rán mà KFC làm và bán cho bạn. Khi trình duyệt tải website từ máy chủ Google về và hiển thị trên máy tính bàn của bạn, nó tương tự như việc bạn gọi món từ KFC và tiêu thụ. 

Thông thường mỗi cửa hàng KFC sẽ có một phòng để chứa thịt gà chưa chế biến và gia vị tẩm ướp. Những nguyên liệu thô để tạo 1 trang web cũng cần được lưu ở một nơi nào đó, và đó chính là lúc bạn cần đến GitHub để cất giữ mã nguồn website của mình. Điểm khác biệt giữa GitHub và các sản phẩm lưu trữ cá nhân như Google Drive là khả năng quản  lịch sử của tất cả các trang (documents) lưu trên GitHub. Nếu bạn vô tình xoá nhầm hoặc chỉnh sửa 1 file nào đó mà sau đó muốn phục hồi lại phiên bản gốc thì việc đó hoàn toàn đơn giản và dễ dàng.

**1. Đăng ký tài khoản**

Mở trình duyệt và vào trang github.com, nhấp vào mục sign up để đăng ký một tài khoản trên GitHub.
![null](/images/uploads/github1.png)

**2. Copy mã nguồn từ 1 tài khoản GitHub khác**  

Một trong những điều tuyệt vời nhất của GitHub là bạn có thể tải/copy rất nhiều mã nguồn mở (open source) từ tài khoản GitHub của những công tuy, cá nhân, hoặc cộng đồng mạng về tài khoản GitHub của bạn và sau đó sửa đổi mã nguồn này theo ý thích của mình. 
Từ trình duyệt của bạn, mở trang web với đường link https://github.com/netlify/victor-hugo. Bạn sẽ thấy nút Fork ở phía trên góc phải của màn hình. Click vào đó và GitHub sẽ copy toàn bộ mã nguồn của trang web về tài khoản của bạn.

![Fork a repository](/images/uploads/screen-shot-2018-08-03-at-4.21.37-pm.png)

3. Kết nối tài khoản với máy tính cá nhân
