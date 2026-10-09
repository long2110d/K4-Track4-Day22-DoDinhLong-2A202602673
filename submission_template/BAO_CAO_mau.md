# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** Tây Tạng **Thành viên:** Đỗ Đình Long (2A202602673), Nguyễn Hồng Cường (2A202602415)

Detector cố định: `yolo26n.pt`, ảnh 640 px, Re-ID `osnet_x0_25_msmt17`. Không đổi các mục này trong bài nộp chính.

## 1. Cấu hình đã chọn

File kết quả chạy đủ frame: [`ket_qua/video_1.txt`](ket_qua/) … `ket_qua/video_5.txt`.

Mỗi video: tracker bạn nộp, `conf`, `iou`, điều bạn **nhìn thấy** trên video, và một cấu hình đã thử rồi loại.

| Video | Tracker | conf | iou | Quan sát khi xem video | Đã thử nhưng loại |
|---|---|---|---|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | botsort | 0.3 | 0.7 | Người gần và vừa giữ ID ổn định. Detector nano bỏ sót rất nhiều người nhỏ ở xa: frame đầu chỉ có 6 hộp ở conf 0.3, trong khi nhãn có khoảng 30 người. Đổi ID chủ yếu xảy ra khi hai người đi ngang nhau trong nhóm đông. | `bytetrack` 0.3/0.5: HOTA 26.9, ít hộp hơn hẳn. `botsort` 0.5/0.5: HOTA 27.2, mất nhiều người. `strongsort` 0.15/0.5: IDF1 cao nhất (32.6) nhưng 109 lần đổi ID và 202 ID. |
| video_2 (phố đêm, tĩnh, rất đông) | botsort | 0.15 | 0.7 | Ở conf 0.15, tracker bắt thêm được người trong đám đông phía trên trái (ở 0.3 chỉ còn 2 hộp). Người ở tiền cảnh giữ cùng ID suốt hàng trăm frame (ID 2–6). Không thấy hộp nhấp nháy trên đèn đường hay ô tô. Đám đông rất xa vẫn không có hộp, do giới hạn của detector. | `bytetrack` 0.3/0.5: ít hộp nhất, bỏ hẳn người ở vỉa hè phải. `botsort` 0.3/0.5: tỉ lệ track ngắn (<15 frame) là 26%, so với 5% của cấu hình đã chọn. |
| video_3 (camera di động, ảnh nhỏ) | botsort | 0.15 | 0.7 | Người sát camera giữ ID tốt (ID 179 và 192 suốt hơn 40 frame) dù camera bước theo. Người nhỏ phía xa vẫn đổi ID khi bị che. Đây là video khó nhất: track trung bình chỉ khoảng 35 frame. | `bytetrack` 0.3/0.5: ít ID hơn nhưng cũng ít hộp hơn, có lúc vẽ hai hộp lồng nhau trên cùng một người. `ocsort`: phủ track thấp nhất (track hay đứt). |
| video_4 (trong nhà, camera di chuyển) | botsort | 0.15 | 0.5 | Không thấy hộp giả trên kính hay trên sàn bóng phản chiếu. Người áo trắng và người áo đỏ ở giữa hành lang giữ ID lâu. conf 0.15 bắt thêm người nhỏ ở cuối hành lang. | `botsort` 0.15/0.7: có hộp chồng lên nhau ở nhóm người đứng sát (frame 800). `bytetrack` 0.3/0.5: bỏ sót người ở xa. |
| video_5 (trên xe bus, giao lộ đông) | botsort | 0.15 | 0.5 | Người đi bộ trên vỉa hè giữ ID qua cả đoạn xe rung (ID 50, 64, 91 từ frame 300 đến 360). Người rất nhỏ ở cuối phố vẫn chưa có hộp. | `bytetrack` 0.3/0.5: chỉ 2.7 hộp/frame, người bên trái bị đổi ID (45 → 58). `botsort` 0.5/0.5: chỉ 2.4 hộp/frame. |

Cách làm: mỗi video chạy cả 5 tracker ở `conf 0.3 / iou 0.5`, đủ frame. Sau đó, với tracker tốt nhất (BoT-SORT), lần lượt đổi một tham số: `conf` 0.15 / 0.5, rồi `iou` 0.4 / 0.7, rồi thử tổ hợp `0.15 / 0.7`. Với video không nhãn, nhóm vẽ ID lên cùng một frame của hai cấu hình để so sánh bằng mắt, kèm số liệu phụ: số hộp/frame, số ID, độ dài track, tỉ lệ track ngắn.

## 2. Số liệu video_1

Dán bảng HOTA / MOTA / IDF1 do `scripts/evaluate_practice.py` in ra.

```
HOTA: nhom01_video1-pedestrian     HOTA      DetA      AssA      DetRe     DetPr     AssRe     AssPr     LocA      OWTA      HOTA(0)   LocA(0)   HOTALocA(0)
video_1                            30.004    18.426    49.123    19.152    75.062    52.446    80.974    83.073    30.626    37.161    76.949    28.595
CLEAR: nhom01_video1-pedestrian    MOTA      MOTP      MODA      CLR_Re    CLR_Pr    MTR       PTR       MLR       sMOTA     CLR_TP    CLR_FN    CLR_FP    IDSW      MT        PT        ML        Frag
video_1                            19.278    80.822    19.455    22.485    88.125    14.516    17.742    67.742    14.966    4178      14403     563       33        9         11        42        105
Identity: nhom01_video1-pedestrian IDF1      IDR       IDP       IDTP      IDFN      IDFP
video_1                            29.749    18.67     73.17     3469      15112     1272
Count: nhom01_video1-pedestrian    Dets      GT_Dets   IDs       GT_IDs
video_1                            4741      18581     54        62
```

