#MVC Pattern Preview

---
- [Giới thiệu](#giới-thiệu)
- [Luồng xử lý trong MVC](#luồng-xử-lý-trong-mvc)
- [Ưu và nhược điểm của MVC pattern](#ưu-và-nhược-điểm-của-mvc-pattern)
---
## Giới thiệu

Giới thiệu tổng quan về mô hình MVC

- MVC là viết tắt của cụm từ **Model, View, Controller**
- Là một mô hình thiết kế được sử dụng trong phát triển phần mềm
- Một số mô hình khác có thể kể đến là **3 tire, Microservice**
- Là một **Mẫu kiến trúc phần mềm** để tạo giao diện người dùng trên máy tính
- **MVC** chia hệ thống thành 3 phần khác nhau với các vai trò riêng biệt, rõ ràng và độc lập với nhau:
  - **Model**:
    - Chứa dữ liệu nguyên thuỷ của ứng dụng (**pure application data**)
    - Chứa các **operation, class** nhằn nhiệm vụ tương tác dữ liệu với **Database**
    - **Không** chứa các operation thể hiện logic làm thế nào để data đưa đến cho người dùng
  - **View**:
    - Là giao diện người dùng (User Interface)
    - Chứa các thành phần tương tác với người dùng như menu, button, image, text,...
    - Hiển thị data từ **Model** đến phía người dùng
    - View đơn thuần chỉ **display data**
  - **Controller**:
    - Là thành phần trung gian giữa **Model và View**
    - Nơi mà các **busiess logic** được viết
    - Có nhiệm vụ lắng nghe **action** từ phía view và thực thi các operation tương ứng
    - Trong hệ thống lớn hơn, Controller còn có nhiệm vụ khác là **điều phối luồng hoạt động của User** (Sẽ nói vào một Topic cụ thể)

## Luồng xử lý trong MVC

Luồng xử lý trong MVC rất đơn giản, gồm các bước sau:

![Luồng xử lý trong MVC](./images/MVC.png)

1. Khi user gửi Request đến thanh trình duyệt, Controller sẽ đảm nhận Request đó.
2. Controller sẽ lấy data từ Model, nhào nặn data nhờ các **Business Logic**
3. Sau đó, chuyển data về phía View và hiển thị cho người dùng

Rất đơn giản phải không!!

## Ưu và nhược điểm của MVC pattern

Một số ưu điểm có thể kể đến của MVC pattern:

- Nhiều nhà phát triển có thể làm việc đồng thời trên model, controller và các views.
- MVC cho phép nhóm hợp lý các hành động liên quan trên một bộ controller với nhau (CRUD chẳng hạn)
- Các views cho một mô hình cụ thể cũng được nhóm lại với nhau.
- Models có thể có nhiều views.

Nhược điểm:

- Điều hướng khung có thể phức tạp vì nó giới thiệu các lớp trừu tượng mới và yêu cầu người dùng thích ứng với các tiêu chí phân tách của MVC (Phải chắc kiến thức về phần mềm - ý là vậy)
- Kiến thức về nhiều công nghệ trở thành tiêu chuẩn. Các nhà phát triển sử dụng MVC cần phải có kỹ năng trong nhiều công nghệ.

Tóm lại, MVC pattern là mô hình được các lập trình viên sử dụng, tuy nhiên cần có kiến thức chắc về MVC thì mới có thể hiểu MVC pattern một cách rõ ràng được.
