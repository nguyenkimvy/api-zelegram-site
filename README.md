# api-zelegram-site
Cloudflare Worker for bidirectional forwarding between Telegram Bot API and your custom server.

![workers](screenshots/workers.png)

## ✨ Features

- Bi-directional request forwarding
- Transparently proxies Telegram API requests
- Supports `setWebhook` and custom webhook forwarding
- Easily deployable on Cloudflare Workers
- Can be mapped to a custom domain

## 🌐 Example Use Cases

- Bypass restrictions when Telegram API is blocked
- Securely proxy bot API calls via your own domain
- Integrate with services that require fixed webhook URLs

## 🚀 Quick Deploy

### 1. Login or Sign Up on Cloudflare

Go to [https://dash.cloudflare.com](https://dash.cloudflare.com)

### 2. Go to **Workers & Pages** → **Create Application**
![Create Application](screenshots/step2.png)

### 3. Create a new Worker
![Create a new Worker](screenshots/step3.png)

### 4. Choose "Hello World"
![Hello World](screenshots/step4.png)
![Hello World](screenshots/step5.png)

### 5. Click **Edit code**:
![Edit code](screenshots/step6.png)

#### And replace everything with:
```js
export default {
  async fetch(request) {
    const url = new URL(request.url);
    const webhooked = url.searchParams.get("webhooked");
    
    if (webhooked) {
      return fetch(new Request(decodeURIComponent(webhooked), request));
    }

    let body = request.body;
    if (url.pathname.includes("/setWebhook") && request.body) {
      const text = await request.text();
      try {
        const json = JSON.parse(text);
        if (json.url) {
          json.url = `${url.protocol}//${url.host}/webhook?webhooked=${encodeURIComponent(json.url)}`;
          body = JSON.stringify(json);
        }
      } catch { body = text; }
    }

    return fetch(`https://api.telegram.org${url.pathname}${url.search}`, {
      method: request.method,
      headers: request.headers,
      body
    });
  }
}

````

### 6. Click **Deploy**

### 7. Copy your deployed URL (e.g. `https://api.zelegram.site`) and use it in your Telegram bot's `setWebhook`.

### (Optional) Add a custom domain to the worker for production use.

## 🛡️ Disclaimer
This project is provided "as-is" without any warranty.

---

## 🧪 Try it live

Try it:
👉 **[https://api.zelegram.site](https://api.zelegram.site)**
![Edit code](screenshots/tryit.png)

---

## 🎓 Learn how to build automation with n8n + AI

- 💡 Muốn học cách xây dựng quy trình automation mạnh mẽ kết hợp **n8n + AI**?
- 👉 Tham gia ngay khoá học tại: [https://n8npremium.com](https://n8npremium.com)
- 🌱 Tự động hoá Zalo trong n8n, Tài nguyên miễn phí, bot mẫu, cộng đồng hỗ trợ: 👉 [https://linktr.ee/n8nzalo](https://linktr.ee/n8nzalo)
- 👉 Tự động hoá không còn là chuyện của developer — ai cũng có thể làm được!

---

