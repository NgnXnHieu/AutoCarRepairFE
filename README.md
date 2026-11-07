# AutoCar — Hệ thống quản lý trung tâm sửa chữa ô tô (Frontend)

Giao diện web (React + Vite) cho hệ thống quản lý trung tâm sửa chữa ô tô AutoCar — phục vụ 4 nhóm người dùng: khách hàng, lễ tân (receptionist), kỹ thuật viên (mechanic) và quản lý (general manager).

> Dự án frontend này bắt buộc phải chạy cùng với backend **AutoCar** (Spring Boot). Xem hướng dẫn chạy backend tại `README.md` của repo backend.

## Công nghệ sử dụng

| Thành phần | Công nghệ |
|---|---|
| Framework | React 19 + Vite |
| Routing | React Router DOM v7 |
| Styling | Tailwind CSS v4 |
| HTTP client | Axios (có interceptor tự refresh JWT token) |
| Icon | lucide-react, react-icons |
| Biểu đồ | Recharts |
| Form | React Hook Form |
| Thông báo | react-toastify, sweetalert2 |
| Animation | Framer Motion |
| Markdown | react-markdown (hiển thị phản hồi chatbot AI) |

## Yêu cầu môi trường

- **Node.js** 18+ (khuyến nghị LTS mới nhất)
- **npm** (đi kèm Node.js)
- Backend **AutoCar** đã chạy ở `http://localhost:8080` (bắt buộc — frontend gọi API trực tiếp tới địa chỉ này, xem mục Cấu hình bên dưới)

## Cài đặt & chạy dự án

### 1. Clone & cài dependency

```bash
git clone <repo-url>
cd motorcycle-repair-shop
npm install
```

### 2. Khởi chạy backend trước

Dự án **không dùng file `.env`** — địa chỉ API được hardcode trong `src/api/axiosClient.js`:

```js
baseURL: 'http://localhost:8080/api'
```

Đảm bảo backend AutoCar đang chạy ở `http://localhost:8080` trước khi mở frontend. Nếu backend chạy ở cổng/domain khác, cần sửa `baseURL` trong file này.

### 3. Chạy frontend (chế độ dev)

```bash
npm run dev
```

Mặc định ứng dụng chạy ở `http://localhost:5173` (cổng mặc định của Vite).

> ⚠️ Backend đang cấu hình CORS cố định chỉ cho phép origin `http://localhost:5173` (xem `SecurityConfig.java` bên backend). Nếu đổi cổng chạy frontend, cần sửa lại CORS ở backend tương ứng.

### 4. Build production

```bash
npm run build    # build ra thư mục dist/
npm run preview  # xem thử bản build
```

## Cấu trúc thư mục chính

```
src/
├── api/                # Các module gọi API (axiosClient, vehicleApi, customerApi, authApi...)
├── Pages/
│   ├── public/           # Trang công khai: Home, Login, Register
│   ├── customer/          # Trang dành cho khách hàng: đặt lịch, quản lý xe, mua phụ tùng, chatbot...
│   ├── receptionist/       # Trang dành cho lễ tân: tiếp nhận xe, quản lý lịch hẹn
│   ├── mechanic/          # Trang dành cho kỹ thuật viên: xử lý lịch hẹn, chẩn đoán
│   └── general_manager/    # Trang dành cho quản lý: danh mục, dịch vụ, báo cáo...
├── components/          # Component dùng chung (Navbar, modal, form...)
├── layouts/             # Layout khung cho từng nhóm người dùng (EmployeeSidebar...)
├── routes/              # Khai báo route (AppRouter.jsx)
└── utils/               # Hàm tiện ích (xử lý lỗi, format dữ liệu...)
```

## Phân quyền & route theo vai trò

Giao diện tương ứng với các `Role` ở backend (`CUSTOMER`, `RECEPTIONIST`, `MECHANIC`, `BRANCH_MANAGER`, `GENERAL_MANAGER`). Sau khi đăng nhập, JWT được lưu qua cookie (do backend set), `axiosClient` tự động đính kèm cookie (`withCredentials: true`) và tự refresh token khi hết hạn (401) thông qua interceptor trong `src/api/axiosClient.js`.

## Lưu ý

- Dự án **không có file `.env`** — mọi cấu hình API đang hardcode trong code (`axiosClient.js`). Nếu triển khai nhiều môi trường (dev/staging/production), nên cân nhắc chuyển sang biến môi trường Vite (`import.meta.env`).
- Một số nội dung hiển thị (banner trang chủ, mô tả, ảnh nền...) được lấy động từ CMS (bảng `contents` ở backend) qua `contentApi` — nếu nội dung hiển thị không đúng chủ đề (ví dụ còn nhắc tới xe máy), cần sửa trực tiếp qua trang quản trị nội dung, không nằm trong code frontend.
