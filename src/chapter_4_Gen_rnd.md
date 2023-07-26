# 03-Generate_random_terms
ปกปิดข้อมูลด้วยการ สุ่มข้อมูลจากชุดคำสั่งแบบสุ่ม เพื่อใช้ในการปกปิดข้อมุลจริง

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
|gen_range| gen_range(lower, upper) |
|gen_rnd_email| gen_rnd_email() |
|gen_rnd_pan| gen_rnd_pan([size in integer])|
|gen_rnd_us_phone| gen_rnd_us_phone() |
|gen_rnd_ssn| gen_rnd_ssn() |

---
## Generate range
สร้างตัวเลขแบบสุ่มตามช่วงที่เลือกและรองรับตัวเลขที่ติดลบ

|Generate Type|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|-------------------------|----|---------------|
|gen_range|gen_range(lower, upper)|SELECT gen_range(10, 100);|เลขสุ่มระหว่าง 10 -100|

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
|------------|------------------------|-------------------------|----|---------------|
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