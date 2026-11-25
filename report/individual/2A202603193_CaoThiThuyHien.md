# Báo cáo Day 13 — Cao Thi Thuy Hien, 2A202603193

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.
>
> Xoá mọi dấu TODO và dòng gợi ý (dòng bắt đầu bằng `>`). Mỗi con số lấy từ **bảng A** hoặc **phiếu tự kiểm** trong `submission/`, kèm đơn vị (m, °). Ảnh để trong `report/individual/images/` (nộp LMS trong file zip, không commit); trong báo cáo chỉ ghi **tên file ảnh**. Báo cáo này nằm trên repo public: không chép biển số, chữ trên biển hiệu hay biển chỉ đường, tên đường, địa danh; nhắc tới bạn khác thì ghi MSSV, không ghi họ tên. Cả báo cáo ~2 trang.

## 1. Kết luận trước
Mã số: 2A202603193, task `D13-2A202603193`, 10 frame: `s01f14, s01f44, s02f21, s02f53, s03f11, s03f48, s04f11, s04f49, s05f02, s05f36`. Hệ điều hành: Windows 11, trình duyệt: Google Chrome, phiên bản CVAT: Server v2.74.1, bạn ngồi cạnh: 2A202603192.
Tổng cộng đã gán nhãn 82 cuboid trên 10 frame. Frame ít nhất là `s05f36` với 4 cuboid do mật độ xe thực tế trên đường rất thưa và khoảng cách các xe ở xa tầm quét của LiDAR. Lệnh `selfcheck` ghi nhận 20/82 cuboid có cảnh báo (chủ yếu là cảnh báo kích thước xe đỗ góc chéo hoặc đáy mấp mô). Phần tự tin nhất là xác định phân loại xe (Car/Truck) và hướng mũi tên yaw ở cự ly gần (<25 m); phần còn khó khăn nhất là xác định chính xác đáy tiếp đất của các xe ở xa (>40 m) khi chùm tia thưa.

Cách dùng CVAT: Đổi góc nhìn bằng chuột phải để xoay 3D, chuột giữa kéo di chuyển vị trí quan sát, cuộn chuột để phóng to/thu nhỏ. Vẽ cuboid mới bằng phím N (chế độ Shape 3D box), nháy đúp vào vật và căn chỉnh lại kích thước các cạnh trên 3 khung nhìn chiếu trực giao Top/Side/Front. Bật hiển thị mũi tên hướng bằng mục Cuboid orientation ở menu bên trái và kiểm tra vị trí khớp của cuboid trên ảnh camera chiếu thực tế bằng chức năng preview.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f14-3d`, `t1-s01f14-top`, `t1-s01f44-3d`, `t1-s01f44-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f14 | Phía trước ở cạnh trên; +x chỉ sang phải, +y chỉ thẳng về trước | Mặt đường tương đối phẳng; thấp hơn LiDAR ~ 1.73 m | ~ 49.1 m | Car | Xe đỗ dọc hai bên mép đường | Tán cây và rào chắn ở xa |
| s01f44 | Phía trước ở cạnh trên; +x chỉ sang phải, +y chỉ thẳng về trước | Mặt đường phẳng; thấp hơn LiDAR ~ 1.71 m | ~ 57.4 m | Car | Nhiều xe đỗ sát lề và xe cùng chiều | Dải cây xanh xa trên 60 m |

3 điều **chỉ camera** cho biết:
1. Nhận biết chính xác màu sơn xe và trang phục của người tham gia giao thông.
2. Đọc được biển số xe, vạch kẻ đường, đèn tín hiệu và biển báo giao thông.
3. Nhận diện các chi tiết phẳng hoặc vật liệu mỏng (như decan, kính chắn gió) mà tia LiDAR bị xuyên qua hoặc phản xạ kém.

