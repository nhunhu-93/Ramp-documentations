# ME08 - Ramp & Airborne Mechanics

## Quick Reference

Tài liệu này giải thích tất cả tham số và hàm của hệ thống Ramp & Airborne Mechanics (ME08), cho phép tinh chỉnh cảm giác nhảy ramp, kiểm soát trên không và hồi phục khi tiếp đất.

---

## Part 1: THAM SỐ

## Airborne State

### 1. **CurrentAirborneState**
- **Loại**: Enum (`EVehicleAirborneState`)
- **Ý nghĩa**:  
  Trạng thái hiện tại của xe liên quan đến ramp và airborne
- **Giá trị**:
  - `Grounded` – Xe đang chạy bình thường trên mặt đất
  - `Airborne` – Xe đang bay trên không
  - `Landing` – Xe vừa tiếp đất và đang hồi phục
- **Giải thích**:
  - State này quyết định logic vật lý nào được áp dụng

---

### 2. **TimeInAir** = 0.0
- **Loại**: Float (seconds)
- **Ý nghĩa**:  
  Tổng thời gian xe đã ở trạng thái Airborne trong lần nhảy hiện tại
- **Phạm vi**: `≥ 0`
- **Giải thích**:
  - Giá trị tăng dần theo thời gian khi xe đang bay trên không
  - Reset khi xe tiếp đất

---

### 3. **MaxHeightReached** = 0.0
- **Loại**: Float (cm)
- **Ý nghĩa**:  
  Độ cao tối đa mà xe đạt được trong lần nhảy hiện tại
- **Phạm vi**: `≥ 0`
- **Giải thích**:
  - Cập nhật khi `CurrentHeight` lớn hơn giá trị trước đó

---

## BP_SimulatePhysicsCar - Air Control Settings

### Air Control Settings Group

#### 4. **GroundSteeringSensitivity** = 1.0
- **Loại**: Float (sensitivity ratio)
- **Ý nghĩa**: Độ nhạy của lái xe khi ở trên mặt đất (ground)
- **Ghi chú**: Thường giữ = 1.0, chỉ điều chỉnh Air Steering Multiplier

---

#### 5. **AirSteeringMultiplier** = 0.75
- **Loại**: Float (multiplier)
- **Ý nghĩa**: Hệ số nhân độ nhạy lái khi xe ở trạng thái airborne (bay trên không)
- **Giải thích**:
  - Khi bất kỳ lúc nào xe bay (bInAir = true), sensitivity lái sẽ bị nhân với giá trị này
  - `ReducedSteeringInput = SteeringInput × AirControlSettings.AirSteeringMultiplier`
  - `= 1.0 × 0.5 = 0.5` → lái trên không nhạy bằng 50% so với dưới đất
- **Điều chỉnh**:
  - 0.5f theo yêu cầu Design
- **Ghi chú**: Không ảnh hưởng đến Yaw/Roll/Pitch Strength (torque), chỉ ảnh hưởng input sensitivity

---

#### 6. **YawStrength** = 2.0
- **Loại**: Float (torque magnitude)
- **Ý nghĩa**: Độ mạnh của lực xoay ngang (quanh trục Z) khi xe bay
- **Giải thích**:
  - Quanh trục Z = xoay trái/phải
  - `Yaw Strength = 2.0` → bấm lái trái/phải sẽ tạo xoay ngang với độ mạnh 2.0
  - Ảnh hưởng trực tiếp đến tốc độ quay (rotation rate)
- **Ghi chú**: Thường có Air Steering Multiplier hạn chế input, nên Yaw Strength cao vẫn không quá mạnh

---

#### 7. **RollStrength** = 2.0
- **Loại**: Float (torque magnitude)
- **Ý nghĩa**: Độ mạnh của lực lật ngang (quanh trục X) khi xe bay
- **Phạm vi**: 1.0 - 8.0
- **Giải thích**:
  - Quanh trục X = lật ngang, xe ngả sang trái/phải
  - Dùng chủ yếu cho trick aerial roll, cảnh quay ngang
  - Nếu quá cao → xe dễ tự lật khó kiểm soát
