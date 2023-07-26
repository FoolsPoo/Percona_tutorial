# 03-Basic_Data_Masking
เราสามารถปกปิดข้อมูลด้วยการใช้งาน Masking Data เพื่อทำให้ข้อมูลของลูกค้าไม่ปรากฎสู่สาธารณะ

## Table content

## เตรียมตัวก่อนทำการ Data Masking

เตรียมตัวก่อนทำการ Data Masking
``````markdown
INSTALL PLUGIN data_masking SONAME 'data_masking.so';
``````

สร้าง ตารางข้อมูล
``````markdown
create table sensative_data (id int, hushhush bigint);
``````

Insert ข้อมูลลงไปในตาราง
``````markdown
insert into sensative_data values (1,1234567890),(2,0987654321);
``````

## Data Masking
โดยการปกปิดตัวแบบ Data anomalyzation หรือ Masking out จะมีหลักๆอยู่ 5 ตัว

|Masking Data  | Parameter |
|--------------|-------------|
|Mask_inner| mask_inner('string, margin1, margin2, [character]) |
|Mask_outer| mask_outer('string, margin1, margin2, [character]) |
|Mask_pan| mask_pan('string') |
|Mask_pan_relaxed| mask_pan_relaxed('string') |
|Mask_ssn| mask_ssn('string') |

---
## Mask inner
ส่งกลับผลลัพธ์ที่มีการปกปิดเฉพาะส่วนในของสตริง สามารถเปลี่ยนตัวอักษรที่ใช้ในการปกปิดได้
|Masking Type|ข้อมูลที่ใช้กก่อนทำการ Masking|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|-----------------------|---------------|
|Mask_inner|6307015589460|mask_inner('string, margin1, margin2, [character])|SELECT mask_inner('6307015589460',1,1);|6XXXXXXXXXXX0|








``````markdown

``````