# Báo cáo Day 13 — Bùi Quang Huy, 2A202603201

## 1. Kết luận trước

- Mã số: 2A202603201 · task `D13-2A202603201`
- 10 frame: s01f23, s01f55, s02f20, s02f54, s03f04, s03f22, s04f00, s04f53, s05f19, s05f37
- Hệ điều hành: Windows 11 · trình duyệt: Chrome · CVAT: 2.82.1 (server, CVAT cài từ Ngày 12; cvat-sdk 2.83.0)
- MSSV ngồi cạnh: Không có.

**Tổng kết annotation:** Tổng số cuboid: 79. Frame ít hộp nhất: `s05f37`, có 5 hộp. Lý do: Số lượng đối tượng xuất hiện trong vùng quan sát ít hơn các frame khác. Đây là nhận xét về số lượng đối tượng, không đồng nghĩa với việc mật độ điểm LiDAR thấp.

**Self-check:** Số cờ self-check: 5/79. Phần tự tin nhất: Hướng và vị trí tổng thể của cuboid, nhờ có thể đối chiếu point cloud từ nhiều góc nhìn. Phần còn yếu nhất: Kích thước và vị trí của các vật thể ở xa do mật độ điểm LiDAR thấp, khó xác định chính xác biên của vật thể.

**Thao tác CVAT:**

- Thay đổi góc nhìn: Sử dụng chuột để xoay point cloud, nhìn từ trên xuống (Top view), thay đổi góc quan sát và di chuyển khung nhìn (Pan) để kiểm tra đối tượng.
- Vẽ và chỉnh sửa hộp: Chọn công cụ Shape/Cuboid để tạo hộp 3D, nháy đúp để hoàn tất hoặc chỉnh sửa đối tượng. Kiểm tra kích thước, vị trí và hướng hộp ở ba góc nhìn Top, Side và Front.
- Kiểm tra hướng: Bật hiển thị mũi tên hướng (Direction) trong chế độ hiển thị cuboid. Sử dụng Camera Preview để kiểm tra cuboid khi chiếu lên ảnh camera, đối chiếu với hình dạng thực tế của phương tiện.

## 2. Đọc frame (Bước 2)

Hai frame đọc kỹ. Ảnh: `t1-s01f23-3d`, `t1-s01f23-top`, `t1-s01f55-3d`, `t1-s01f55-top`.

| Frame  | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m)    | Vật xa nhất đọc được (~ m) | Loại vật thấy                    | Vật phía sau / hai bên | Cụm chưa đọc được |
| ------ | ---------------------------------------------------------------------- | -------------------------------------------- | -------------------------- | -------------------------------- | ---------------------- | ----------------- |
| s01f23 | +X: bên trái xe; +Y: hướng về phía sau xe (phía trước xe là −Y)        | Tương đối phẳng; thấp hơn LiDAR khoảng 1,7 m | 20m                        | xe ô tô, xe bus, người đi xe máy |                        | Không có          |
| s01f55 | +X: bên trái xe; +Y: hướng về phía sau xe (phía trước xe là −Y)        | Tương đối phẳng; thấp hơn LiDAR khoảng 1,7 m | 16m                        | xe ô tô                          |                        | Không có          |

Mặt đường: cần căn cứ vào các điểm mặt đường và tọa độ cảm biến để xác định. Khoảng cách quan sát: khả năng nhận diện phụ thuộc vào mật độ điểm và kích thước vật thể.

**Chỉ camera cho biết:** (1) Màu sắc phương tiện. (2) Loại phương tiện và đặc điểm hình dáng. (3) Đèn tín hiệu, đèn phanh và các chi tiết trực quan.

**Chỉ LiDAR cho biết:** (1) Khoảng cách thực tế đến vật thể. (2) Kích thước và vị trí vật thể trong không gian 3D. (3) Hình dạng không gian và mật độ phân bố điểm.

Tám frame lướt:

