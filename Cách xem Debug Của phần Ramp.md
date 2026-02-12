# ME08 - Ramp & Airborne Mechanics

## Debug Guide – Ramp System

---

## Quick Reference

Tài liệu này hướng dẫn cách sử dụng hệ thống Debug của Ramp & Airborne Mechanics (ME08), giúp kiểm tra trạng thái xe, lực tác động và hành vi khi bay.

---

## Part 1: DEBUG THÔNG TIN

### 1. Current Airborne State

Ý nghĩa:  
Hiển thị trạng thái hiện tại của xe khi tương tác với ramp và airborne.

Giá trị gồm:

- Grounded  
Xe đang bám mặt đất bình thường.  
Hệ thống treo hoạt động đầy đủ.  
Không áp dụng logic bay.

- Airborne  
Xe đã rời khỏi mặt đất (nhảy ramp hoặc rơi).  
Áp dụng gravity động theo độ cao.  
Có thể kích hoạt Auto Balance.

- Landing  
Xe vừa chạm đất sau khi bay.  
Kiểm tra góc tiếp đất (Pitch / Roll).  
Nếu tiếp đất xấu → chuyển sang Crash Landing.

[Xem Hình 1](Image/DebugRamp/image1.png)  
[Xem Hình 2](Image/DebugRamp/image2.png)

---

### 2. Time In Air

Ý nghĩa:  
Hiển thị tổng thời gian xe ở trạng thái Airborne trong lần nhảy hiện tại.

Thời gian được tính từ lúc xe rời mặt đất đến khi tiếp đất.

[Xem Hình 3](Image/DebugRamp/image3.png)

---

### 3. Auto Balance State

Ý nghĩa:  
Cho biết hệ thống tự động cân bằng đang hoạt động.

Hệ thống sẽ tự điều chỉnh:

- Pitch  
- Roll  

Nhằm tránh:

- Lật ngang  
- Chúc đầu quá mạnh  
- Tiếp đất ở tư thế xấu  

[Xem Hình 4](Image/DebugRamp/image4.png)

---

### 4. Gravity Vector & Gravity Value

Debug hiển thị:

- Mũi tên chỉ xuống (Gravity Vector)  
- Giá trị Gravity hiện tại  

Ý nghĩa:

Đây là gravity bổ sung theo độ cao.

Xe càng gần mặt đất → gravity càng lớn.  
Lực hút xuống đất mạnh hơn.

[Xem Hình 5](Image/DebugRamp/image5.png)

---

### 5. Vehicle Rotation

Debug hiển thị:

- Roll → Nghiêng trái/phải  
- Pitch → Chúc đầu/ngửa đầu  
- Yaw → Hướng quay ngang  

[Xem Hình 6](Image/DebugRamp/image6.png)

---

### 6. Crash State & Recovery Progress

Khi xe tiếp đất với góc quá lớn:

Hiện dòng: !! CRASH !!  
Xuất hiện thanh tiến trình màu đỏ.

Ý nghĩa:

Thanh đỏ thể hiện thời gian Recovery sau Crash.

Thanh đầy dần theo thời gian.  
Khi đầy → xe được reset / ổn định lại.

[Xem Hình 7](Image/DebugRamp/image7.png)

---

### 7. Xem Quỹ Đạo Xe Bay

Bước 1:  
Tick chọn Debug của Ramp trong Game Debug.

Bước 2:  
Khi xe chuẩn bị bay, bấm nút Camera Left trong phần Debug.

[Xem Hình 8](Image/DebugRamp/image8.png)

Kết quả:

[Xem Hình 9](Image/DebugRamp/image9.png)
