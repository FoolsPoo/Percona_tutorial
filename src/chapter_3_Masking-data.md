# 03-Basic_Data_Masking
เราสามารถปกปิดข้อมูลด้วยการใช้งาน Masking Data เพื่อทำให้ข้อมูลของลูกค้าไม่ปรากฎสู่สาธารณะ

## Table content

- [Basic Data Masking](#03-basic_data_masking)
    - [Prepare](#เตรียมตัวก่อนทำการ-data-masking)
    - [Data Masking](#data-masking)
    - [Mask inner & outer](#mask-inner--outer)
    - [Mask Pan](#mask-pan)
    - [Mask Pan Relaxed](#mask-pan-relaxed)
    - [Mask SSN](#mask-ssn)
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
## Mask inner & outer
inner ส่งกลับผลลัพธ์ที่มีการปกปิดเฉพาะส่วนในของสตริง สามารถเปลี่ยนตัวอักษรที่ใช้ในการปกปิดได้ outer ทำการปกปิดข้อมูลส่วนนอก แต่จะไม่ปกปิดข้อมูลส่วนด้านใน

|Masking Type|ข้อมูลที่ใช้กก่อนทำการ Masking|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|------------------------|-------------------------|----|---------------|
|Mask_inner|6307015589460|mask_inner('string, margin1, margin2, [character])|SELECT mask_inner('6307015589460',1,1);|6XXXXXXXXXXX0|
|Mask_outer|6307015583468|mask_outer('string, margin1, margin2, [character])|SELECT mask_outer('6307015583468',3,3);|XXX7015583XXX|

ทดลองใช้ Masking Inner & outer

``````markdown
SELECT id, 
       hushhush as 'Original', 
       MASK_INNER(convert(hushhush using binary),2,3) as 'Inner', 
       MASK_OUTER(convert(hushhush using binary),3,3) as 'Outer' 
FROM sensative_data;
``````
---
## Mask pan
ปกปิดหมายเลขบัญชี(PAN) โดยแทนที่ข้อมูลด้วย "X" ยกเว้นสี่ตัวสุดท้าย ข้อมูล PAN ต้องมีความยาว 15 ตัวอักษรหรือ 16 ตัวอักษร

|Masking Type|ข้อมูลที่ใช้กก่อนทำการ Masking|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|------------------------|-------------------------|----|---------------|
|Mask_pan|123456789012345|mask_pan('string') |SELECT mask_inner('6307015589460',1,1);|XXXXXXXXXXX2345| 

ทดลองใช้ Mask pan

---
## Mask pan relaxed
โชว์ข้อมูลแค่หกตัวแรกและตัวเลขสี่ตัวสุดท้าย สตริงที่เหลือจะถูกแทนที่ด้วย "X"

|Masking Type|ข้อมูลที่ใช้กก่อนทำการ Masking|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|mask_pan_relaxed|123456789012345|mask_pan_relaxed('string') |SELECT mask_pan_relaxed('123456789012345');|123456XXXXX2345|

ทดลองใช้ Masking pan relaxed

---
## Mask SSN
โชว์ข้อมูลที่มองเห็นเฉพาะตัวเลขสี่ตัวสุดท้าย ข้อมูลที่เหลือจะถูกแทนที่ด้วย "X"

|Masking Type|ข้อมูลที่ใช้กก่อนทำการ Masking|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|------------------------|-------------------------|----|---------------|
|mask_ssn|555-55-5555|mask_ssn('string')|SELECT mask_ssn('555-55-5555');|XXX-XX-5555| 


``````markdown

``````