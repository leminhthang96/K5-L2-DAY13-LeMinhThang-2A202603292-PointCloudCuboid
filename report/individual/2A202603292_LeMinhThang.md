# Báo cáo Day 13 — Lê Minh Thắng, 2A202603292

## 1. Kết luận trước

- MSSV: 2A202603292
- Task: D13-2A202603292
- Hệ điều hành: Windows 11
- Trình duyệt: Cốc Cốc
- CVAT: 2.81.0
- Số frame: 10
- Tổng số cuboid: 50

| Frame | Số cuboid |
|---|---:|
| s01f35 | 5 |
| s01f56 | 6 |
| s02f27 | 6 |
| s02f45 | 5 |
| s03f24 | 2 |
| s03f41 | 3 |
| s04f24 | 7 |
| s04f53 | 6 |
| s05f21 | 5 |
| s05f41 | 5 |
| Tổng | 50 |

Frame s03f24 có ít nhất 2 cuboid do khó xác định thêm
đối tượng từ point cloud.

Selfcheck cảnh báo 32/50 cuboid (64%).
Đã đạt ngưỡng tổng số cuboid theo rubric nhưng một số
frame vẫn chưa đạt số lượng yêu cầu riêng. Cần tiếp tục
cải thiện độ cao đáy hộp và độ chính xác kích thước.

Thao tác CVAT:

- Xoay, di chuyển và phóng to point cloud bằng chuột.
- Dùng Shape để tạo cuboid và chỉnh qua Top/Side/Front.
- Bật Cuboid orientation để kiểm tra hướng đầu xe.
- Đối chiếu point cloud với ảnh camera.

## 2. Đọc frame

| Frame | Hướng | Mặt đường | Đối tượng | Cụm chưa xác định |
|---|---|---|---|---|
| s01f35 | Trước -Y, trái +X, sau +Y | Tương đối phẳng, khoảng -1.7 m | Xe con phía trước và sau | Cụm điểm dài bên đường |
| s01f56 | Trước -Y, trái +X, sau +Y | Tương đối phẳng, khoảng -1.7 m | Nhiều xe con | Các cụm điểm xa và bị che |

Ảnh minh chứng:

- t1-s01f35-3d.png
- t1-s01f35-top.png
- t1-s01f56-3d.png
- t1-s01f56-top.png

Camera giúp nhận biết màu sắc, hình dáng phương tiện
và các chi tiết đầu xe, đuôi xe.

LiDAR giúp xác định khoảng cách, tọa độ 3D và kích
thước thực tế của vật thể.

| Frame | Hướng trước | Quan sát |
|---|---|---|
| s02f27 | -Y | Có phương tiện hai chiều |
| s02f45 | -Y | Điểm thưa ở xa |
| s03f24 | -Y | Ít đối tượng rõ |
| s03f41 | -Y | Xe phía trước và sau |
| s04f24 | -Y | Nhiều xe tại giao lộ |
| s04f53 | -Y | Có người đi xe máy |
| s05f21 | -Y | Xe ở nhiều khoảng cách |
| s05f41 | -Y | Có xe tải và xe buýt |

## 3. Ba cuboid mẫu

| Frame, idx | Class | L × W × H (m) | Ảnh |
|---|---|---|---|
| s01f35, 1 | Car | 4.5 × 1.8 × 1.5 | t2-a-car.png |
| s05f41, 5 | Bus | 12 × 2.5 × 3.2 | t2-b-bus-truck.png |
| s04f53, 6 | Cyclist | 2 × 0.8 × 1.7 | t2-c-ped-cyclist.png |

Đã sử dụng các góc nhìn Top, Side, Front và camera
để căn chỉnh kích thước, vị trí và hướng cuboid.

Các cuboid được tạo dưới dạng Shape, không dùng Track.
Cyclist bao gồm cả người và phương tiện hai bánh.

Một số vật có điểm thưa hoặc không rõ loại được bỏ qua
do LOW_VISIBILITY hoặc UNCERTAIN_CLASS.

Chưa có kết quả vòng hiệu chuẩn chung được xác nhận.

## 4. Bốn hiện tượng chất lượng

