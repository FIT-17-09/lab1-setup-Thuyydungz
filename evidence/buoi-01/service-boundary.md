# Service Boundary của nhóm

## 1. Thông tin nhóm

- Tên nhóm: 9
- Lớp: CNTT 17-09
- Thành viên:
+ Cao Văn Thiện
+ Lê Thùy Dung
+ Nguyễn Hương Giang
- Service nhóm phụ trách: Notification 
- Sản phẩm tổng thể của lớp: Smart Campus Operations Platform – Product A
- Đề tài: Xây dựng dịch vụ gửi cảnh báo đa 

## 2. Actor

Các actor tương tác với service gồm:
- Core Business Service: gửi alert sang Notification Service khi phát hiện sự kiện bất thường.
- Analytics Service: lấy log gửi thông báo để phục vụ thống kê, báo cáo.
- Quản trị viên / bảo vệ / nhân sự phụ trách: là đối tượng nhận cảnh báo từ hệ thống.

## 3. System Boundary

Nhóm em xây phần: Notification Service – dịch vụ tiếp nhận cảnh báo và gửi thông báo.

Phần nhóm kiểm soát:

- API tiếp nhận alert
- xử lý gửi cảnh báo
- ghi log trạng thái gửi
- kiểm tra trùng alert ở mức đơn giản
- health check
- Docker hóa service

Phần nhóm chỉ tích hợp:

- dữ liệu alert từ Core Business
- hệ thống thống kê của Analytics
- các kênh gửi thông báo bên ngoài như Telegram, Email hoặc mock console

## 4. Service Boundary

Service của nhóm có trách nhiệm gì?
- nhận alert từ Core Business
- kiểm tra dữ liệu đầu vào
- gửi cảnh báo qua ít nhất một kênh
- lưu trạng thái gửi
- cung cấp log phục vụ Analytics

Service KHÔNG làm gì?
- không phát hiện bất thường từ camera, IoT hoặc access gate
- không xử lý nghiệp vụ trung tâm
- không phân tích dữ liệu AI
- không thay thế Core Business trong việc ra quyết định tạo alert

## 5. Input / Output

### Input

- Notification Service nhận dữ liệu alert từ Core Business.

Ví dụ dữ liệu:

+ alert_id
+ severity
+ message
+ target

### Output

+ trạng thái gửi thành công / thất bại
+ kênh gửi
+ trạng thái duplicate nếu alert bị trùng

Ngoài ra service tạo log gửi để Analytics sử dụng.

## 6. API dự kiến

| Method | Endpoint | Mục đích |
|---|---|---|
| GET | /health | Kiểm tra trạng thái hoạt động của service |
| POST | /alerts | Nhận alert từ Core Business và xử lý gửi cảnh báo |
| GET | /logs | Cung cấp log gửi thông báo cho Analytics

## 7. Phụ thuộc service khác

Service này gọi đến service nào?
Downstream (service hoặc hệ thống mà nhóm gọi ra)
- Telegram Bot
- Email
- Console mock

Service nào gọi đến service này?
Upstream (service gọi đến service của nhóm)
- Core Business Service
- gửi alert sang Notification Service

Consumer (service sử dụng dữ liệu của nhóm)
- Analytics Service
- đọc log gửi thông báo để thống kê
## 8. Sơ đồ minh họa

Có thể vẽ bằng Mermaid, draw.io, Ludichart hoặc ảnh chụp sơ đồ.

```mermaid
flowchart LR
    User[Actor] --> Service[Service của nhóm]
    Service --> DB[(Database)]
    Service --> Other[Service khác]

flowchart LR
    Core[Core Business Service] --> Notification[Notification Service]
    Notification --> Channel[Telegram / Email / Console]
    Notification --> Analytics[Analytics Service]
