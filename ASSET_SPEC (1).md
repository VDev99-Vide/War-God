# Asset 2D — Thần hươu băng — spec cắt part và prompt cho AGY

Nguồn thiết kế: `../stag_god_catalogue_v1.png` (catalogue 6 ô) và ảnh tư thế
nghỉ `stag_god_reference_pose.png` trong thư mục này. Nhân vật này chưa có tên
riêng; tạm gọi là thần hươu băng.

## Trạng thái bộ này

Đã có: ảnh tư thế nghỉ toàn thân (góc 3/4 nhẹ hướng sang phải) + file spec này.
Chưa có: các part PNG đã cắt có alpha, manifest đầy đủ số liệu, preview HTML,
rig và animation. Các part phải được cắt từ atlas theo đúng quy trình của pack
ZEUS_2D_ASSET_V1 rồi mới điền số liệu vào manifest. Không tự điền số liệu
pixel khi chưa cắt.

## Quy ước canvas (giống pack v1)

- Canvas ghép mẫu: 1024 × 1536. Gốc tọa độ ở góc trên trái, X sang phải,
  Y xuống dưới. Góc xoay tính bằng độ, dương theo chiều kim đồng hồ.
- Điểm gốc đứng trên mặt đất gợi ý: (512, 1425).
- Quy ước near/far PHẢI xác định lại theo pose thực tế của nhân vật này, vì
  pose này cầm trượng ở phía phải màn hình, ngược với pack v1. Trong pose này:
  bên cầm trượng là bên gần camera (foreground), bên còn lại là bên xa. Khi
  đổi hướng, lật toàn bộ root, không lật riêng từng part.
- Mỗi part dùng pivot riêng trong file đó, không căn tất cả theo tâm file.
- Thứ tự biến đổi: translate đến vị trí, rotate, scale, rồi vẽ texture tại
  trừ pivot. Vẽ theo `draw_order` tăng dần. Trượng là sprite gắn socket tay,
  luôn đi theo tay cầm.

## Danh sách part (15 part, đặt tên theo mẫu v1)

| id | Nội dung | Ghi chú cắt |
|---|---|---|
| head_antlers | Đầu hươu, gạc lớn, tinh thể treo | Gạc cứng nên gắn cùng xương đầu; tinh thể treo tách thành mảnh nhỏ riêng nếu cần đung đưa, nếu không cứ để chung |
| fur_back | Bờm lông trắng xám phía sau | Mảnh riêng để làm chuyển động phụ |
| torso | Thân, ngực, áo giáp rune xanh | |
| mantle | Áo choàng lông vai | Mesh riêng, không khóa vào thân |
| robe | Áo dài xám rách nhiều lớp | Một mảnh như váy của v1 |
| belt | Thắt lưng gắn đá xanh | Tách riêng khỏi áo dài |
| upper_arm_near | Bắp tay bên gần | |
| upper_arm_far | Bắp tay bên xa | |
| forearm_hand_near | Cẳng tay, bao tay lông, bàn tay bên gần | Bàn tay liền cẳng tay như v1 |
| forearm_hand_far | Cẳng tay, bao tay lông, bàn tay bên xa | Như trên, phía đối diện |
| thigh_near | Đùi phủ lông bên gần | |
| thigh_far | Đùi phủ lông bên xa | |
| shin_foot_near | Cẳng chân và bàn chân thú có móng bên gần | Bàn chân liền cẳng chân như v1 |
| shin_foot_far | Cẳng chân và bàn chân thú có móng bên xa | Như trên, phía đối diện |
| staff | Trượng gỗ xoắn, tinh thể xanh | Sprite riêng, gắn socket tay cầm trượng |

Thứ tự vẽ gợi ý từ sau ra trước: fur_back, mantle, các part far (tay, chân),
torso, robe, belt, các part near (chân, tay), head_antlers, staff ở trên cùng
khi cần che tay hợp lý.

## Prompt cho AGY (dựa theo AGY_PROMPT_VI.md của pack v1)