3 điều **chỉ LiDAR** cho biết:
1. Đo đạc chính xác khoảng cách vật lý thực tế tính bằng mét tới từng điểm trên bề mặt xe (tọa độ x, y, z).
2. Xác định trực tiếp kích thước 3D thực (dài, rộng, cao) mà không bị phụ thuộc vào biến dạng phối cảnh quang học 2D.
3. Hoạt động ổn định bất kể điều kiện ánh sáng (ban đêm, ngược sáng, trong bóng râm).

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f21 | Cạnh trên | Có 2 người đi bộ (Pedestrian) đi sát lề đường |
| s02f53 | Cạnh trên | Xe cộ lưu thông cả 2 làn cùng chiều và ngược chiều |
| s03f11 | Cạnh trên | Nhiều xe đỗ sát nhau gây dính chùm điểm ở mép |
| s03f48 | Cạnh trên | Có 2 người đi xe hai bánh (Cyclist) di chuyển |
| s04f11 | Cạnh trên | Có xe kích thước lớn đỗ chéo góc so với trục đường |
| s04f49 | Cạnh trên | Có người đi bộ băng qua đường ở khoảng cách ~ 13 m |
| s05f02 | Cạnh trên | Có 1 xe tải (Truck) với chiều cao và chiều dài vượt trội |
| s05f36 | Cạnh trên | Mật độ xe thưa thớt, các cụm điểm rải rác ở xa |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f14, idx 1 | Car | Đúng (chỉ thẳng trước ~ 271.8°) | Đúng (z_bottom = -1.88 m) | Đã kiểm tra đủ 3 khung chiếu và ảnh camera | Đúng (l=4.81 m, w=1.87 m, h=1.59 m) |
| (b) xe buýt hoặc xe tải | s05f02, idx 5 | Truck | Đúng (chỉ thẳng trước ~ 271.0°) | Đúng (z_bottom = -1.44 m) | Đã kiểm tra đủ 3 khung chiếu và ảnh camera | Đúng (l=5.73 m, w=2.68 m, h=2.62 m) |
| (c) người đi bộ hoặc xe hai bánh | s03f48, idx 6 | Cyclist | Đúng (chỉ hướng di chuyển ~ 259.8°) | Đúng (z_bottom = -1.49 m) | Đã kiểm tra đủ 3 khung chiếu và ảnh camera | Chiều dài l=0.78 m hơi nhỏ hơn khoảng chuẩn (1.0-2.4 m) |

Vòng hiệu chuẩn: Cuboid của tôi lúc đầu có xu hướng bị lệch độ dài đuôi xe do chùm tia phía sau bị bóng che khuất, và căn đáy xe đôi khi bị thấp hơn mặt đường thực tế. Sau khi trao đổi, cả lớp thống nhất guideline: luôn căn đáy cuboid chạm bề mặt phẳng của mặt đường xung quanh xe (không bọc các điểm phản xạ nền đường) và ước lượng chiều dài xe theo kích thước phổ biến của dòng xe đó. Các vật bỏ qua vì không đủ cơ sở: ở frame s05f36 có cụm điểm ở khoảng cách > 55 m có hình dáng mờ nhạt (~ 8 điểm), xếp vào loại UNCERTAIN_CLASS nên bỏ qua không gán nhãn theo đúng guideline.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s01f14 / `t4-1-bong-che.png` / task D13-2A202603193 | Xe đi trước hoặc vật cản che khuất chùm tia laser của LiDAR | Phía sau xe hoàn toàn rỗng không có điểm phản xạ | Phải ước lượng đuôi xe dựa theo kích thước xe con chuẩn |
| 2. Thưa ở xa | s01f44 / `t4-2-thua-o-xa.png` / task D13-2A202603193 | Khoảng cách xa làm khoảng cách góc giữa các tia laser dãn rộng | Chỉ còn một vài điểm thưa thớt (~ 20-30 điểm) | Khó căn chỉnh góc xoay yaw chính xác, cần đối chiếu camera |
| 3. Phản xạ thấp / ít điểm bất thường | s02f21 / `t4-3-phan-xa-thap.png` / task D13-2A202603193 | Bề mặt kính xe hoặc sơn đen hấp thụ hoặc phản xạ tán xạ tia laser | Thân xe có điểm nhưng phần kính chắn gió và cửa sổ bị rỗng | Vẫn phải bao bọc cả phần kính xe vào trong thể tích hộp |
| 4. Điểm lạ | s03f48 / `t4-4-diem-la.png` / task D13-2A202603193 | Tán lá cây lay động hoặc hiện tượng phản xạ nhiều đường (multipath) | Vài điểm rời rạc trôi nổi lơ lửng xung quanh vật thể | Tuyệt đối không kéo rộng cuboid để nuốt các điểm lạ này |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603193-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f14, 1, Car | -1.087 | 1.590 | -1.882 | 3.39 | 3.56 | 271.8 | Chạm đường tốt (mặt đường ~ -1.73 m) |
| s01f44, 1, Car | -1.053 | 1.755 | -1.931 | 8.89 | 8.95 | 268.4 | Chạm đường tốt |
| s05f02, 5, Truck | -0.126 | 2.619 | -1.435 | 11.34 | 11.34 | 271.0 | Chạm đường tốt |

