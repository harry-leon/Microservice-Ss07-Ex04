# Bài tập 4: Tự động cập nhật cấu hình với @RefreshScope

## Mục tiêu
- Thay đổi giá trị biến/cấu hình mà không cần khởi động lại Server (`patient-service`).
- Sử dụng Spring Boot Actuator `/actuator/refresh` kết hợp `@RefreshScope`.

## Cấu trúc dự án
- `config-server`: Spring Cloud Config Server chạy ở cổng `8888`, đọc file cấu hình từ `C:/medical-config-repo`.
- `patient-service`: Spring Boot Application chạy ở cổng `8081`, sử dụng `@RefreshScope` và Actuator.
- `medical-config-repo`: Thư mục cấu hình chứa `patient-service.properties`.

## Cấu hình chi tiết
1. **Actuator Dependency & Exposure**:
   - `spring-boot-starter-actuator` trong `patient-service/pom.xml`.
   - `management.endpoints.web.exposure.include=refresh` trong `patient-service/src/main/resources/application.properties`.

2. **WelcomeController**:
   - Đánh dấu `@RefreshScope` trên `WelcomeController`.
   - Lấy giá trị qua `@Value("${app.welcome}")`.
   - Endpoint GET `/welcome`.

## Các bước kiểm tra (Verification Steps)

1. Khởi động **Config Server** (Port `8888`) và **Patient Service** (Port `8081`).
2. Mở trình duyệt hoặc gửi request:
   `GET http://localhost:8081/welcome`
   -> Ket quả: `Chào mừng tới BV RikkeiAcademy`
3. Cập nhật thuộc tính `app.welcome` trong file `C:/medical-config-repo/patient-service.properties` (và commit git nếu dùng Git repo):
   `app.welcome=Chào mừng tới BV RikkeiEducation`
4. Kích hoạt refresh cấu hình bằng lệnh Terminal:
   `curl -X POST http://localhost:8081/actuator/refresh`
5. Mở trình duyệt lại:
   `GET http://localhost:8081/welcome`
   -> Ket quả: `Chào mừng tới BV RikkeiEducation` (Giá trị cập nhật tức thì không bị gián đoạn hay restart app).
