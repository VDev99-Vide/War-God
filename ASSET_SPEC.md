# Asset 2D — Zeus v2 (mẫu ornate) — spec cắt part và prompt cho AGY

Nguồn thiết kế: `../zeus_catalogue_v2.png` (catalogue 6 ô) và ảnh tư thế nghỉ
`zeus_v2_reference_pose.png` trong thư mục này. Thiết kế này KHÁC bản cartoon
của pack ZEUS_2D_ASSET_V1: không vương miện, áo choàng viền lông trắng, giáp
vàng chạm khắc gắn đá xanh, áo dài trắng viền hoa văn Greek-key, tỉ lệ cao oai.

## Trạng thái bộ này

Đã có: ảnh tư thế nghỉ toàn thân (góc 3/4 nhẹ hướng sang phải) + file spec này.
Chưa có: các part PNG đã cắt có alpha, manifest đầy đủ số liệu, preview HTML,
rig và animation. Các part phải được cắt từ atlas theo đúng quy trình của pack
v1 rồi mới điền số liệu vào manifest. Không tự điền số liệu pixel khi chưa cắt.

## Quy ước canvas (giống pack v1)

- Canvas ghép mẫu: 1024 × 1536. Gốc tọa độ ở góc trên trái, X sang phải,
  Y xuống dưới. Góc xoay tính bằng độ, dương theo chiều kim đồng hồ.
- Điểm gốc đứng trên mặt đất gợi ý: (512, 1425).
- `near` là bên gần camera, tức tay cầm tia sét ở phía trái màn hình trong
  pose này. `far` là bên còn lại. Khi đổi hướng, lật toàn bộ root, không lật
  riêng từng part.
- Mỗi part dùng pivot riêng trong file đó, không căn tất cả theo tâm file.
- Thứ tự biến đổi: translate đến vị trí, rotate, scale, rồi vẽ texture tại
  trừ pivot. Vẽ theo `draw_order` tăng dần. Tia sét mặc định ẩn, chỉ hiện
  khi thi triển.

## Danh sách part (15 part, đặt tên theo mẫu v1)

| id | Nội dung | Ghi chú cắt |
|---|---|---|
| head | Mặt, tóc trước, râu | Râu có thể để chung với đầu như v1, hoặc tách riêng nếu cần nhép miệng |
| hair_back | Tóc dài trắng phía sau | Mảnh riêng để làm chuyển động phụ |
| torso | Thân, cổ, ngực trần | Đã tính cả phần vai nối áo choàng |
| cape_fur | Áo choàng viền lông trắng | Mesh riêng, không khóa vào thân |
| belt | Thắt lưng vàng bản rộng chạm khắc, đá xanh | Tách riêng khỏi áo dài, khác với v1 gộp váy và thắt lưng |
| robe | Áo dài trắng viền Greek-key vàng | Một mảnh như váy của v1 |
| upper_arm_near | Bắp tay bên gần | Kèm bao tay vàng phần trên nếu liền mạch |
| upper_arm_far | Bắp tay bên xa | Như trên, phía đối diện |
| forearm_hand_near | Cẳng tay, bao tay, nắm tay bên gần | Bàn tay liền cẳng tay như v1 |
| forearm_hand_far | Cẳng tay, bao tay, nắm tay bên xa | Như trên, phía đối diện |
| thigh_near | Đùi bên gần | |
| thigh_far | Đùi bên xa | |
| shin_foot_near | Cẳng chân và sandal bên gần | Bàn chân liền cẳng chân như v1 |
| shin_foot_far | Cẳng chân và sandal bên xa | Như trên, phía đối diện |
| lightning | Tia sét rời | Sprite riêng, gắn socket tay cầm sét |

Thứ tự vẽ gợi ý từ sau ra trước: hair_back, cape_fur, các part far (tay, chân),
torso, robe, belt, các part near (chân, tay), head, lightning ở trên cùng khi hiện.