- **Điều chỉnh**:
  - Cao (5.0-6.0): Cho phép trick roll aerial
    - Dùng: Freestyle mode
  - Vừa phải (3.0-4.0): Kiểm soát roll nhưng vẫn có anti-roll
    - Dùng: Hầu hết gameplay
  - Thấp (1.5-2.0): Khó lật, an toàn
    - Dùng: Racing mode strict
- **Ghi chú**: Thường thấp hơn Yaw Strength vì roll dễ gây lật khó phục hồi

---

#### 8. **PitchStrength** = 4.0
- **Loại**: Float (torque magnitude)
- **Ý nghĩa**: Độ mạnh của lực ngóc/chúi đầu (quanh trục Y) khi xe bay
- **Phạm vi**: 1.0 - 8.0
- **Giải thích**:
  - Quanh trục Y = nose up/ nose down
  - Ảnh hưởng đến landing angle, có thể gây landing nose-first hoặc tail-first
- **Ghi chú**: Theo Design không áp dụng vào nên chỉnh cũng không ảnh hưởng đến Air Control

---

## Dynamic Gravity System

### 9. **GravityByHeightCurve**
- **Loại**: Curve Float
- **Ý nghĩa**:  
  Curve dùng để điều chỉnh **trọng lực bổ sung dựa trên độ cao của xe so với mặt đường**
- **Giải thích**:
  - Cho phép tạo cảm giác:
    - Ở cao → gravity nhẹ → rơi chậm (floaty)
    - Gần đất → gravity mạnh → rơi nhanh

---

## Ramp Boost Parameters

### 10. **IsRampBoostActive** = false
- **Loại**: Bool
- **Ý nghĩa**:  
  Xác định **trạng thái hiện tại của xe** có đang ở trạng thái ramp boost hay không
- **Giải thích**:
  - True → hệ thống đang coi là ramp boost
  - False → xe không ở trạng thái ramp boost

---

### 11. **RampBoostTimeRemaining** = 0.0
- **Loại**: Float (seconds)
- **Ý nghĩa**:  
  Thời gian còn lại cho phép xe ở trạng thái ramp boost.
- **Phạm vi**: `≥ 0`
- **Giải thích**:  
  - Bắt đầu đếm khi hệ thống xác định ramp boost
  - Dùng để điều khiển quá trình trượt và hồi phục

---

### 12. **RampAccelBoost** = 2000.f
- **Loại**: Float
- **Ý nghĩa**:  
  Tỉ lệ tăng tốc khi xe đi qua ramp
- **Phạm vi**: `> 0`
- **Giải thích**:  
  - Giá trị này được cộng vào tốc độ của xe để tạo lực nhảy
- **Điều chỉnh**:  
  - Tăng → Xe nhảy cao hơn
  - Giảm → Xe nhảy thấp hơn

---

### 13. **RampBoostDuration** = 0.6f
- **Loại**: Float (seconds)
- **Ý nghĩa**:  
  Thời gian tối đa cho phép xe ở trạng thái ramp boost.
- **Phạm vi**: `> 0`
- **Giải thích**:  
  - Khoảng thời gian xe bay trong trạng thái ramp boost
- **Điều chỉnh**:  
  - Tăng → Xe bay xa hơn
  - Giảm → Xe bay gần hơn

---

### 14. **BoostForce** = 800.0
- **Loại**: Float (force units, cùng hệ với ResultantForce)
- **Ý nghĩa**: Độ mạnh của lực đẩy khi xe chạm vào Ramp Zone
- **Phạm vi**: 300 - 1500 (khuyến nghị)
- **Giải thích**:
  - Khi xe overlap với Ramp trigger, hệ thống apply thêm lực theo hướng dốc ramp
  - Lực này được áp dụng 1 lần duy nhất từ khi vào ramp cho đến khi ra khỏi ramp
  - Quyết định xe bay xa hay gần, mạnh hay yếu.

---


### 15. **MaxSpeedForBoostCurve** = 180.0
- **Loại**: Float (cm/s)
- **Ý nghĩa**:  
  Vận tốc tối đa dùng để ** Boost Force Curve **
