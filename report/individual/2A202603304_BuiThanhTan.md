# Báo cáo Day 13 — Bùi Thanh Tân, 2A202603304


## 1. Kết luận trước
- Mã số: 2A202603304 · Task: `D13-2A202603304`
- 10 frame: s01f00, s01f21, s02f04, s02f22, s03f19, s03f56, s04f27, s04f45, s05f23, s05f46
- Hệ điều hành: Windows 11 · Trình duyệt: Google Chrome · CVAT: 2.81.0
- MSSV bạn ngồi cạnh: 2A202603177

Em vẽ tổng 53 cuboid trên 10 frame: s01f00 có 8, mỗi frame còn lại có 5. Các frame ít nhất (5 hộp). selfcheck cờ 1/53 cuboid (s04f27 #5, length < width),  ( giữ nguyên vì em sửa mãi k đạt yêu cầu). 

Cách dùng CVAT:
- **Đổi góc nhìn:** trong khung 3D, kéo chuột trái để xoay, kéo chuột phải để dời, lăn con lăn để phóng to/thu nhỏ; xoay tới khi nhìn thẳng xuống để có ảnh từ trên xuống; nháy đúp vào chỗ trống để về góc mặc định.
- **Vẽ và chỉnh cuboid:** chọn công cụ cuboid → label → **Shape** (không dùng Track), đưa hộp tới cụm điểm rồi nháy đúp để đặt; ở **Top** dời tâm, kéo cạnh đổi dài/rộng và xoay yaw; ở **Side/Front** chỉ dời lên xuống và kéo cạnh để đáy chạm đường, đặt chiều cao; `N` vẽ vật tiếp theo, `F`/`D` sang frame sau/trước, `Ctrl+S` để Save.
- **Hướng và ảnh camera:** bật **Appearance → Cuboid orientation**, căn vạch đỏ `+X` dọc thân xe và chỉ về đầu xe, đối chiếu với 7 ảnh camera (bánh răng ở góc khung ảnh để đổi camera); vì CVAT không chiếu cuboid lên ảnh, dùng `python scripts/lab.py preview` (trong `sync`) để xem cuboid trên ảnh camera ở `work/xem/`.

## 2. Đọc frame (Bước 2)

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f00 | Phía trước của xe nằm ở cạnh bên phải của  ảnh nhìn từ trên xuống, +x chỉ về bên trái xe, +y chỉ về phía sau xe |Đường dốc lên về phía -y | Car tầm 35m về phía -y|car |Car và Bus  | Về phía -x có các vật thể  có thể là Pedestrian hoặc Cyclist|
| s01f21 | Phía trước của xe nằm ở cạnh bên phải của ảnh nhìn từ trên xuống, +x chỉ về bên trái xe, +y chỉ về phía sau xe| Mặt đường: phẳng. Thấp hơn LiDAR khoảng 1,7–1,9 m |Car tầm 30m về phía -y |Car | Phía sau: 1 xe con cùng làn ~19 m. Trái: xe bus/tải làn bên cạnh (~3–5 m), 2 xe đỗ ngoài lề (~11–13 m). Phải: 1 xe đỗ sát lề (~6–7 m), vài vật nhỏ ~3 m | vật nhỏ sát phải ~3 m; cụm mép trước-phải ~34 m; vài vật nhỏ trước-trái ~20 m và trước-phải ~29 m; cụm thực vật/công trình dọc hai lề chưa tách rõ.|





## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | t2-a-car|Car. |Có |Có | Đã kiểm| |
| (b) xe buýt hoặc xe tải | t2-b-bus-truck | Bus|Có |Có |Đã kiểm | |
| (c) người đi bộ hoặc xe hai bánh | t2-c-ped-cyclist | Pedestrian|Có |Có |Đã kiểm | |



## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s02f12 #3 Car (10,1 m) và #4 Car (6,3 m), cùng làn với #2 (4,0 m), occluded = 1; thêm s05f06 #6. Nguồn: bảng A, MSSV 2A202603177 |Xe gần (#2) và lan can chắn tia laser, nên phần phía sau xe bị khuất không có điểm | Chỉ thấy một mặt hoặc một góc xe (hình chữ L, nửa thân). Phía sau vật che có vùng trống.|Vẫn gán theo kích thước đầy đủ của xe, ước lượng thêm từ ảnh camera; không thu hộp theo cụm điểm. Đánh occluded = 1. |
| 2. Thưa ở xa | s01f15 #7 Car (69,3 m) và #8 Car (48,9 m). Nguồn: bảng A, 2A202603177 | Khoảng cách giữa các vòng quét tăng theo khoảng cách, nên vật ở xa chỉ nhận được vài tia|Chỉ còn vài vạch ngắn, không rõ đường viền. Hộp #7 chìm khoảng 0,5 m (z_bottom −2,31), hộp #8 lơ lửng khoảng 0,75 m (z_bottom −1,02). |Kích thước lấy theo mẫu chuẩn của class (4,2–4,3 × 1,84 × 1,50 m), đáy căn theo mặt đường, hướng lấy theo làn. Nếu số điểm dưới ngưỡng của guideline thì không gán. |
| 3. Phản xạ thấp / ít điểm bất thường | s01f15 #1 Car: chỉ rộng 1,46 m dù chỉ cách 11,8 m; thêm s03f02 #5 (rộng 1,54 m ở 10,6 m). Nguồn: bảng A, 2A202603177 |Sơn tối màu và kính xe hấp thụ hoặc phản xạ lệch tia laser, nên chỉ có điểm ở biển số, đèn và mâm xe | Xe ở gần nhưng thưa điểm và khuyết mảng (thường là vùng kính và nóc), làm hộp bị hẹp dưới mức tối thiểu 1,70 m|Không thu hộp theo cụm điểm. Mở rộng về kích thước chuẩn trong bảng 4.3, đối chiếu ảnh camera. |
| 4. Điểm lạ |s05f06 #3 và #4 Car rộng 2,32–2,33 m (ở 3,9–7,2 m); s04f13 #5 Car cao 2,22 m. Nguồn: bảng A, 2A202603177  |Bề mặt phản xạ mạnh (biển số, gương, tấm phản quang, kính tòa nhà) gây loang điểm hoặc điểm ma. Cũng có thể điểm của lan can, bụi cây lẫn vào cụm xe. |Có dải điểm nhô ra hai bên thân xe hoặc một lớp điểm lơ lửng phía trên nóc, làm hộp to hơn thực tế |Loại các điểm lạ khi gán. Hộp chỉ ôm thân xe thật (Car rộng ≤ 2,1 m). Điểm ma không có vật tương ứng trong ảnh camera thì không gán nhãn. |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
|s02f04, 1, Car  |−1,037 |1,552 |−1,813 |3,04 |3,21 | 270,5|Chạm đường (ground_gap −0,02 m) |
|s05f46, 5, Car  |−0,163 |1,553 | −0,940| 37,40| 37,40| 13,8|Chạm đường: theo ngưỡng −1,7 m thì cao hơn 0,76 m, nhưng ground_gap chỉ 0,25 m (đất ở xa cao hơn) |
|s03f19, 2, Pedestrian  | −0,836| 1,623| −1,647| 9,12| 9,16|336,0 | Chạm đường (ground_gap 0,09 m)|

(a) Cuboid gần nhất là s02f04 #1 Car: dist_m = 3,04 m, r = 3,21 m. Cuboid xa nhất là s05f46 #5 Car: dist_m = 37,40 m, r = 37,40 m. Ở gần, r lớn hơn dist_m rõ hơn vì z (khoảng −1 m) chiếm tỉ lệ đáng kể so với khoảng cách ngang. Ở xa, ảnh hưởng của z không đáng kể nên hai giá trị gần như bằng nhau.

(b)

Người đi bộ s03f19 #2: l × w × h = 0,80 × 0,68 × 1,62 m. Cả ba chiều đều nằm trong khoảng người lớn của bảng 4.3 (0,4–0,9 × 0,5–0,9 × 1,50–1,95 m).
Xe con s04f45 #3: l × w × h = 5,77 × 2,06 × 1,90 m. Rộng và cao nằm trong khoảng SUV (1,80–2,10 và 1,65–2,10 m). Chiều dài 5,77 m vượt mức tối đa 5,3 m. Có hai khả năng: hộp kéo dài quá đuôi xe vì ôm thêm điểm nhiễu phía sau, hoặc đây là xe bán tải (dài 5,2–6,2 m), khi đó phải đổi class sang Truck.

(c) Theo quy ước của bảng A, hướng phía trước là −y (az = 0°).

Xe cùng chiều phía trước s05f23 #1 (14,2 m, az −1,4°): yaw = 270,4°, rất gần 270°.
Xe ngược chiều s02f22 #5: yaw = 84,6°, gần 90° nhưng lệch 5,4°. Hộp hơi xoay chéo so với làn đường, nên chỉnh lại khoảng 90°

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):


| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s02f04, 1, Car |3,0 |2345 |
| s05f23, 1, Car |14,2 |637 |
| s01f21, 3, Car | 27,4|212 |
| s04f45, 5, Car | 35,0|119 |

### Quy luật: 

Xa gấp đôi thì số điểm còn khoảng 1/3. Ví dụ xe ở 14,2 m có 637 điểm, xe ở 27,4 m còn 212 điểm. Số liệu của tôi khớp với đường cong trên slide ở dải 10–30 m (637 so với 718, 212 so với 214). Ở 3,0 m, xe có 2345 điểm, cao hơn mức 1968 của dải 0–10 m vì xe ở rất gần. Ở 35 m, xe có 119 điểm, cao hơn mức 72 vì xe này dài 5,2 m, lớn hơn xe con trung bình. Tính cả slide, từ 0–10 m đến 60–80 m (xa khoảng 14 lần) số điểm giảm khoảng 100 lần (1968 → 19).

### Đã sửa:

s04f27, idx 5, Cyclist: selfcheck báo length 0,56 m nằm ngoài khoảng, length nhỏ hơn width, và 82 điểm sát ngoài mép hộp. Tôi sửa kích thước thành khoảng 1,9 × 0,8 × 1,69 m (xe máy), xoay yaw dọc theo thân xe và nới hộp để ôm hết các điểm đó.
s04f45, idx 4, Car: ground_gap 0,34 m, hộp lơ lửng. Tôi hạ đáy hộp khoảng 0,3 m cho chạm mặt đường.