## Prompt cho AGY (dựa theo AGY_PROMPT_VI.md của pack v1)

Dùng bộ asset này để tạo nhân vật 2D có xương và animation cho prototype game
mobile theo lượt 1v1. Đây là công việc rig/animation, không dựng model 3D,
không chuyển PNG thành GLB. Trước khi làm, đọc file spec này, catalogue v2 và
ảnh tư thế nghỉ. Nếu chưa có dự án hoặc chưa xác định engine, báo rõ và hỏi
lựa chọn engine trước khi tạo định dạng rig phụ thuộc engine.

- Giữ nguyên ảnh nguồn, làm bản làm việc riêng nếu phải chỉnh texture. Dùng
  alpha thật, không thay nền bằng trắng hoặc đen.
- Ghép theo transform, pivot và draw_order trong manifest sau khi cắt xong.
  Dùng ảnh tư thế nghỉ để kiểm tra nhận diện và tỉ lệ, không yêu cầu khớp
  từng pixel với bộ mảnh.
- Cây xương: root → pelvis → chest → neck/head; hai chuỗi
  shoulder → elbow → grip; hai chuỗi hip → knee → ankle; thêm chuỗi phụ cho
  tóc sau và áo choàng lông.
- Đặt polygon/mesh và trọng số cho các phần cần biến dạng. Chỉnh overlap,
  mesh boundary hoặc visibility mask để không lộ đường nối khi xoay. Không
  tuyên bố rig xong chỉ vì tạo được các node xương.
- Bàn tay liền cẳng tay, bàn chân liền cẳng chân: không cố làm ngón tay độc
  lập hoặc đổi nắm tay thành bàn tay mở từ texture hiện có.
- Áo choàng lông và tóc sau cần chuyển động phụ nhẹ, tránh kéo giãn quá mạnh.
  Áo dài là một mảnh nên chỉ dùng bước nhỏ và mesh deformation nếu cần.
- Tạo socket tia sét tại grip_near. Tia sét là sprite riêng, phải đi theo tay
  khi giơ hoặc xoay.
- Clip thử nghiệm (thời lượng gợi ý): idle 2,4 giây loop (thở nhẹ, tóc và
  choàng trễ nhẹ, chân không trượt); move 0,65 giây loop (bước ngắn); attack
  0,7 giây (event attack_hit đúng thời điểm tác động); cast_lightning 1,25
  giây (tích lực, giơ tay, phóng chiêu, hồi; event cast_release, socket không
  rời tay); hit 0,3 giây; defeat 0,9 giây không loop; victory 1,6 giây.
- Làm scene test có nút phát từng clip, pause, đổi tốc độ và lật hướng. Kiểm
  tra trên nền sáng và tối: không mất mảnh, không lộ đường nối lớn, không
  xuyên vai, tách khuỷu hoặc gối, trượt chân hay rơi tia sét khỏi tay.
- Lưu scene, skeleton, mesh weights, animation và hướng dẫn import bằng định
  dạng thật của engine. Nêu rõ texture cần vẽ bổ sung nếu chuyển động vượt
  quá bộ hiện có. Không tự thay nhân vật thành phong cách khác.

## Khung manifest (điền số liệu sau khi cắt, không điền số giả)

```json
{
  "schema": "zeus-cutout-guide/1.0",
  "character_id": "zeus_v2_001",
  "version": "1.0.0",
  "purpose": "Source art and suggested rest-pose layout for external rigging; NOT an engine skeleton or finished animation.",
  "view": "mild three-quarter toward screen right",
  "canvas": {"width": 1024, "height": 1536, "origin": "top-left", "ground_y": 1425},
  "source_art": "zeus_v2_reference_pose.png",
  "parts": "TODO: liệt kê đủ 15 part ở bảng trên, mỗi part ghi file, cell_rect_xywh, source_bounds_xywh, pivot_px, draw_order sau khi cắt xong"
}
```