| Frame                                                          | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có)     |
| -------------------------------------------------------------- | ------------------------------------------------ | -------------------- |
| s02f20, s02f54, s03f04, s03f22, s04f00, s04f53, s05f19, s05f37 |                                                  | Không có gì đặc biệt |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật                              | Frame, idx  | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
| -------------------------------- | ----------- | ----- | ----------------------- | --------------- | ----------------------------- | -------------------------- |
| (a) xe con gần                   | s02f20, #1  | Car   | Có                      | Có              | Chuẩn                         | OK                         |
| (b) xe buýt hoặc xe tải          | s03f04, #10 |       | Có                      | Có              | Chuẩn                         | OK                         |
| (c) người đi bộ hoặc xe hai bánh | s03f22, #8  |       | Có                      | Có              | Chuẩn                         | OK                         |

Vòng hiệu chuẩn: Bỏ qua theo hướng dẫn của coach.

Đối tượng không đủ cơ sở nhận diện (bỏ qua): Không có.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng                           | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
| ------------------------------------ | ----------------------- | ------------------------ | ------------------------ | ------------------------------- |
| 1. Bóng che                          | s01f23 / `t4-1-bong-che` / task của tôi |                          |                          |                                 |
| 2. Thưa ở xa                         | s01f55 / `t4-2-thua-o-xa` / task của tôi |                          |                          |                                 |
| 3. Phản xạ thấp / ít điểm bất thường | s01f55 / `t4-3-phan-xa-thap` / task của tôi |                          |                          |                                 |
| 4. Điểm lạ                           | s01f55 / `t4-4-diem-la` / task của tôi |                          |                          |                                 |

## 5. Số đo cuboid của tôi (Bước 5)

