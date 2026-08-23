---
title: "ตัวอย่าง: ลดเวลา Google Apps Script ด้วย Batch Operations"
date: 2026-08-23
type: note
status: reviewed
tags: [google-apps-script, spreadsheet, performance]
---

# ตัวอย่าง — ลดเวลา Google Apps Script ด้วย Batch Operations

> ไฟล์นี้เป็นข้อมูลตัวอย่างสำหรับดูรูปแบบการเก็บความรู้พร้อมโค้ด

## สรุปสั้น ๆ

เมื่อต้องประมวลผลหลายแถว ควรอ่านข้อมูลจาก Google Sheet ครั้งเดียวเป็น Array ประมวลผลในหน่วยความจำ แล้วเขียนผลลัพธ์กลับครั้งเดียว วิธีนี้ช่วยลดจำนวนครั้งที่สคริปต์ติดต่อกับบริการ Spreadsheet

## สิ่งที่ควรหลีกเลี่ยง

```javascript
for (let row = 2; row <= sheet.getLastRow(); row++) {
  const amount = sheet.getRange(row, 2).getValue();
  sheet.getRange(row, 3).setValue(amount > 0 ? 'พร้อม' : 'ตรวจสอบ');
}
```

โค้ดนี้อ่านและเขียนชีตทุกครั้งที่วนลูป

## แนวทาง Batch Operation

```javascript
function updateStatuses() {
  const sheet = SpreadsheetApp.getActive()
    .getSheetByName('Sales');

  const lastRow = sheet.getLastRow();
  if (lastRow < 2) return;

  const amounts = sheet
    .getRange(2, 2, lastRow - 1, 1)
    .getValues();

  const statuses = amounts.map(([amount]) => [
    Number(amount) > 0 ? 'พร้อม' : 'ตรวจสอบ',
  ]);

  sheet
    .getRange(2, 3, statuses.length, 1)
    .setValues(statuses);
}
```

## หลักที่นำไปใช้ต่อได้

- อ่านข้อมูลเป็นชุดด้วย `getValues()`
- ประมวลผลด้วย Array เช่น `map()`, `filter()` หรือ `reduce()`
- เขียนผลลัพธ์กลับเป็นชุดด้วย `setValues()`
- ตรวจให้จำนวนแถวและคอลัมน์ของ Array ตรงกับ Range
- วัดเวลาทำงานก่อนและหลังแก้ ไม่ใช้ความรู้สึกตัดสิน

## สิ่งที่ต้องทดลองต่อ

- [ ] เปรียบเทียบเวลาเมื่อมี 100, 1,000 และ 10,000 แถว
- [ ] ทดลองใช้ `console.time()` และ `console.timeEnd()`
- [ ] ศึกษาการใช้ Cache สำหรับข้อมูลที่อ่านซ้ำ

## เชื่อมโยง

- [ตัวอย่างเรื่องที่เริ่มจาก Inbox](../00-inbox/EXAMPLE-2026-08-23-topic-to-study.md)