- **Phạm vi**: `> 0`
- **Giải thích**:
  - Vận tốc hiện tại của xe được chuẩn hóa theo công thức:
    	NormalizedSpeed = FMath::Clamp(CurrentSpeed / MaxSpeedForBoostCurve, 0.0f, 1.0f);
  - Giá trị này được dùng làm trục X của Boost Force Curve
- **Điều chỉnh**:
  - Tăng → Hệ số lực đẩy đạt tối đa chậm hơn
  - Giảm → Hệ số lực đẩy đạt tối đa nhanh hơn

---

### 16. **MaxHeightForBoost** = 700.0
- **Loại**: Float (cm)
- **Ý nghĩa**:  
  Độ cao để áp dụng lực kéo bổ sung để đưa xe trở lại mặt đất
- **Phạm vi**: `> 0`
- **Giải thích**:
  - MaxHeightForBoost xác định độ cao mà tại đó hệ thống bắt đầu áp dụng lực kéo bổ sung để đưa xe trở lại mặt đất.
  Khi xe bay cao hơn MaxHeightForBoost, lực kéo xuống càng mạnh.

---

### 17. **BoostForceCurve**
- **Loại**: Curve Float
- **Ý nghĩa**:  
  Curve điều chỉnh **lực nhảy ramp dựa trên vận tốc xe**
- **Trục X**: Normalized Speed (0.0 – 1.0)  
- **Trục Y**: Boost Multiplier
- **Giải thích**:
  - Xe chạy càng nhanh → lực nhảy càng lớn

---

### Ví dụ về tính tương quan giữa tốc độ và lực Boost khi bay qua ramp

- **Công thức tính NormalizedSpeed**:
	NormalizedSpeed = CurrentSpeed / MaxSpeedForBoostCurve;

- **Chuẩn hoá NormalizedSpeed về khoảng [0.0, 1.0]**

- **Công thức tính FinalBoostForce**:
	FinalBoostForce = BoostForce * BoostMultiplier;

- **Tính toán lực boost cuối cùng**
-> Ở tốc độ càng cao thì lực boost càng lớn
-> Ở tốc độ càng thấp thì lực boost càng nhỏ

- **Ví dụ**:

| CurrentSpeed (km/h) | MaxSpeedForBoostCurve (km/h) | NormalizedSpeed | BoostMultiplier | FinalBoostForce |
|---------------------|------------------------------|-----------------|-----------------|-----------------|
| 50                  | 200                          | 0.25            | 1               | 800             |
| 90                  | 200                          | 0.45            | 1.34            | 1072            |
| 180                 | 200                          | 0.9             | 1.748           | 1398            |



- **Giải thích chi tiết**:
  - Khi xe có vận tốc 90 km/h bay qua ramp
  - NormalizedSpeed = 90 / 200 = 0.45
  - BoostMultiplier = 1.34
  - FinalBoostForce = 800 * 1.34 = 1072
  -> Lực đẩy cuối cùng tác động lên xe là 1072


## Crash Landing Properties

### 18. **IsCrashLanding** = false
- **Loại**: Bool
- **Ý nghĩa**:  
  Xác định **trạng thái hiện tại của xe** có đang ở trạng thái crash landing hay không
- **Giải thích**:
  - True → hệ thống đang coi là crash landing
  - False → xe không ở trạng thái crash landing

---

### 19. **TimeSinceCrashLanding** = 0.0
- **Loại**: Float (seconds)
- **Ý nghĩa**:  
  Thời gian đã trôi qua kể từ khi xe **bị crash / hard landing**
- **Phạm vi**: `≥ 0`
- **Giải thích**:
  - Bắt đầu đếm khi hệ thống xác định crash landing
  - Dùng để điều khiển quá trình trượt và hồi phục

---

### 20. **CrashLandingMaxTime** = 2.0
- **Loại**: Float (seconds)
- **Ý nghĩa**:  
  Thời gian tối đa cho phép xe ở trạng thái **crash landing**
- **Phạm vi**:`> 0`
- **Giải thích**:
  - Khoảng thời gian xe trượt trong trạng thái **crash landing**
- **Điều chỉnh**:
  - Tăng → Xe trượt dài 
  - Giảm → Xe trượt ngắn

---