Dùng bộ asset này để tạo nhân vật 2D có xương và animation cho prototype game
mobile theo lượt 1v1. Đây là công việc rig/animation, không dựng model 3D,
không chuyển PNG thành GLB. Trước khi làm, đọc file spec này, catalogue và
ảnh tư thế nghỉ. Nếu chưa có dự án hoặc chưa xác định engine, báo rõ và hỏi
lựa chọn engine trước khi tạo định dạng rig phụ thuộc engine.

- Giữ nguyên ảnh nguồn, làm bản làm việc riêng nếu phải chỉnh texture. Dùng
  alpha thật, không thay nền bằng trắng hoặc đen.
- Ghép theo transform, pivot và draw_order trong manifest sau khi cắt xong.
  Dùng ảnh tư thế nghỉ để kiểm tra nhận diện và tỉ lệ, không yêu cầu khớp
  từng pixel với bộ mảnh.
- Cây xương: root → pelvis → chest → neck/head; hai chuỗi
  shoulder → elbow → grip; hai chuỗi hip → knee → ankle; thêm chuỗi phụ cho
  bờm lông sau và áo choàng.
- Đặt polygon/mesh và trọng số cho các phần cần biến dạng. Chỉnh overlap,
  mesh boundary hoặc visibility mask để không lộ đường nối khi xoay. Không
  tuyên bố rig xong chỉ vì tạo được các node xương.
- Bàn tay liền cẳng tay, bàn chân thú liền cẳng chân: không cố làm ngón tay
  độc lập từ texture hiện có. Gạc gắn cứng với đầu, chỉ đung đưa tinh thể
  treo nếu đã tách mảnh riêng.
- Bờm lông và áo choàng cần chuyển động phụ nhẹ, tránh kéo giãn quá mạnh.
  Áo dài là một mảnh nên chỉ dùng bước nhỏ và mesh deformation nếu cần.
  Muốn chạy mạnh hoặc đá cao thì cần tách các tà áo hoặc dựng mesh biến dạng.
- Tạo socket trượng tại tay cầm trượng. Trượng là sprite riêng, phải đi theo
  tay khi giơ hoặc xoay.
- Clip thử nghiệm (thời lượng gợi ý): idle 2,4 giây loop (thở nhẹ, bờm và
  choàng trễ nhẹ, chân không trượt); move 0,65 giây loop (bước ngắn); attack
  0,7 giây (vung trượng, event attack_hit đúng thời điểm tác động);
  cast_spell 1,25 giây (tích lực rune xanh, giơ trượng, phóng chiêu, hồi;
  event cast_release, socket không rời tay); hit 0,3 giây; defeat 0,9 giây
  không loop; victory 1,6 giây.
- Làm scene test có nút phát từng clip, pause, đổi tốc độ và lật hướng. Kiểm
  tra trên nền sáng và tối: không mất mảnh, không lộ đường nối lớn, không
  xuyên vai, tách khuỷu hoặc gối, trượt chân hay rơi trượng khỏi tay.
- Lưu scene, skeleton, mesh weights, animation và hướng dẫn import bằng định
  dạng thật của engine. Nêu rõ texture cần vẽ bổ sung nếu chuyển động vượt
  quá bộ hiện có. Không tự thay nhân vật thành phong cách khác.

## Khung manifest (điền số liệu sau khi cắt, không điền số giả)

```json
{
  "schema": "zeus-cutout-guide/1.0",
  "character_id": "stag_god_001",
  "version": "1.0.0",
  "purpose": "Source art and suggested rest-pose layout for external rigging; NOT an engine skeleton or finished animation.",
  "view": "mild three-quarter toward screen right",
  "canvas": {"width": 1024, "height": 1536, "origin": "top-left", "ground_y": 1425},
  "source_art": "stag_god_reference_pose.png",
  "parts": "TODO: liệt kê đủ 15 part ở bảng trên, mỗi part ghi file, cell_rect_xywh, source_bounds_xywh, pivot_px, draw_order sau khi cắt xong"
}
```
