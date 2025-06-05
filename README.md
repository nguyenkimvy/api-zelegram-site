
Cloudflare Worker for bidirectional forwarding between Telegram Bot API and your custom server.



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