### 21. **CrashSlideStarted** = false
- **Loại**: Bool
- **Ý nghĩa**:  
  Cờ trạng thái dùng để xác định crash slide đã bắt đầu hay chưa.
- **Phạm vi**: `True/False`
- **Giải thích**:
  - True → crash slide bắt đầu
  - False → crash slide chưa bắt đầu

---

### 22. **CrashStartLocation** = (0,0,0)
- **Loại**: FVector
- **Ý nghĩa**:  
  Vị trí bắt đầu của crash slide.
- **Phạm vi**: `FVector`

---

### 23. **TargetCrashSlideDistance** = 1000.0
- **Loại**: float
- **Ý nghĩa**:  
  Khoảng cách mục tiêu xe sẽ trượt khi xảy ra crash landing.
- **Phạm vi**: `float`
- **Điều chỉnh**:
  - Giá trị cao → crash landing mạnh, trượt xa
  - Giá trị thấp → crash landing nhẹ, trượt ngắn

---

### 24. **CrashSlideForce** = 2000.0
- **Loại**: float
- **Ý nghĩa**:  
  Độ lớn lực trượt được áp dụng cho xe sau khi xảy ra crash landing (tiếp đất mạnh), dùng để đẩy xe trượt về phía trước một đoạn sau khi chạm đất.
- **Điều chỉnh**:
  - Giá trị cao → crash landing mạnh, trượt xa, cảm giác nặng
  - Giá trị thấp → xe dừng nhanh, crash nhẹ

---

## BP_SimulatePhysicsCar - Anti Air / Anti Roll Settings

Nhóm này dùng để **chống xe bị xoay/lật quá đà khi bay** và **tự cân bằng khi sắp đáp**.

### Anti Roll In Air Group

#### 25. **MaxRollAngle** = 15.0 
- **Loại**: Float (degrees)
- **Ý nghĩa**: Góc nghiêng ngang tối đa cho phép khi airborne
- **Phạm vi**: 5 - 45 (Khuyến nghị)
- **Giải thích**:
  - Nếu Roll vượt quá `Max Roll Angle` → hệ thống sẽ tự động áp dụng anti-roll torque để kéo về
  - `5.0°` → xe được phép lật nghiêng tối đa 5 độ, vượt là bị kéo lại
- **Điều chỉnh**:
  - 5 - 45 cảm giác phù hợp nhất
- **Ví dụ**: 5.0° khoảng 1/36 của một full rotation, đủ để player cảm nhận nhưng không để lật nằm ngang

---

#### 26. **MaxPitchAngle** = 20.0
- **Loại**: Float (degrees)
- **Ý nghĩa**: Góc ngóc/chúi đầu tối đa cho phép khi airborne
- **Phạm vi**: 10.0 - 60.0 (Khuyến nghị)
- **Giải thích**:
  - Nếu Pitch vượt quá `Max Pitch Angle` → hệ thống kéo xe về ngồi thẳng
  - `10.0°` → xe được phép ngóc hoặc chúi tối đa 10 độ từ "level"
  - Giúp tránh xe bay quá dựng đứng rồi rơi tự do khó kiểm soát
- **Điều chỉnh**:
  - 10.0 - 60.0 cảm giác phù hợp nhất
- **Ghi chú**: Nên cao hơn Max Roll Angle vì pitch ít gây lật khi landing

---

#### 27. **AntiRollPowerInAir** = 8000.0
- **Loại**: Float (torque magnitude)
- **Ý nghĩa**: Độ mạnh của lực tự cân bằng roll khi xe bay
- **Giải thích**:
  - Khi Roll vượt `Max Roll Angle`, hệ thống apply lực ngược để kéo xe về thẳng

---

#### 28. **AntiPitchPowerInAir** = 10000.0
- **Loại**: Float (torque magnitude)
- **Ý nghĩa**: Độ mạnh của lực tự cân bằng pitch khi xe bay
- **Giải thích**:
  - Tương tự Anti Roll Power nhưng cho trục pitch (ngóc/chúi)
  - Giúp xe không bay dựng thẳng hay quá chúi dài hạn
- **Ghi chú**: Thường thấp hơn Anti Roll Power vì pitch ít critical

