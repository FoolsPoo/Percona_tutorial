# 03-Generate_Dictionary
ปกปิดข้อมูลด้วยการ สุ่มข้อมูลจากชุดคำสั่งแบบสุ่ม โดยนำข้อมููลมาจาก Dictionary ที่เราสร้างไว้ เพื่อใช้ในการปกปิดข้อมูลจริง

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
## Logic
โดยการสุ่มด้วยวิธีนี้ใช้วิธีการสุ่มทั้งหมดจากความน่าจะเป็นในทุกๆรอบ กล่าวคือ หากมีข้อมูลทั้งหมด 100 ตัว เราจะต้องทำการสุ่ม 1 ใน 100 เสมอ ไม่ใช่ 1 ใน 100 แล้วไป 1 ใน 99 ทำให้ข้อมูลที่ออกมานั้นสามารถเกิดซ้ำได้ติดๆกัน การเพิ่ม ข้อมูลลงไปใน Dictionary จึงเป็นสิ่งสำคัญเพื่อทำให้การสุ่มนั้น เฉลี่ยและเกิดซ้ำได้ยากขึ้น 

## Generate Dictionary
โดยการปกปิดตัวแบบ Generate Dictionary จะมีอยู่ทั้งหมด 2 วิธี โดยจะมีวิธีการนำเข้า และ ลบ เพิ่มขึ้นมา

|Masking Data  | Parameter |
|--------------|-------------|
|gen_blacklist| gen_blacklist(str, dictionary_name, replacement_dictionary_name) |
|gen_dictionary| gen_dictionary(dictionary_name) |
|gen_dictionary_drop| gen_dictionary_drop(dictionary_name) |
|gen_dictionary_load| gen_dictionary_load(path, dictionary_name)|


---
## Generate Blacklist
แทนที่ข้อมูล ด้วย ข้อมูล จาก Dictionary อีกชื่อนึง

|Masking Type|terms ที่อยู่ใน dictionary|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|----------------------|--------------------------|----|---------------|
|gen_blacklist|fruit = { "apple"}, nut = {"wallnut"}|gen_blacklist(str, dictionary_name, replacement_dictionary_name)|select gen_blacklist('apple', 'fruit', 'nut');|wallnut|

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