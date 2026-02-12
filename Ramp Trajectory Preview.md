# RAMP TRAJECTORY PREVIEW - HƯỚNG DẪN SỬ DỤNG

## 1. Giới thiệu

- **Mô tả**: Tính năng preview đường bay của xe trong Editor
- **Mục đích**: Giúp điều chỉnh các thông số cần thiết để dự đoán khoảng cách xe sẽ bay trực tiếp trong Editor.

---

## 2. Công thức tính khoảng cách

**Distance (m) = -22 + 0.5 × Speed + 8 × Boost + 0.35 × Angle**

| Parameter | Mô tả | Đơn vị | Range |
| :---- | :---- | :---- | :---- |
| Speed | Tốc độ xe | km/h | 50 - 300 |
| Boost | Hệ số boost (tương ứng với tốc độ xe bao nhiêu sẽ có hệ số boost tương ứng bấy nhiêu) - được giải thích chi tiết ở phần dưới. | X | 1.0 - 2.0 |
| Angle | Góc xe bay | Độ (°) |  |

### Ví dụ

- Speed = 100, Boost = 1.5, Angle = 17
- Distance ≈ 46 m
- Speed = 200, Boost = 1.75, Angle = 19.8
- Distance ≈ 99 m

---

### Giải thích cách lựa chọn hệ số Boost

- Biểu đồ dưới đây mô tả mối quan hệ giữa tốc độ xe (Normalized Speed) và hệ số lực Boost khi nhảy ramp.
- Ở tốc độ càng cao thì hệ số lực Boost càng lớn
- Ở tốc độ càng thấp thì hệ số lực Boost càng nhỏ

- **Trục X** – Normalized Speed (0 → 1): đại diện cho tốc độ xe khi lao lên ramp, đã được chuẩn hóa

  Công thức:
  **NormalizedSpeed = Clamp(CurrentSpeed / MaxSpeedForBoostCurve, 0 → 1)**

  [Xem Hình 1](Image/Trajectory/image1.png)

- **Trục Y** – Boost Multiplier: là hệ số nhân lực boost. Giá trị này không có đơn vị.

  Với tốc độ xe đã chuẩn hóa sẽ có hệ số tương ứng.

  [Xem Hình 2](Image/Trajectory/image2.png)

- Biểu đồ **Boost Multiplier Curve:**

  [Xem Hình 3](Image/Trajectory/image3.png)

  **Hình 1: Boost Multiplier Curve theo Normalized Speed**

  **Ví dụ:**
  - Khi xe có vận tốc 90 km/h bay qua ramp
  - NormalizedSpeed = 90 / 200 = 0.45
  - Boost = 1.34

---

## 3. Cách sử dụng trong Editor

### Bước 1: Mở Level Racing Test RampBoost

[Xem Hình 4](Image/Trajectory/image4.png)

---

### Bước 2: Chọn Ramp trong Viewport

[Xem Hình 5](Image/Trajectory/image5.png)

---

### Bước 3: Mở Details Panel và search Trajectory Preview

[Xem Hình 6](Image/Trajectory/image6.png)

---

### Bước 3: Enable Preview

Tick **☑️** Show Trajectory Preview:

[Xem Hình 7](Image/Trajectory/image7.png)

---

### Bước 4: Điều chỉnh tham số

- Có thể chỉnh được tham số PreviewSpeedKph trong Details. Khi chỉnh PreviewSpeedKph thì tham số PreviewBoostMult sẽ tự động cập nhật theo dữ liệu.

  [Xem Hình 8](Image/Trajectory/image8.png)

- Chỉnh PreviewBoostMult bằng cách mở file sau:

  [Xem Hình 9](Image/Trajectory/image9.png)

  Sau đó chỉnh hệ số Boost theo mong muốn:

  [Xem Hình 3](Image/Trajectory/image3.png)

- Sau khi chỉnh hoàn tất nhấn nút RefreshTrajectoryPreview thì hình vẽ quỹ đạo bay sẽ được tự động cập nhật lại.

  [Xem Hình 10](Image/Trajectory/image10.png)

- Chỉnh PreviewRampAngle bằng cách chỉnh trực tiếp actor Ramp trong Editor bằng cách nhấn phím R

  [Xem Hình 11](Image/Trajectory/image11.png)

---

### Bước 5: Xem kết quả

- Đường Parabol màu vàng cho biết quỹ đạo dự đoán xe sẽ bay.
- Vòng tròn đỏ ở điểm cuối Parabol cho biết vị trí dự đoán xe sẽ rơi xuống.
- Khoảng cách thực tế trong gameplay có thể chênh lệch do va chạm, Gravity và input người chơi.

[Xem Hình 12](Image/Trajectory/image12.png)

[Xem Hình 13](Image/Trajectory/image13.png)

[Xem Hình 14](Image/Trajectory/image14.png)