Các cấu hình đã chấm trên video_1 (đủ 600 frame):

| Tracker | conf | iou | HOTA | DetA | AssA | MOTA | IDF1 | IDSW |
|---|---|---|---|---|---|---|---|---|
| bytetrack | 0.3 | 0.5 | 26.9 | 15.1 | 48.1 | 17.3 | 25.7 | 12 |
| ocsort | 0.3 | 0.5 | 27.4 | 17.9 | 42.3 | 19.8 | 28.7 | 46 |
| deepocsort | 0.3 | 0.5 | 27.4 | 17.8 | 42.3 | 19.8 | 27.8 | 50 |
| strongsort | 0.3 | 0.5 | 28.7 | 17.7 | 46.6 | 19.7 | 29.9 | 42 |
| botsort | 0.3 | 0.5 | 29.5 | 18.1 | 48.2 | 19.8 | 29.3 | 25 |
| botsort | 0.15 | 0.5 | 29.3 | 19.3 | 45.0 | 20.8 | 29.7 | 29 |
| botsort | 0.5 | 0.5 | 27.2 | 14.3 | 51.7 | 15.3 | 24.6 | 10 |
| botsort | 0.3 | 0.4 | 29.3 | 17.4 | 49.6 | 19.5 | 29.8 | 19 |
| **botsort** | **0.3** | **0.7** | **30.0** | 18.4 | 49.1 | 19.3 | 29.7 | 33 |
| botsort | 0.15 | 0.7 | 29.7 | 19.5 | 45.5 | 20.4 | 30.0 | 41 |
| strongsort | 0.15 | 0.5 | 29.2 | 21.6 | 40.1 | 19.9 | 32.6 | 109 |

`video_2` đến `video_5` không có nhãn trong gói lab. Không điền số cho các video đó.

## 3. Phân tích

**video_1 (quảng trường, camera tĩnh, ban ngày).** Điểm nghẽn là phát hiện chứ không phải liên kết: DetA chỉ khoảng 18 trong khi AssA khoảng 49, và FN khoảng 14 400 so với FP khoảng 560. YOLO nano ở 640 px không thấy người nhỏ ở xa. BoT-SORT thắng ByteTrack vì nhận thêm hộp điểm thấp vào vòng ghép thứ hai (ngưỡng tạo track mới thấp), nên DetA cao hơn (18.4 so với 15.1). Nhờ có Re-ID, nó vẫn giữ AssA ngang ByteTrack. `iou 0.7` giữ được hai người đứng sát nhau mà NMS 0.5 sẽ gộp làm một. Bảng cũng cho thấy ba metric kể ba câu chuyện khác nhau. StrongSORT ở conf 0.15 có IDF1 cao nhất (32.6) nhưng đổi ID 109 lần và AssA thấp nhất. ByteTrack có ít lần đổi ID nhất (12) nhưng HOTA thấp nhất, vì nó bỏ sót người chứ không phải giữ ID giỏi hơn.

**video_2 (phố đêm, camera tĩnh, rất đông), chỉ đánh giá bằng mắt.** Trời tối làm điểm tin cậy của detector thấp, nên `conf 0.15` là thay đổi có tác dụng lớn nhất: tracker bắt thêm người trong đám đông, và track dài hơn hẳn (trung vị khoảng 170 frame so với 92, tỉ lệ track ngắn 5% so với 26%). Ánh đèn mạnh không sinh hộp giả. Cảnh rất đông, người hay đứng sát và che nhau, nên tracker chỉ dùng chuyển động (ByteTrack, OC-SORT) dễ nhảy hộp sang người bên cạnh. Re-ID của BoT-SORT giúp nhận lại đúng người sau khi bị che. `iou 0.7` tránh việc NMS gộp hai người đứng sát thành một hộp.

**video_5 (trên xe bus, rung lắc), chỉ đánh giá bằng mắt.** Camera vừa chạy vừa rung, nên vị trí trong ảnh của một người đứng yên vẫn nhảy giữa các frame. Bộ lọc Kalman thuần chuyển động (ByteTrack) đoán sai vị trí, dẫn đến mất track và đổi ID (ID 45 → 58). BoT-SORT có bù chuyển động camera (sparse optical flow) trước khi ghép, kèm Re-ID, nên giữ ID ổn định hơn. Người đi bộ ở đây nhỏ và chiếm ít pixel, nên `conf 0.15` cần thiết để có hộp. Ở `conf 0.5` gần như mất hết người.

**video_3 và video_4 (camera di chuyển).** Hai video này dùng cùng lý lẽ bù chuyển động camera. Với video_3 (ảnh 640×480, ít khung hình/giây), mỗi người dịch chuyển nhiều giữa hai frame, và khác biệt giữa các tracker nhỏ hơn: cả năm tracker đều có trung vị độ dài track chỉ khoảng 13–16 frame.

## 4. Nếu có thêm thời gian

Nhóm sẽ thử phần mở rộng (ngoài bài nộp chính) với ảnh đầu vào lớn hơn hoặc detector lớn hơn, vì video_1 cho thấy mất điểm chủ yếu ở phát hiện (DetA khoảng 18) chứ không phải ở giữ ID. Nhóm cũng sẽ chỉnh ngưỡng bên trong BoT-SORT (`track_buffer`, `appearance_thresh`) cho video_3 có ít khung hình/giây, và xem kỹ các frame đổi ID trong video_2.
