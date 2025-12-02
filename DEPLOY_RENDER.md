# Hướng dẫn Deploy lên Render

## Chuẩn bị

1. Tài khoản GitHub
2. Tài khoản Render (https://render.com)
3. Gemini API Key từ Google AI Studio (https://aistudio.google.com/apikey)

## Bước 1: Push code lên GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <your-github-repo-url>
git push -u origin main
```

## Bước 2: Deploy trên Render

### Cách 1: Sử dụng render.yaml (Tự động)

1. Đăng nhập vào Render Dashboard
2. Click **"New +"** → **"Blueprint"**
3. Chọn repository GitHub của bạn
4. Render sẽ tự động phát hiện file `render.yaml` và cấu hình
5. Trong phần **Environment Variables**, thêm:
   - Key: `GEMINI_API_KEY`
   - Value: `<your-gemini-api-key>`
6. Click **"Apply"**

### Cách 2: Cấu hình thủ công

1. Đăng nhập vào Render Dashboard
2. Click **"New +"** → **"Static Site"**
3. Kết nối với GitHub repository
4. Cấu hình:
   - **Name**: `ai-bring-to-life` (hoặc tên bạn muốn)
   - **Build Command**: `npm install && npm run build`
   - **Publish Directory**: `dist`
   - **Auto-Deploy**: Yes
5. Trong **Environment** tab, thêm:
   - Key: `GEMINI_API_KEY`
   - Value: `<your-gemini-api-key>`
6. Trong **Redirects/Rewrites** tab, thêm rule:
   - **Source**: `/*`
   - **Destination**: `/index.html`
   - **Action**: Rewrite
7. Click **"Create Static Site"**

## Bước 3: Đợi build hoàn tất

Render sẽ tự động:
- Cài đặt dependencies
- Build dự án
- Deploy lên production

Thời gian build: ~2-5 phút

## Bước 4: Truy cập ứng dụng

Sau khi deploy thành công, bạn sẽ nhận được URL:
```
https://ai-bring-to-life.onrender.com
```

## Lưu ý quan trọng

### Biến môi trường
- Vite yêu cầu prefix `VITE_` cho các biến môi trường client-side
- File `vite.config.js` đã được cấu hình để map `GEMINI_API_KEY` thành `process.env.API_KEY`
- **⚠️ BẢO MẬT QUAN TRỌNG**: 
  - **KHÔNG BAO GIỜ** commit API key vào Git!
  - **KHÔNG** hardcode API key trong file `render.yaml`
  - PHẢI thêm `GEMINI_API_KEY` qua Render Dashboard trong phần Environment Variables
  - File `render.yaml` chỉ khai báo tên biến, giá trị sẽ được set riêng trên Render

### Auto Deploy
- Mỗi khi push code mới lên GitHub, Render sẽ tự động rebuild và deploy

### Free Tier Limitations
- Render free tier có giới hạn bandwidth và build minutes
- Ứng dụng có thể "sleep" sau 15 phút không hoạt động
- Lần truy cập đầu tiên sau khi sleep sẽ mất ~30 giây để wake up

## Troubleshooting

### Build fails
- Kiểm tra Node version trong `package.json` (đã set >=18.0.0)
- Xem build logs để tìm lỗi cụ thể

### 404 trên routes
- Đảm bảo đã thêm rewrite rule `/* → /index.html`

### API không hoạt động
- Kiểm tra đã thêm `GEMINI_API_KEY` trong Environment Variables chưa
- Verify API key còn valid tại Google AI Studio

### 502 Error
- Đảm bảo chọn "Static Site", không phải "Web Service"
- Kiểm tra `staticPublishPath` trỏ đến `./dist`

## Cấu trúc file quan trọng

```
.
├── render.yaml          # Cấu hình deploy tự động
├── package.json         # Dependencies và build scripts
├── vite.config.js       # Cấu hình Vite + env variables
├── dist/               # Thư mục build output (ignored)
└── DEPLOY_RENDER.md    # File hướng dẫn này
```

## Liên kết hữu ích

- Render Docs: https://render.com/docs/static-sites
- Vite Deploy Guide: https://vite.dev/guide/static-deploy
- Gemini API: https://ai.google.dev/gemini-api/docs