Lấy từ `submission/D13-2A202603201-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z      | h     | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
| -------------------------- | ------ | ----- | --------------- | ------ | --------------- | ------- | -------------------------------------- |
| (s03f04, 1, Car)           | -1.026 | 1.511 | -1.782          | 3.81   | 3.95            | 269.0°  | Chạm đường                             |
| (s01f23, 4, Bus)           | -0.076 | 3.346 | -1.749          | 13.67  | 13.67           | 269.7°  | Chạm đường                             |
| (s03f22, 8, Pedestrian)    | -0.561 | 2.004 | -1.564          | 14.98  | 14.99           | 275.9°  | Chạm đường                             |

**Gần nhất / xa nhất:**

| Đối tượng                  | dist_m |     r |
| -------------------------- | -----: | ----: |
| Gần nhất: (s03f04, 1, Car) |   3.81 |  3.95 |
| Xa nhất: (s05f37, 5, Car)  |  48.67 | 48.68 |

**So sánh kích thước với bảng 4.3:**

- Người đi bộ hoặc xe hai bánh: Cuboid người đi bộ (s03f22, 8, Pedestrian) có kích thước dài 0,830 m, rộng 0,700 m, cao 2,004 m. Chiều dài và chiều rộng nằm trong khoảng tham chiếu người lớn; chiều cao vượt giới hạn trên 1,95 m khoảng 0,054 m. Sai lệch có thể do kích thước thực tế và cách đặt cuboid theo điểm mây.
- Xe con: Cuboid (s04f00, 1, Car) có kích thước dài 5,104 m, rộng 2,481 m, cao 1,815 m. Chiều rộng vượt khoảng tham chiếu SUV/minivan/van chở người (1,80–2,10 m), có thể do cách đặt cuboid hoặc hình dạng thực tế của xe.

Không nên ép kích thước của mọi đối tượng vào một giá trị cố định nếu hình dạng thực tế khác nhau.

**Kiểm tra yaw:**

- Xe cùng chiều: (s03f04, 1), yaw = 269,0°.
- Xe ngược chiều: (s02f20, 1), yaw = 89,7°.

Đối chiếu với quy ước của dataset: xe cùng chiều có yaw gần 270°, xe ngược chiều có yaw gần 90°.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
| -------------------------- | ------ | --------------------- |
| (s03f04, 1, Car)           | 3.81   | 2.193                 |
| (s02f20, 6, Car)           | 16.68  | 733                   |
| (s02f54, 6, Car)           | 32.95  | 144                   |
| (s05f37, 5, Car)           | 48.67  | 49                    |

Nhận xét: Khi khoảng cách đến vật thể tăng, số lượng điểm LiDAR thu được thường giảm. Tuy nhiên, mức giảm còn phụ thuộc vào kích thước, góc phản xạ và vị trí của vật thể.

Trong 4 cuboid được chọn, khoảng cách tăng từ 3,81 m lên 48,67 m, tức khoảng 12,77 lần; số điểm giảm từ 2.193 xuống 49, chỉ còn khoảng 2,23%. Có thể nhận xét rằng khi khoảng cách tăng khoảng 12,77 lần, số điểm trong mẫu xa nhất chỉ còn khoảng 1/44,8 mẫu gần nhất. Đây là kết quả của các mẫu được chọn, không phải tỷ lệ cố định cho mọi vật thể.

**Hai cuboid đã sửa lỗi xoay 90°:**

- Cuboid 1: Frame s01f23, index 8. Điều chỉnh yaw để trục đỏ không còn nằm ngang thân xe mà phù hợp với hướng thực tế.
- Cuboid 2: Frame s01f55, index . Chỉnh lại hướng xoay của hộp và kiểm tra bằng Top view, Camera Preview.

**Năm cảnh báo còn lại:**

| Cuboid   | Cảnh báo        | Giữ nguyên hay đã sửa, lý do                                   |
| -------- | --------------- | -------------------------------------------------------------- |
| s03f04#6 | Rộng/cao        | Kiểm tra lại kích thước và đối chiếu hình dạng thực tế         |
| s03f22#4 | Đáy chìm 0,82 m | Ưu tiên kiểm tra và sửa vị trí đáy nếu không phù hợp mặt đường |
| s03f22#7 | Cao 2,41 m      | Kiểm tra lại chiều cao theo point cloud và loại phương tiện    |
| s04f00#1 | Rộng 2,48 m     | Kiểm tra lại chiều rộng và phân loại phương tiện               |
| s05f37#4 | Cao 1,20 m      | Kiểm tra lại chiều cao, tránh ép hộp theo kích thước mặc định  |

Nguyên tắc: Không sửa chỉ để xóa cảnh báo. . Giữ nguyên nếu số đo phù hợp với vật thể thực tế và có đủ bằng chứng; sửa khi kiểm tra point cloud xác nhận hộp bị sai.

**Đổi kiểm:** Không có ai đối chiếu.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

**(a)** Nhãn 3D được đo bằng mét thay vì pixel vì chúng biểu diễn vị trí và kích thước thực tế của vật thể trong không gian ba chiều, không phụ thuộc vào độ phân giải hay góc nhìn của camera. Vật thể thưa điểm cần quy tắc riêng vì LiDAR chỉ thu được một số ít điểm phản hồi, khiến việc xác định ranh giới và kích thước không chính xác như vật thể gần. Do đó cần tránh suy diễn và chỉ gán nhãn khi có đủ cơ sở.

**(b) Dùng AI:**

- Công cụ: Claude Code (trợ lý lập trình trong VS Code), không gửi ảnh, dữ liệu VF hay ảnh chụp màn hình cho AI.
- Câu hỏi chính: cài môi trường (`.venv`, `lab.ini`), chạy lệnh `task`/`sync`/`check_submission`, chuyển và đổi tên ảnh theo đúng tên file; giải thích màu trục cuboid trong CVAT (đỏ +X dài, xanh lá +Y rộng, xanh dương +Z cao) và vì sao `selfcheck` báo `length < width` (trục đỏ nằm ngang thân xe).
- Dùng kết quả thế nào: tự sửa lại các cuboid trên CVAT (xoay để trục đỏ dọc thân xe, chỉ về đầu xe), chạy lại `sync` để kiểm. AI có đếm số cuboid và số cảnh báo `selfcheck` theo frame từ log và phiếu tự kiểm để báo lại, và chép các câu trả lời do tôi tự viết vào mẫu báo cáo; AI không vẽ cuboid, không đọc bảng A hộ, không viết nhận xét hay điền số trong báo cáo.

**(c)** Tôi vẫn cần cải thiện khả năng xác định kích thước và vị trí đáy của các vật thể ở xa, đặc biệt khi mật độ điểm thấp hoặc vật thể bị che khuất. Ngoài ra, cần kiểm tra kỹ hướng yaw và đối chiếu giữa các góc nhìn để hạn chế lỗi xoay hộp 90°.
