# BÁO CÁO KIỂM TRA ĐỘ CAO PHÙ HỢP CỦA VỰC CHO CHỨC NĂNG RAMP

- **Project:** PrototypeRacing (2h estimated)
- **Test Objective:** Thu thập thông tin trạng thái hoạt động vật lý của xe trong các trường hợp độ cao của vực khác nhau. Từ đó có thể tìm được độ cao vực tiêu chuẩn cho việc xây dựng map sau này để đảm bảo vật lý xe hoạt động ổn định, không lỗi.

---

## CAR

| No  | Name     | Length |
|-----|----------|--------|
| 1   | VF LuxA  | ~5m    |
| 2   | VF8      | ~5m    |
| 3   | Mercedes | ~5m    |

Khi tiếp đất, độ dài của xe có thể ảnh hưởng đến quá trình tiếp đất có ổn định hay không.
-> Các xe có độ dài gần như bằng nhau nên sẽ dùng 1 xe để test trên các vực có độ cao là H = a.L khác nhau (a: tham số ngẫu nhiên, L: độ dài xe đua)

[Xem Hình 1](Image/HeightRamp/image1.png)
[Xem Hình 2](Image/HeightRamp/image2.png)
[Xem Hình 3](Image/HeightRamp/image3.png)

---

## Case 1: a < 1 (a=0,6), H = 3m

| No  | Case              | Ref | Behavior |
|-----|-------------------|-----|----------|
| 1   | Tốc độ < 50km/h   | [Video](https://drive.google.com/file/d/1Sskg6UrntmiyAKLVe42DiPwa9vQybDrK/view?usp=drive_link) | Đầu xe chạm đất trước khi đuôi xe rời vực, cảm giác dính collision, tiếp đất mạnh |
| 2   | Tốc độ < 80km/h   | [Video](https://drive.google.com/file/d/1Q0xGnFk_Zl1jDQLdABYlrq-RgWLcIXxN/view?usp=drive_link) | Đầu xe chạm đất khi đuôi xe vừa rời vực, cảm giác dính collision nhẹ hơn case 1 |
| 3   | Tốc độ < 120km/h  | [Video](https://drive.google.com/file/d/1o8dU0YJ4cXgxvHP1KWJ1TdQ3vSHE-QUn/view?usp=drive_link) | Đầu xe chạm đất khi đuôi xe vừa rời vực, nhưng nhờ tốc độ cao cảm giác dính collision rất ít |
| 4   | Tốc độ > 120 km/h | [Video](https://drive.google.com/file/d/11zaLDBqP2-i1ylmsRvvrOrBWGA3NUPaj/view?usp=drive_link) | Tiếp đất ổn định |

[Xem Hình 4](Image/HeightRamp/image4.png)

---

## Case 2: a = 1, H = 5m

| No  | Case              | Ref | Behavior |
|-----|-------------------|-----|----------|
| 1   | Tốc độ < 50km/h   | [Video](https://drive.google.com/file/d/1WMZsvlveKfpJG9YBqy53ysOLdgdrzspE/view?usp=drive_link) | Đầu xe chạm đất khi đuôi xe vừa rời vực, cảm giác dính collision nhẹ |
| 2   | Tốc độ < 80km/h   | [Video](https://drive.google.com/file/d/1TTVHC09wJQ_1t37UOTROcJrARCJKWV4O/view?usp=drive_link) | Đầu xe chạm đất khi đuôi xe vừa rời vực, cảm giác dính collision nhẹ |
| 3   | Tốc độ < 120km/h  | [Video](https://drive.google.com/file/d/1ag067K65SQKNzBZ4prFHvH8Nc6qrF7v4/view?usp=drive_link) | Đầu xe chạm đất khi đuôi xe vừa rời vực, cảm giác dính collision nhẹ |
| 4   | Tốc độ > 120 km/h | [Video](https://drive.google.com/file/d/15Jp8UM4bXSX_D_dKybksskGHXpATueR-/view?usp=drive_link) | Tiếp đất ổn định |

[Xem Hình 5](Image/HeightRamp/image5.png)

---

## Case 3: a > 1 (a=1,5), H = 7,5m

| No  | Case              | Ref | Behavior |
|-----|-------------------|-----|----------|
| 1   | Tốc độ < 50km/h   | [Video](https://drive.google.com/file/d/1m1CEd_OktDmbRXWG2r_EORF6gFpbh0iG/view?usp=drive_link) | Đầu xe hướng xuống đất, chưa kịp cân bằng khi chạm đất |
| 2   | Tốc độ < 80km/h   | [Video](https://drive.google.com/file/d/18ifeIu0GJq0UXrTmZ8Vpxsa0myOv9b3E/view?usp=drive_link) | Đầu xe hướng xuống đất, chưa kịp cân bằng khi chạm đất |
| 3   | Tốc độ < 120km/h  | [Video](https://drive.google.com/file/d/1JegQvJ2A0zc9SfsYh-u6VYmyNinDOsXg/view?usp=drive_link) | Tiếp đất ổn định khoảng 50% |
| 4   | Tốc độ > 120 km/h | [Video](https://drive.google.com/file/d/1XQw2AEFKgw1FIWobqCvzM7f-I5WB75sQ/view?usp=drive_link) | Tiếp đất ổn định |

[Xem Hình 6](Image/HeightRamp/image6.png)

---

## Case 4: a > 2 (a=2,5), H = 12,5m

| No  | Case              | Ref | Behavior |
|-----|-------------------|-----|----------|
| 1   | Tốc độ < 50km/h   | [Video](https://drive.google.com/file/d/1ob1-ZWPC5dpWdPQt7tpBcEKQQOInXk4O/view?usp=drive_link) | Đầu xe hướng xuống đất, nhưng đã cân bằng trước khi chạm đất, tiếp đất ổn định |
| 2   | Tốc độ < 80km/h   | [Video](https://drive.google.com/file/d/1GvZXGORQ3rGH4I3bv7vFsQsSisnHGs8_/view?usp=drive_link) | Đầu xe hướng xuống đất, nhưng đã cân bằng trước khi chạm đất, tiếp đất ổn định |
| 3   | Tốc độ < 120km/h  | [Video](https://drive.google.com/file/d/1Pyoa5cJw9cdGObrCfz73WQus10E58QQ4/view?usp=drive_link) | Tiếp đất ổn định |
| 4   | Tốc độ > 120 km/h | [Video](https://drive.google.com/file/d/1PCKPP8k0KtmFzi7M3LhE7hQo8sXFlczt/view?usp=drive_link) | Tiếp đất ổn định |

[Xem Hình 7](Image/HeightRamp/image7.png)

---

## CONCLUSION

Để vật lý xe hoạt động ổn định khi sử dụng Ramp / rời khỏi vực, độ cao trung bình của vực cần lớn hơn hoặc bằng 2,5 lần chiều dài của xe (>12,5m).

Có thể thêm logic làm cứng Rotation của thân xe (không cho đầu xe hướng xuống) khi vừa rời khỏi vực để đạt trạng thái cân bằng sớm hơn.
