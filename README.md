# Soap's Little World — bản sửa lỗi hình ảnh

## Chạy trên máy

```powershell
npm.cmd install
npm.cmd run dev
```

## Build để triển khai GitHub Pages

```powershell
npm.cmd run build
```

## Thay hình minh họa

Các hình minh họa được đặt trong `public/stickers/` và được gọi trực tiếp bằng thẻ `<img>` trong `slides.md`:

- `mascot-soap.svg`: mascot ở bìa.
- `coconut.svg`: hình minh họa dầu dừa ở slide nguyên liệu.
- `mascot-book.svg`: mascot ở slide kết thúc.

Bạn có thể thay nội dung các tệp SVG bằng hình mới, nhưng nên giữ nguyên tên tệp. Nếu dùng PNG, hãy đổi cả phần mở rộng trong `slides.md` tương ứng.

## Ảnh và video thực nghiệm

- Ảnh: đặt trong `public/photos/` theo tên tệp được ghi ở từng `PhotoFrame`.
- Video: đặt trong `public/videos/quy-trinh-thuc-hanh.mp4`.

Không xóa `public/stickers/`, `components/` hoặc `styles/` khi cập nhật.

## Sửa lỗi SVG không hiển thị

Ba hình minh họa không còn được import bằng `?raw` và `v-html`. Chúng được gọi như tài nguyên tĩnh trong `public/stickers/`, tránh phụ thuộc vào biến SVG trong `<script setup>`.
