**Cách xem Debug Của phần Ramp:**   
Vào Game Debug  → Track Logic  → AirBorne  
[Hình 1](Image/DebugRamp/image1.png)

Debug gồm các tính năng sau:

1. **Xem trạng thái hiện tại của xe**  
     
- Gồm 3 trạng thái:  
* Grounded**:** xe đang bám mặt đất bình thường, hệ thống treo hoạt động đầy đủ, không áp dụng logic bay.

* Airborne**:** xe đã rời khỏi mặt đất (nhảy ramp hoặc rơi), đang ở trạng thái bay. Khi đó:  
  \+ Áp dụng gravity động theo độ cao  
  \+ Có thể kích hoạt auto balance


* Landing**:** xe vừa chạm đất sau khi bay:  
  \+ Kiểm tra góc tiếp đất (Pitch / Roll)  
  \+ Nếu tiếp đất xấu → chuyển sang Crash Landing  
    
  [Hình 2](Image/DebugRamp/image2.png)  
2. **Xem thời gian xe đã bay**  
   Debug hiển thị:  
- Thời gian xe đã bay kể từ lúc rời mặt đất

[Hình 3](Image/DebugRamp/image3.png)

3. **Trạng thái Tự Động Cân Bằng**  
   Có ý nghĩa: Xe đang tự động chỉnh lại Pitch / Roll   
- Tránh xe: Lật ngang   
- Chúc đầu quá mạnh   
- Tiếp đất ở tư thế quá xấu  
- Sẽ hiện Debug khi tự động cân bằng đang được áp dụng.  
  [Hình 4](Image/DebugRamp/image4.png)

4. **Xem lực kéo xe xuống đất**  
   Debug hiển thị:   
- Mũi tên chỉ xuống dưới (Gravity Vector)   
- Giá trị Gravity hiện tại 

Ý nghĩa: Đây là gravity bổ sung theo độ cao. Khi: 

- Xe càng gần mặt đất → gravity càng lớn → lực hút xuống đất sẽ càng mạnh


  [Hình 5](Image/DebugRamp/image5.png)


5. **Xem Rotation của xe**  
   Debug hiển thị:   
   Góc của xe  
   Giá trị:   
- Roll: Nghiêng trái/phải   
- Pitch: Chúc đầu/ngửa đầu   
- Yaw: Hướng quay ngang


  [Hình 6](Image/DebugRamp/image6.png)


6. **Xem thời gian và thanh tiến trình khi xe trong trạng thái Crash**

	Khi xe tiếp đất với góc quá lớn:

- Debug hiện dòng “\!\! CRASH \!\!”  
- Xuất hiện thanh tiến trình màu đỏ

Thanh này thể hiện: Thời gian recovery sau crash

Thanh đỏ sẽ:

- Đầy dần theo thời gian  
- Khi đầy → xe được reset / ổn định lại

[Hình 7](Image/DebugRamp/image7.png)

7. **Xem quỹ đạo xe bay**  
- Bước 1: bấm Tick chọn xem debug của Ramp như trên  
- Bước 2: Khi xe chuẩn bị bay, bấm nút Camera Left của phần Debug


  [Hình 8](Image/DebugRamp/image8.png)


- Xem kết quả  
  **[Hình 9](Image/DebugRamp/image9.png)**