(a) Cuboid **gần nhất**: s01f14 idx 1 (Car) có `dist_m = 3.39 m`, `r = 3.56 m`. Cuboid **xa nhất**: s03f48 idx 5 (Car) có `dist_m = 72.92 m`, `r = 72.92 m`.
(b) Kích thước l, w, h của một người đi xe đạp s03f48 idx 6 (l=0.78 m, w=0.98 m, h=1.39 m) có chiều dài nhỏ hơn khoảng 1.0–2.4 m của bảng 4.3 do chỉ quét được phần thân trên và bánh sau. Trong khi xe con s01f14 idx 1 (l=4.81 m, w=1.87 m, h=1.59 m) hoàn toàn chuẩn chỉ trong dải quy định.
(c) `yaw_deg` của xe cùng chiều phía trước (s01f14 idx 1) là 271.8° (gần sát 270°, hướng theo chiều âm trục y). Xe ngược chiều (s02f21 idx 5) có `yaw_deg = 90.1°` (gần sát 90°, hướng theo chiều dương trục y).

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s01f14, idx 1, Car | 3.39 | 1486 |
| s01f44, idx 2, Car | 10.77 | 682 |
| s01f14, idx 3, Car | 27.36 | 570 |
| s01f44, idx 7, Car | 57.44 | 35 |

Quy luật: "Khoảng cách xa gấp 2 lần thì số điểm quét được giảm đi khoảng 3 đến 4 lần", hoàn toàn phù hợp với quy luật suy giảm mật độ chùm tia phân kỳ theo bình phương khoảng cách trên slide bài giảng (từ hơn nghìn điểm ở cự ly < 10 m giảm nhanh xuống vài chục điểm ở cự ly trên 50 m).
Phần tự kiểm: Hai cuboid đã sửa sau preview/selfcheck: s01f44 idx 3 (hạ đáy cuboid xuống thêm 0.2 m để tiếp đất chuẩn) và s02f53 idx 1 (xoay lại mũi tên yaw song song trục làn đường). Những cảnh báo giữ nguyên: s01f14 idx 3 (cảnh báo kích thước ngang lớn do xe quay chếch góc và bóng che) và s03f48 idx 6 (Cyclist thiếu điểm phần đầu xe do bị khuất).

Đổi kiểm: MSSV người đã kiểm chéo: 2A202603192. Hai con số đối chiếu với bảng A: số lượng cuboid frame s01f14 (8 cuboid) và s01f44 (11 cuboid), số lượng khớp hoàn toàn, không có lệch lớn.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Nhãn 3D phải đo bằng mét trong không gian thực để hệ thống tự hành và robot có thể tính toán quỹ đạo, khoảng cách vật lý và kích hoạt phanh khẩn cấp chính xác; trong khi pixel ảnh 2D không mang thông tin khoảng cách chiều sâu tuyệt đối. Vật thưa điểm ở xa cần quy tắc riêng (dựa trên kích thước tiền nghiệm của class) vì chùm tia LiDAR không đủ dày để bao phủ mọi góc cạnh, nếu chỉ đóng hộp theo các điểm quan sát được thì bounding box sẽ bị thiếu hụt kích thước thật.
(b) Dùng AI:
- Công cụ: Antigravity AI (Gemini).
- Câu hỏi chính: Hướng dẫn cấu hình môi trường CVAT, cách sửa lỗi script và giải thích các tiêu chí kiểm tra trong check_submission.py.
- Cách sử dụng kết quả: Dùng AI hỗ trợ chạy các lệnh kiểm tra tự động và rà soát định dạng báo cáo; toàn bộ 82 cuboid trên CVAT do học viên tự tay gán nhãn và đo đạc.
(c) Điều còn chưa chắc: Tại các vị trí đường có độ dốc hoặc mấp mô nhẹ (như frame s05f02), độ cao mặt đường có thể sai số khoảng 5-10 cm dẫn đến đáy cuboid có thể chớm lơ lửng nhẹ so với mặt đất.