### Giữ nguyên:

s05f46 #5, s05f46 #4 và s02f22 #5: z đáy lệch quá 0,3 m so với −1,7 m, nhưng ground_gap chỉ từ −0,03 đến 0,25 m. Hộp vẫn chạm mặt đất cục bộ; phần lệch là do mặt đường không phẳng.
s03f56 #5, Bus: ground_gap trống vì dưới hộp không có điểm mặt đất (thân bus che mặt đường). Hộp đã ôm đủ 1019 điểm nên tôi giữ nguyên.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Model 3D dự đoán vị trí, kích thước và hướng của vật trong không gian thật, nên nhãn đo bằng mét mới cho nó cùng một chuẩn ở mọi khoảng cách: chiếc xe dài 4,5 m vẫn là 4,5 m dù ở 3 m hay 35 m. Nếu đo bằng pixel, kích thước co giãn theo khoảng cách và góc nhìn, model sẽ học sai và không dùng được cho việc tính khoảng cách, tránh va chạm. Vật thưa điểm (xa, bị che, phản xạ thấp) chỉ còn vài chục điểm, không đủ để thấy hết thân xe, nên nếu mỗi người tự ôm hộp theo cụm điểm thì nhãn sẽ lúc to lúc nhỏ, lơ lửng hoặc chìm. Cần quy tắc riêng (dùng kích thước chuẩn, đáy chạm đất, yaw theo làn, bỏ qua nếu dưới ngưỡng điểm) để model học được nhãn nhất quán thay vì học nhiễu.

(b) Dùng AI:
- Công cụ: Claude.
- Câu hỏi chính: phân biệt Pedestrian / Cyclist theo guideline, cách tìm hiện tượng phản xạ thấp; nhờ chạy `pack`, `check_submission.py`.
- Dùng kết quả thế nào: làm theo hướng dẫn quy trình; không đưa dữ liệu VF; tự vẽ cuboid, tự đọc bảng A, phiếu tự kiểm và tự viết các nhận xét, số liệu trong báo cáo.


