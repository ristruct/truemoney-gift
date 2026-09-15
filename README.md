# TrueMoney Voucher API
API สำหรับรับซอง TrueMoney Voucher โดยอัตโนมัติ

## Endpoint Base URL
```
https://api.ristruct.dpdns.org
```
> เปลี่ยนเป็นโดเมนจริงที่คุณ deploy ไว้

### เนื้อหา
- [Introduction](#introduction)
- [Authentication](#authentication)
- [Endpoint](#endpoint)
- [Parameters](#parameters)
- [Examples](#examples) — cURL, Python, JavaScript, PHP, Cloudflare Workers
- [Response](#response)
- [Errors](#errors)

### ข้อมูลเพิ่มเติม
- API นี้รองรับทั้ง **GET** และ **POST** requests
- ต้องระบุ `voucher` hash (หรือลิงก์เต็ม) และเบอร์โทรศัพท์ 10 หลัก
- รองรับรูปแบบข้อมูลทั้ง **JSON** และ **form-data**
- ⚠️ **ต่างจาก API ทั่วไป: endpoint นี้บังคับใช้ API Key ทุกครั้ง** ไม่มี key = ใช้งานไม่ได้

---

## Introduction
TrueMoney Voucher API (Ristruct) ช่วยให้คุณตรวจสอบและแลกรับเงินผ่านลิงก์อั่งเปา TrueMoney ได้โดยอัตโนมัติ ผ่าน API ที่เรียบง่าย รองรับทั้ง HTTP GET และ POST methods

ระบบภายในทำงาน 2 ขั้นตอนต่อ 1 คำขอโดยอัตโนมัติ:
1. **Verify** — ตรวจสอบว่าซองยังใช้ได้ ไม่หมดอายุ ไม่ถูกใช้ไปแล้ว
2. **Redeem** — แลกรับเงินเข้าเบอร์ที่ระบุ ถ้าผ่าน verify

คุณไม่ต้องเรียกสองขั้นตอนเอง ระบบจัดการให้ในคำขอเดียว

---

## Authentication
API นี้**บังคับใช้ API Key** ทุก request ไปยัง `/api/twgift` — ถ้าไม่ส่ง key หรือ key ไม่ถูกต้อง/ไม่ active จะได้ `401 Unauthorized` ทันที ไม่มีข้อยกเว้น

**ขอ API-Key ได้ที่:** <ลิงก์ Discord ของคุณ>

ส่ง API Key ได้ 2 วิธี:

| วิธี | ตัวอย่าง |
|---|---|
| Header (แนะนำ) | `X-API-Key: tmk_xxxxxxxx` |
| Query string (เฉพาะ GET) | `?apikey=tmk_xxxxxxxx` |

Key ที่ออกให้ทุกตัวขึ้นต้นด้วย `tmk_`

---

## Endpoint
```
POST https://api.ristruct.dpdns.org/api/twgift
```
หรือใช้ผ่าน **GET** request โดยส่งพารามิเตอร์ผ่าน query string

---

## Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `voucher` | String | Yes | Voucher hash (เฉพาะส่วนหลัง `v=` ใน URL) เช่น จากลิงก์เต็ม `https://gift.truemoney.com/campaign/?v=019b54d1844e7c99a68f3d394b249e3845X` ให้ส่งเฉพาะ `019b54d1844e7c99a68f3d394b249e3845X` **หรือส่งลิงก์เต็มมาตรงๆ ก็ได้เช่นกัน** ระบบแกะ hash ให้อัตโนมัติ |
| `phone` | String | Yes | เบอร์มือถือ 10 หลัก (ต้องมี 0 นำหน้า) เช่น `0982345678` |

---

## Examples

### cURL
```bash
curl -X POST "https://api.ristruct.dpdns.org/api/twgift" \
  -H "X-API-Key: tmk_xxxxxxxx" \
  -H "Content-Type: application/json" \
  -d '{
    "voucher": "019b54d1844e7c99a68f3d394b249e3845X",
    "phone": "0982345678"
  }'
```
หรือใช้ GET request:
```bash
curl "https://api.ristruct.dpdns.org/api/twgift?voucher=019b54d1844e7c99a68f3d394b249e3845X&phone=0982345678&apikey=tmk_xxxxxxxx"
```

### Python
```python
import requests

url = "https://api.ristruct.dpdns.org/api/twgift"
headers = {"X-API-Key": "tmk_xxxxxxxx"}
payload = {
    "voucher": "019b54d1844e7c99a68f3d394b249e3845X",
    "phone": "0982345678"
}

# Send as JSON
response = requests.post(url, json=payload, headers=headers)
print(response.status_code)
print(response.json())

# Or as form data
# response = requests.post(url, data=payload, headers=headers)
```

### JavaScript
```javascript
// Using fetch API
const url = 'https://api.ristruct.dpdns.org/api/twgift';
const apiKey = 'tmk_xxxxxxxx';
const data = {
  voucher: '019b54d1844e7c99a68f3d394b249e3845X',
  phone: '0982345678'
};

// As JSON
fetch(url, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-API-Key': apiKey
  },
  body: JSON.stringify(data)
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));

// As form data
const formData = new FormData();
formData.append('voucher', data.voucher);
formData.append('phone', data.phone);

fetch(url, {
  method: 'POST',
  headers: { 'X-API-Key': apiKey },
  body: formData
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

### PHP
```php
<?php
$url = "https://api.ristruct.dpdns.org/api/twgift";

$data = [
    "voucher" => "019b54d1844e7c99a68f3d394b249e3845X",
    "phone" => "0982345678"
];

$ch = curl_init($url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'Content-Type: application/json',
    'X-API-Key: tmk_xxxxxxxx'
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

$result = json_decode($response, true);

if ($httpCode == 200 && $result['status']['code'] === 'SUCCESS') {
    echo "แลกรับสำเร็จ: " . $result['data']['voucher']['redeemed_amount_baht'] . " บาท\n";
} else {
    echo "เกิดข้อผิดพลาด: " . json_encode($result) . "\n";
}
```

### Cloudflare Workers
เรียกจาก Worker ตัวอื่น (เช่นระบบเติมซองอัตโนมัติที่ deploy บน Cloudflare Workers เช่นกัน):
```javascript
export default {
  async fetch(request, env, ctx) {
    const API_URL = "https://api.ristruct.dpdns.org/api/twgift";
    const API_KEY = env.TMK_API_KEY; // เก็บผ่าน `wrangler secret put TMK_API_KEY`

    const payload = {
      voucher: "019b54d1844e7c99a68f3d394b249e3845X",
      phone: "0982345678",
    };

    const res = await fetch(API_URL, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "X-API-Key": API_KEY,
      },
      body: JSON.stringify(payload),
    });

    const result = await res.json();

    if (result?.status?.code === "SUCCESS") {
      return new Response(
        `แลกรับสำเร็จ: ${result.data.voucher.redeemed_amount_baht} บาท`,
        { status: 200 }
      );
    }

    return new Response(JSON.stringify(result), {
      status: res.status,
      headers: { "Content-Type": "application/json" },
    });
  },
};
```
> ใช้ `env.TMK_API_KEY` แทนการ hardcode key ในโค้ด — ตั้งค่าด้วย `wrangler secret put TMK_API_KEY` แล้วเรียกผ่าน `env` ใน handler

---

## Response

### Success Response
```json
{
  "status": {
    "message": "success",
    "code": "SUCCESS"
  },
  "data": {
    "voucher": {
      "voucher_id": "996332695962297682",
      "amount_baht": "10.00",
      "redeemed_amount_baht": "10.00",
      "member": 1,
      "status": "active",
      "link": "019b54d1844e7c99a68f3d394b249e3845X",
      "detail": "",
      "expire_date": 1757081437527,
      "type": "F",
      "redeemed": 1,
      "available": 0
    },
    "owner_profile": {
      "full_name": "จิรัสย์ ***"
    },
    "redeemer_profile": {
      "mobile_number": "0982345678"
    }
  }
}
```
> หมายเหตุ: response ส่งต่อโครงสร้างดิบจาก TrueMoney โดยตรง (`voucher`, `owner_profile`, `redeemer_profile`) ระบบนี้ไม่ได้แนบ `my_ticket` หรือ `tickets` เพิ่มเติม

### Error Response (รูปแบบทั่วไป)
```json
{
  "status": "fail",
  "message": "Voucher hash is required"
}
```

---

## Errors

| Error Message | HTTP Status | คำอธิบาย | วิธีแก้ |
|---|---|---|---|
| `Invalid or missing API key — contact admin to get a key` | 401 | ไม่ได้ส่ง API key หรือ key ไม่ถูกต้อง/ถูกปิดใช้งาน | ตรวจสอบว่าใส่ header `X-API-Key` ถูกต้อง หรือติดต่อแอดมินขอ key ใหม่ |
| `Voucher hash is required` | 400 | ไม่ได้ส่งพารามิเตอร์ `voucher` หรือรูปแบบไม่ถูกต้อง | ตรวจสอบให้แน่ใจว่าได้ส่งพารามิเตอร์ `voucher` |
| `Invalid phone number` | 400 | เบอร์โทรไม่ใช่ 10 หลักหรือไม่ได้ขึ้นต้นด้วย 0 | ตรวจสอบว่าเป็นหมายเลขโทรศัพท์ 10 หลักและขึ้นต้นด้วย 0 |
| `Link expired or invalid` | 404 | ลิงก์ซองหมดอายุหรือไม่มีอยู่จริง | ใช้ลิงก์ซองใหม่ |
| `Method Not Allowed` | 405 | เรียกด้วย method อื่นที่ไม่ใช่ GET/POST | ใช้ GET หรือ POST เท่านั้น |
| `{"status":{"code":"VOUCHER_OUT_OF_STOCK", ...}}` | 200 | ซองนี้ถูกใช้ไปแล้ว (ส่งต่อ response ดิบจาก TrueMoney) | ใช้ซองอื่นที่ยังไม่ถูกใช้ |
| `Internal Server Error` | 500 | เกิดข้อผิดพลาดฝั่งเซิร์ฟเวอร์ | ลองใหม่อีกครั้ง หรือแจ้งแอดมิน |

---

## Health Check (public — ไม่ต้องใช้ API key)
```
GET /
GET /health
```
```json
{
  "status": "success",
  "message": "API พร้อมให้บริการแล้วค่ะ!",
  "version": "0.0.1"
}
```

---

**TrueMoney Voucher API** — โดย Ristruct
**ขอ API-Key ได้ที่:** https://discord.gg/VfqZYBuw4V