---

#### 29. **AntiAirBorneInRampMultiplier** = 0.05
- **Loại**: Float (multiplier)
- **Ý nghĩa**: Hệ số giảm/tăng tác dụng của anti-air khi xe mới rời ramp
- **Phạm vi**: 0.0 - 1.0
- **Giải thích**:
  - Ngay sau khi xe bị launch từ ramp, anti-roll & anti-pitch được giảm xuống
  - `Anti Air Power In Ramp = Anti Air Power × 0.1`
  - `= 5000 × 0.1 = 500` → lực anti-air chỉ còn 500 trong khoảng mới rời ramp
  - Tạo cảm giác "launch then stabilize"
- **Ghi chú**: Điều chỉnh "ramp personality" rất mạnh, nên test kỹ

---

### Height & Landing Recovery Group

### 30. **LandingTime** = 0.0
- **Loại**: Float (seconds)
- **Ý nghĩa**:  
  Thời gian đã trôi qua kể từ khi xe bắt đầu tiếp đất
- **Phạm vi**: `≥ 0`
- **Giải thích**:
  - Bắt đầu đếm khi xe chuyển sang trạng thái Landing
  - Dùng để điều khiển quá trình hồi phục sau khi tiếp đất

---

### 31. **LandingHeightThreshold** = 150.0
- **Loại**: Float (cm)
- **Ý nghĩa**:  
  Ngưỡng độ cao dùng để xác định thời điểm xe **được coi là đã chạm đất**
- **Phạm vi**: `> 0`
- **Giải thích**:
  - Khi khoảng cách từ xe đến mặt đường **≤ Landing Height Threshold**, hệ thống coi là landing
- **Điều chỉnh**:
  - Theo độ cao của xe

---

### 32. **HeightResetThreshold** = 550.0
- **Loại**: Float (centimeters)
- **Ý nghĩa**: Độ cao so với mặt đất mà hệ thống bắt đầu "chuẩn bị reset" tư thế trước landing
- **Phạm vi**:550 - 1000 cm
- **Giải thích**:
  - Khi xe cao hơn `Height Reset Threshold` → hệ thống "chờ"
  - Khi xe xuống thấp hơn → hệ thống bắt đầu check rotation và chuẩn bị correct
- **Ghi chú**: Nên điều chỉnh tùy theo jump cao độ

---

### 33. **LandingResetRollThreshold** = 45.0
- **Loại**: Float (degrees)
- **Ý nghĩa**: Ngưỡng roll khi chạm đất
- **Phạm vi**: 10 - 90 độ
- **Giải thích**:
  - Khi xe chạm đất mà Roll > 45° → hệ thống chuyển sang trạng thái crash landing

---

### 34. **LandingResetPitchThreshold** = 70.0
- **Loại**: Float (degrees)
- **Ý nghĩa**: Ngưỡng pitch khi chạm đất
- **Phạm vi**: 5 - 70 độ
- **Giải thích**:
  - Khi xe chạm đất mà Pitch > 70° (ngóc hoặc chúi) → hệ thống chuyển sang trạng thái crash landing

---

## Part 2: HÀM

### 35. **UpdateDynamicGravity**
- **Loại**: Function (Runtime Update)
- **Ý nghĩa**:  
  Cập nhật **gravity bổ sung theo độ cao của xe** trong quá trình bay 
- **Giải thích**:
  - Tính toán **gravity multiplier bổ sung** dựa trên độ cao
- **Kết quả**:
  - Giá trị `AdditionalGravityMultiplier` được cập nhật

---

### 36. **UpdateAirControl**
- **Loại**: Function
- **Ý nghĩa**:  
  Cho phép **điều khiển hướng và tư thế xe khi đang ở trên không**.
- **Giải thích**:
  - Tính mô‑men xoắn (torque) cho xe khi đang bay trên không dựa trên input người chơi.
- **Kết quả**:
  - Xe xoay/nghiêng khi đang ở trên không, giúp người chơi chỉnh hướng xe trong không trung.


**Phiên bản**: 2.0  
**Cập nhật**: 2026-02-03
**Áp dụng cho**: ME08 - Ramp & Airborne Mechanics

