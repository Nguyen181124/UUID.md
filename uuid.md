# UUID trong Laravel: 5 câu hỏi thường gặp

## Câu 1. Khi nào nên sử dụng UUID? Trong trường hợp nào?
UUID rất tốt cho một số trường hợp:
- Tạo ID duy nhất cho mô hình của bạn và không phải lo lắng về xung đột (trùng lặp)
- Tạo ID duy nhất cho mô hình của bạn và không phải lo lắng về việc lộ số lượng bản ghi bạn có trong cơ sở dữ liệu của mình
- Tạo ID duy nhất cho mô hình của bạn và không phải lo lắng về việc hiển thị thứ tự các bản ghi trong cơ sở dữ liệu

Nếu chúng ta xem xét UUID V4 trông như thế này `'f47ac10b-58cc-4372-a567-0e02b2c3d479'` - chúng ta có thể khá chắc chắn rằng các giá trị được tạo sẽ luôn là duy nhất. Khả năng xảy ra va chạm là dưới 1 trên 2,71 triệu (rất thấp!), vì vậy bạn có thể yên tâm sử dụng chúng cho mô hình của mình. Một lợi ích khác khi làm như vậy là bạn có thể ẩn chi tiết trong URL của mình. Đã bao nhiêu lần bạn nhìn thấy một URL như thế này?

`domain.com/shop/orders/1596115`

Điều này cho bạn biết rằng có ít nhất 1.596.115 đơn hàng trong cơ sở dữ liệu. Và mọi người có thể thử thay đổi số lượng xem có thêm đơn hàng nào không. Nhưng nếu bạn sử dụng UUID, bạn có thể ẩn thông tin đó:

`domain.com/shop/orders/f47ac10b-58cc-4372-a567-0e02b2c3d479`

Giờ đây, người dùng của bạn sẽ không biết bạn có bao nhiêu đơn hàng và họ sẽ không có cơ hội đoán chính xác đơn hàng tiếp theo hoặc trước đó (trừ khi họ cực kỳ may mắn!). Nó cũng mang lại cho bạn sự bảo mật cao hơn một chút, vì bạn không thể chỉ thay đổi 1 số trong URL và cố gắng xem đơn đặt hàng của người khác. Nó cũng đặc biệt hữu ích nếu bạn có một ứng dụng có nhiều bên thuê.

Để tạo UUID V4 trong Laravel, bạn có thể sử dụng:


```php
Ramsey\Uuid\Uuid::uuid4()->toString();
// Or
Illuminate\Support\Str::uuid()->toString();
```

Nó sẽ cung cấp cho bạn một chuỗi như thế này: `81239d02-9334-4051-b72f-e3409dc4310a`. Trình trợ giúp Str là một trình bao bọc xung quanh thư viện Ramsey, vì vậy bạn có thể sử dụng một trong hai.

## Câu hỏi 2. UUID chiếm bao nhiêu dung lượng?

Nếu chúng ta so sánh kích thước của trường UUID với trường `bigInt`, chúng ta có thể thấy rằng UUID chiếm dung lượng gấp đôi. Điều này là do UUID được lưu dưới dạng chuỗi, trong khi bigInt được lưu dưới dạng số.

## Câu hỏi 3. UUID có chậm không?

Nếu so sánh tốc độ của trường UUID với trường `bigInt`, chúng ta có thể thấy UUID chậm hơn gấp 2 lần. Điều này là do UUID được lưu dưới dạng chuỗi, trong khi bigInt được lưu dưới dạng số. Nhưng điều này chỉ đáng chú ý nếu bạn có cơ sở dữ liệu lớn. Nếu bạn có một cơ sở dữ liệu nhỏ - bạn sẽ không nhận thấy bất kỳ sự khác biệt nào.

## Câu 4. UUID và ULID? Có gì khác biệt?

Sự khác biệt chính giữa UUID và ULID là cách chúng được tạo. UUID được tạo bằng trình tạo số ngẫu nhiên, trong khi ULID được tạo bằng dấu thời gian và trình tạo số ngẫu nhiên. Điều này có nghĩa là ULID có thể sắp xếp được, còn UUID thì không. Điều này cũng có nghĩa là ULID không độc đáo như UUID vì nó sử dụng dấu thời gian. Nhưng nó vẫn đủ độc đáo cho hầu hết các trường hợp.

Nếu muốn xem UUID và ULID trông như thế nào, chúng ta có thể tạo một vài trong số chúng:

```php
dd(
    \Illuminate\Support\Str::uuid()->toString(),
    \Illuminate\Support\Str::ulid()->toBase32()
);
```
Điều này sẽ cho chúng ta kết quả sau:

**UUID**

> *"28f39d8d-4972-4d59-a642-248c453a6662 c8e43384-17b6-44ce-8d16-0aefc6e3eb4c"*

**ULID**
> *01H6ET312T05WVXVGSF0V0KXW7 01H6ET73YNZMJ2JWJPDHS5ADKC*

Như bạn có thể thấy, ULID được tạo theo thứ tự, trong khi UUID thì không.

## Câu 5. UUID là Khóa Chính hay Cột Riêng?

Khi nói về việc sử dụng UUID làm khóa chính hoặc cột riêng biệt, có một số điều cần cân nhắc:

- Sử dụng UUID làm khóa chính sẽ khiến cơ sở dữ liệu của bạn chậm hơn vì nó sẽ phải tìm kiếm một chuỗi thay vì một số

- Sử dụng UUID làm khóa chính sẽ khiến thao tác chèn của bạn chậm hơn, vì MySQL sẽ phải cập nhật chỉ mục để được "sắp xếp" để có hiệu suất tốt hơn. Việc này mất nhiều thời gian hơn vì đây là một chuỗi chứ không phải int đã được đặt hàng

- Sử dụng UUID làm khóa chính sẽ chiếm nhiều dung lượng hơn vì nó sẽ phải lưu trữ một chuỗi thay vì một số

- Sử dụng UUID làm khóa chính sẽ giúp cơ sở dữ liệu của bạn an toàn hơn vì việc đoán bản ghi tiếp theo hoặc trước đó sẽ khó hơn. Nhưng điều đó đi kèm với cái giá phải trả là tốc độ

Một cách tiếp cận tốt hơn có thể là:

- Việc sử dụng UUID làm cột riêng biệt sẽ khiến bạn dễ vô tình sử dụng sai ID hơn, vì bạn sẽ có 2 ID để làm việc

- Sử dụng UUID làm cột riêng biệt sẽ cho phép bạn sử dụng ID ngắn hơn cho các mối quan hệ

Điều đó nói lên rằng, mọi thứ đều phụ thuộc vào hiệu suất mà bạn cần cho cơ sở dữ liệu của mình. Nếu không có nhiều dữ liệu - bạn có thể ổn với UUID làm khóa chính, nhưng nếu tốc độ là quan trọng - hãy sử dụng nó làm cột riêng biệt.

Link bài dịch: https://laraveldaily.com/post/uuid-in-laravel-common-questions 
