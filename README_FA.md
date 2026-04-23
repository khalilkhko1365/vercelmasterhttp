# MasterHttpRelayVPN (حالت Vercel)

این پروژه حالا به‌صورت پیش‌فرض با Vercel کار می‌کند و مسیر Google Apps Script حذف شده است.

## معماری

```
Browser -> Local Proxy -> Front Domain (TLS SNI) -> Vercel API relay -> Target website
```

## 1) دیپلوی رله روی Vercel

فایل رله:

- `api/api.js`

در Vercel یک متغیر محیطی تنظیم کنید:

- `AUTH_KEY` = رمز دلخواه شما

مستندات رسمی:

- https://vercel.com/docs/functions/functions-api-reference?framework=other
- https://vercel.com/docs/functions/runtimes/edge-runtime

بعد از دیپلوی، هاست پروژه را بردارید:

- `YOUR_PROJECT.vercel.app`

## 2) تنظیم `config.json`

```json
{
  "mode": "vercel_edge",
  "front_domain": "vercel.com",
  "front_ip": "",
  "worker_host": "YOUR_PROJECT.vercel.app",
  "relay_path": "/api/api",
  "auth_key": "CHANGE_ME",
  "listen_host": "127.0.0.1",
  "listen_port": 8085,
  "log_level": "INFO",
  "verify_ssl": true,
  "hosts": {}
}
```

نکته‌ها:

- مقدار `auth_key` باید با `AUTH_KEY` در Vercel یکی باشد.
- `front_domain` همان SNI قابل مشاهده برای شبکه است.
- `front_ip` اختیاری است (اگر خالی باشد DNS استفاده می‌شود).

## 3) اجرای پراکسی

```bash
pip install -r requirements.txt
python main.py
```

## 4) تنظیم پراکسی مرورگر

- Host: `127.0.0.1`
- Port: `8085`

## 5) نصب CA (برای HTTPS ضروری)

در حالت `vercel_edge` پراکسی TLS را به‌صورت محلی MITM می‌کند، پس باید `ca/ca.crt` را Trusted Root کنید.

فرمان سریع:

```bash
python main.py --install-cert
```

## حالت‌ها

- `vercel_edge`: حالت اصلی (MITM + رله JSON روی Vercel)
- `domain_fronting`: مسیر WebSocket/forward به worker
- `custom_domain`: اتصال مستقیم با دامنه شخصی

## فایل‌های مهم

- `main.py`
- `proxy_server.py`
- `domain_fronter.py`
- `api/api.js`
- `config.json`

## امنیت

- `config.json` را به اشتراک نگذارید.
- پوشه `ca/` را به اشتراک نگذارید.
- `listen_host` را روی `127.0.0.1` نگه دارید.
