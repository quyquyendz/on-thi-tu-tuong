# Hướng dẫn gắn Firebase vào web ôn thi — làm 1 lần, khoảng 10 phút

Web (`index.html` trong thư mục này) đã có sẵn toàn bộ phần đăng nhập Firebase: đăng ký/đăng nhập bằng **email + mật khẩu**, đăng nhập **Google**, **quên mật khẩu qua email**, và **lịch sử làm bài đồng bộ** (lưu trên Firestore theo từng tài khoản). Việc còn lại chỉ là tạo project Firebase của bạn, lấy `firebaseConfig` dán vào file, và up lại lên GitHub.

Làm trên điện thoại được, nhưng mở Firebase Console bằng trình duyệt máy tính sẽ dễ hơn. Đăng nhập Firebase bằng đúng tài khoản Google của bạn.

---

## Bước 1 — Tạo project

1. Vào **console.firebase.google.com** → **Create a project** (Tạo dự án).
2. Đặt tên, ví dụ `on-thi-tu-tuong` → Continue → tắt Google Analytics cũng được → **Create project** → đợi → Continue.

## Bước 2 — Bật đăng nhập Email và Google

1. Menu trái: **Build → Authentication** → **Get started**.
2. Tab **Sign-in method** → bấm **Email/Password** → bật **Enable** → Save.
3. Quay lại danh sách → bấm **Google** → bật **Enable**, chọn email hỗ trợ là email của bạn → Save.

## Bước 3 — Tạo Firestore (nơi lưu lịch sử làm bài)

1. Menu trái: **Build → Firestore Database** → **Create database** → chọn **Start in production mode** → Create/Enable.
2. Vào tab **Rules** của Firestore, xóa nội dung cũ, dán đúng đoạn này rồi bấm **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/results/{docId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Đoạn rules này có nghĩa: mỗi tài khoản chỉ đọc/ghi được lịch sử của chính mình.

## Bước 4 — Lấy firebaseConfig

1. Bấm biểu tượng bánh răng cạnh **Project Overview** → **Project settings**.
2. Mục **Your apps** → bấm biểu tượng web **`</>`** → đặt nickname, ví dụ `web-on-thi` → **Register app**.
   (Không tích "Firebase Hosting" — web của bạn ở trên GitHub Pages rồi.)
3. Firebase hiện một đoạn mã, trong đó có:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
};
```

**Copy cả 6 dòng này.** Có 2 cách gắn vào web:

- **Cách nhanh nhất:** gửi nguyên đoạn config đó cho Muse ở đây — Muse dán vào file và gửi lại file hoàn chỉnh cho bạn up lên GitHub. Config này là khóa công khai của web app (không phải mật khẩu), nó vốn sẽ hiện công khai trong mã nguồn web khi chạy, nên gửi ở đây được.
- **Tự dán:** mở file `index.html`, tìm chữ `DAN firebaseConfig CUA BAN VAO DAY` ở gần cuối file, thay 6 dòng `DAN_..._VAO_DAY` bằng 6 dòng của bạn, giữ nguyên dấu phẩy và dấu ngoặc.

## Bước 5 — Cấp phép tên miền GitHub Pages (rất hay quên!)

Nếu bỏ qua bước này, đăng nhập sẽ báo lỗi `auth/unauthorized-domain`.

1. Trong Firebase Console: **Authentication → Settings → Authorized domains** → **Add domain**.
2. Thêm tên miền GitHub Pages của bạn, chỉ phần tên miền, ví dụ: `ten-github-cua-ban.github.io`
   (`localhost` và tên miền firebaseapp.com đã có sẵn, để nguyên.)

## Bước 6 — Up lên GitHub và kiểm thử

1. Upload file `index.html` (đã có config) lên repo GitHub của bạn, thay file cũ → Commit. Đợi 1–2 phút cho GitHub Pages cập nhật.
2. Mở link web bằng điện thoại và kiểm lần lượt:
   - Mở web không còn thấy khung vàng "chưa gắn firebaseConfig".
   - **Đăng ký** bằng email của bạn → phải vào thẳng được phần ôn thi.
   - Bấm **Đăng xuất** → **Đăng nhập** lại bằng email vừa tạo → vào được.
   - Làm thử một đề ngắn, nộp bài → **đăng nhập bằng cùng tài khoản trên thiết bị/trình duyệt khác** → lịch sử làm bài phải hiện giống nhau.
   - Thử nút **Quên mật khẩu?**: nhập email → Firebase gửi thư đặt lại mật khẩu (kiểm cả mục Spam).
   - Thử **Đăng nhập bằng Google**.
3. Lỗi thường gặp:
   - `auth/operation-not-allowed`: chưa bật Email/Password hoặc Google ở Bước 2.
   - `auth/unauthorized-domain`: thiếu Bước 5.
   - Lịch sử không đồng bộ nhưng đăng nhập vẫn được: kiểm lại Firestore đã tạo chưa (Bước 3) và Rules đã Publish chưa.

## Ghi chú

- Toàn bộ 400 câu trong bản Firebase này đã được đối chiếu bằng máy với file gốc của bạn: **giữ nguyên 100%, không sửa đáp án nào** (kể cả 2 câu Ch6–21/25 theo chốt của bạn).
- Gói Firebase miễn phí (Spark) đủ cho nhu cầu ôn thi của một lớp: đăng nhập và ghi lịch sử với số lượng này không tốn phí.
