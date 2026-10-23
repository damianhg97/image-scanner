# Base Image Scanner (Trivy)

Công cụ scan **base image** trước khi đưa vào sử dụng trong project.

Hỗ trợ:
- Scan nhiều base image cùng lúc
- Báo cáo Table (trên log Actions)
- Báo cáo HTML (tải về xem offline)
- Báo cáo SARIF (hiện trên tab **Security** của GitHub)
- Fail pipeline nếu phát hiện lỗ hổng CRITICAL/HIGH (đã có bản vá)

---

## Cấu trúc repo

```
base-image-scanner/
├── .github/
│   └── workflows/
│       └── scan-base-image.yml
├── images.txt
├── .gitignore
└── README.md
```

---

## Cách sử dụng

### 1. Thêm base image cần kiểm tra

Mở file `images.txt` và thêm image (mỗi dòng 1 image):

```txt
eclipse-temurin:25-jre
eclipse-temurin:21-jre
python:3.12-slim
```

### 2. Chạy scan

**Cách 1 – Tự động**
- Push code lên nhánh `main` hoặc `master` → workflow tự chạy.

**Cách 2 – Thủ công**
1. Vào tab **Actions**
2. Chọn workflow **Scan Base Images with Trivy**
3. Bấm **Run workflow**
4. (Tùy chọn) nhập 1 image cụ thể nếu chỉ muốn scan 1 cái

### 3. Xem báo cáo

| Loại báo cáo | Cách xem |
|--------------|----------|
| **Table** | Tab **Actions** → chọn run → mở step "Scan base images" |
| **HTML** | Tab **Actions** → chọn run → phần **Artifacts** → tải `trivy-html-reports` |
| **SARIF** | Tab **Security** → **Code scanning alerts** (hoặc tải artifact `trivy-sarif-reports`) |

---

## Cấu hình quan trọng

Trong workflow bạn có thể chỉnh các tham số sau:

| Tham số | Ý nghĩa | Giá trị mặc định |
|---------|---------|------------------|
| `--severity` | Mức độ lỗ hổng cần quan tâm | `CRITICAL,HIGH` |
| `--ignore-unfixed` | Bỏ qua lỗ hổng chưa có bản vá | Bật |
| `--exit-code 1` | Fail pipeline nếu có lỗ hổng | Bật |

---

## Ví dụ kết quả

```
==============================================
Scanning: eclipse-temurin:25-jre
==============================================
eclipse-temurin:25-jre (ubuntu 24.04)

Total: 0 (CRITICAL: 0, HIGH: 0)

✅ eclipse-temurin:25-jre → CLEAN
```

Nếu có lỗ hổng:

```
❌ eclipse-temurin:25-jre → CÓ LỖ HỔNG CRITICAL/HIGH
```

Pipeline sẽ **fail**.

---

## Khuyến nghị

- Chỉ đưa vào `images.txt` những base image bạn **thực sự định dùng**.
- Pin base image bằng digest khi dùng trong Dockerfile:
  ```dockerfile
  FROM eclipse-temurin:25-jre@sha256:xxxxxxxx
  ```
- Chạy lại scan định kỳ vì database lỗ hổng luôn được cập nhật.

---

## License

MIT