| Hiện tượng | Frame | Giả thuyết và ảnh hưởng | Ảnh |
|---|---|---|---|
| Bóng che | s01f56 | Xe gần có thể che xe xa, làm thiếu điểm | t4-1-bong-che.png |
| Thưa ở xa | s02f45 | Khoảng cách tăng làm giảm mật độ điểm | t4-2-thua-o-xa.png |
| Ít điểm bất thường | s04f53 | Có thể do phản xạ thấp, khoảng cách hoặc hộp chưa khớp | t4-3-phan-xa-thap.png |
| Điểm lạ | s01f35 | Cụm điểm nghi nhiễu hoặc vật chưa xác định | t4-4-diem-la.png |

Đối với vật bị che một phần, sử dụng thuộc tính Occluded
khi xác nhận được hiện tượng che khuất.

Không gán nhãn cho các điểm lạ hoặc vật không đủ cơ sở
xác định.

## 5. Số liệu từ cuboid

| Frame, idx | Class | z (m) | h (m) | z đáy (m) | dist (m) | r (m) | Yaw |
|---|---|---:|---:|---:|---:|---:|---:|
| s01f35, 1 | Car | -1.022 | 1.5 | -1.772 | 8.97 | 9.03 | 273.8° |
| s05f41, 4 | Truck | -0.305 | 2.8 | -1.705 | 20.56 | 20.56 | 273.8° |
| s05f41, 5 | Bus | -0.018 | 3.2 | -1.618 | 22.54 | 22.54 | 269.3° |

Ba cuboid trên có đáy tương đối sát mặt đường.

- Cuboid gần đã ghi nhận: s05f41, idx 2,
  dist = 3.69 m, r ≈ 3.71 m.
- Cuboid xa đã ghi nhận: s04f24, idx 4,
  dist = 44.01 m, r ≈ 44.01 m.
- Xe cùng chiều: yaw 273.8°, gần 270°.
- Xe ngược chiều: yaw 89.6°, gần 90°.

Kích thước Car là 4.5 × 1.8 × 1.5 m.
Kích thước Cyclist là 2 × 0.8 × 1.7 m.
Các kích thước được sử dụng làm giá trị tham khảo
và căn chỉnh theo point cloud.

### Mật độ điểm theo khoảng cách

| Frame, idx | Khoảng cách (m) | Số điểm |
|---|---:|---:|
| s01f35, 1 | 8.97 | 1819 |
| s01f35, 2 | 10.51 | 728 |
| s02f27, 2 | 20.92 | 268 |
| s02f27, 3 | 33.82 | 110 |

Khoảng cách tăng khoảng 3.77 lần thì số điểm giảm
từ 1819 xuống 110 điểm, còn khoảng 6%.
Kết quả phù hợp với xu hướng giảm mật độ điểm
theo khoảng cách trong bài giảng.

### Kết quả tự kiểm

- Đã điều chỉnh độ cao Truck và Bus tại s05f41.
- Đã loại một cuboid vượt phạm vi 50 m.
- Đã bổ sung 3 cuboid tại s01f35.
- Tổng số cuboid sau chỉnh sửa: 50.
- Selfcheck: 32/50 cuboid còn cảnh báo.
- Checker: 0 lỗi, 4 cảnh báo trước khi commit.

Các cảnh báo còn lại chủ yếu liên quan đến độ cao
đáy hộp và số điểm nằm gần ngoài mép cuboid.

Chưa có kết quả kiểm chéo với học viên khác được xác nhận.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D sử dụng mét để mô hình học kích thước và
vị trí thực tế của vật trong không gian.

Vật ở xa cần quy tắc riêng vì mật độ điểm thấp khiến
việc nhận diện và xác định kích thước khó hơn.

Sử dụng ChatGPT:

- Hướng dẫn thao tác CVAT và giải thích guideline.
- Hỗ trợ đọc bảng A và phiếu tự kiểm.
- Hỗ trợ tổng hợp báo cáo từ kết quả thực tế.

Các điểm còn hạn chế: độ cao đáy cuboid, kích thước
phương tiện và nhận diện các vật có ít điểm LiDAR.