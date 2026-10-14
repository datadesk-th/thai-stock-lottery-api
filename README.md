# API ผลหวยหุ้นไทย ฟรี (Thai Stock Lottery Results API)

API และป้ายผล (widget) **ผลหวยหุ้น** ดาวโจนส์ · นิเคอิ · ฮั่งเส็ง · หุ้นจีน · ไต้หวัน · สิงคโปร์ — **ฟรี ไม่ต้องสมัคร ไม่ต้องมีคีย์** อัปเดตอัตโนมัติทุกงวด

🌐 เว็บหลัก / ป้ายพร้อมใช้: **https://huaywidget.com**
📖 เอกสาร API: **https://huaywidget.com/api**

---

## ทำอะไรได้

- ✅ ผล 3 ตัวบน / 2 ตัวล่าง งวดล่าสุดของหวยหุ้น 6 ตลาด (นิเคอิ ฮั่งเส็ง และหุ้นจีน มีทั้งรอบเช้าและรอบบ่าย)
- ✅ ข้อมูลดิบประกอบ: ค่าดัชนีปิด (`close`) และค่าเปลี่ยนแปลง (`chg`) ตรวจย้อนได้ทุกงวด
- ✅ JSON เรียกตรงจากเบราว์เซอร์ได้ (เปิด CORS) · แคช 60 วินาที
- ✅ ป้ายผลสำเร็จรูป ก๊อปโค้ด 2 บรรทัดไปแปะ WordPress / Blogger / HTML ได้ทันที

## Endpoint

| Endpoint | ข้อมูล |
|---|---|
| `GET https://huaywidget.com/api/all` | ผลล่าสุดทุกตลาด |
| `GET https://huaywidget.com/api/dowjones` | ดาวโจนส์ |
| `GET https://huaywidget.com/api/nikkei` | นิเคอิ (รอบเช้า + บ่าย) |
| `GET https://huaywidget.com/api/hangseng` | ฮั่งเส็ง (รอบเช้า + บ่าย) |
| `GET https://huaywidget.com/api/china` | หุ้นจีน — ดัชนีเซินเจิ้น (รอบเช้า + บ่าย) |
| `GET https://huaywidget.com/api/taiwan` | หุ้นไต้หวัน |
| `GET https://huaywidget.com/api/singapore` | หุ้นสิงคโปร์ |

## รูปแบบข้อมูล

```json
{
  "id": "nikkei",
  "name": "นิเคอิ",
  "site": "https://nikkeith.com",
  "am": { "round": "2026-10-09", "top3": "230", "bot2": "81", "close": 68512.30, "chg": -529.81 },
  "pm": { "round": "2026-10-09", "top3": "092", "bot2": "19", "close": 68450.92, "chg": -591.19 }
}
```

- `round` = วันที่งวด (YYYY-MM-DD ตามเวลาไทย)
- `top3` / `bot2` = 3 ตัวบน / 2 ตัวล่าง (string เพื่อเก็บเลข 0 นำหน้า)
- `am` มีเฉพาะตลาดที่มีรอบเช้า

## ตัวอย่างการใช้งาน

**JavaScript (เบราว์เซอร์ / Node.js 18+)**

```js
const r = await fetch('https://huaywidget.com/api/dowjones');
const d = await r.json();
console.log(`ดาวโจนส์ งวด ${d.pm.round}: ${d.pm.top3} / ${d.pm.bot2}`);
```

**Python**

```python
import requests
d = requests.get("https://huaywidget.com/api/all", timeout=10).json()
for m in d["markets"]:
    print(m["name"], m["pm"]["round"], m["pm"]["top3"], m["pm"]["bot2"])
```

**Google Apps Script (ส่งผลเข้า Google Sheets)**

```js
function pullResults() {
  const d = JSON.parse(UrlFetchApp.fetch('https://huaywidget.com/api/all').getContentText());
  const sh = SpreadsheetApp.getActiveSheet();
  d.markets.forEach(m => sh.appendRow([new Date(), m.name, m.pm.round, m.pm.top3, m.pm.bot2]));
}
```

## ป้ายผลสำเร็จรูป (Widget)

วางโค้ดนี้ในเว็บ ป้ายจะขึ้นผลงวดล่าสุดและเปลี่ยนเองทุกงวด:

```html
<div class="hk-widget" data-m="dowjones"><a href="https://dowjonesth.com/">ผลดาวโจนส์วันนี้</a></div>
<script src="https://huaywidget.com/w.js" async></script>
```

เปลี่ยน `data-m` เป็น `nikkei` `hangseng` `china` `taiwan` `singapore` ได้ · วางหลายป้ายในหน้าเดียว ใส่ `<script>` ครั้งเดียวพอ

วิธีติดทีละขั้น: [WordPress](https://huaywidget.com/wordpress) · [Blogger](https://huaywidget.com/blogger)

## ผลคำนวณอย่างไร

ใช้สูตรมาตรฐานหวยหุ้นจากดัชนีตลาดหุ้นจริง:

- **3 ตัวบน** = หลักหน่วย + ทศนิยม 2 ตำแหน่งของดัชนีปิด
- **2 ตัวล่าง** = ทศนิยม 2 ตำแหน่งของค่าเปลี่ยนแปลงจากวันก่อน
- รอบเช้า = ค่าดัชนีตอนพักเที่ยงของตลาดนั้น
- หุ้นจีนใช้ดัชนี **เซินเจิ้น (SZSE Component, 399001)** ไม่ใช่เซี่ยงไฮ้ — ตรงกับที่เว็บหวยส่วนใหญ่ใช้

ผลย้อนหลังทุกงวด: [ดาวโจนส์](https://dowjonesth.com/yonlang) · [นิเคอิ](https://nikkeith.com/yonlang) · [ฮั่งเส็ง](https://hangsength.com/yonlang) · [หุ้นจีน](https://chinahoon.com/yonlang) · [ไต้หวัน](https://taiwanhoon.com/yonlang) · [สิงคโปร์](https://singaporehoon.com/yonlang)

## เงื่อนไขการใช้

ใช้ฟรีทั้งส่วนตัวและเชิงพาณิชย์ ขอเพียง:

1. แสดง "ข้อมูลจาก" พร้อมลิงก์ไปเว็บผลของตลาดนั้น (ค่า `site` ในข้อมูล)
2. แคชฝั่งคุณอย่างน้อย 1 นาที อย่ายิงถี่เกินจำเป็น

ข้อมูลให้ "ตามสภาพ" เพื่อการอ้างอิง โปรดตรวจสอบกับแหล่งที่คุณใช้อีกครั้ง

---

**English:** Free JSON API and embeddable widget for Thai "stock lottery" results (Dow Jones, Nikkei, Hang Seng, Shenzhen Component, TAIEX, Straits Times) computed from real index closes. No sign-up, no key, CORS enabled. Docs: https://huaywidget.com/api
