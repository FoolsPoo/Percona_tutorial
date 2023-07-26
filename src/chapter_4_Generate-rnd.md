# 04-Generate_random_terms
ปกปิดข้อมูลด้วยการ สุ่มข้อมูลจากชุดคำสั่งแบบสุ่ม เพื่อใช้ในการปกปิดข้อมุลจริง

## Table content

- [Generate rnd](#04-generate_random_terms)
    - [Prepare](#เตรียมตัวก่อนทำการ-data-masking)
    - [Generate Type](#generate)
    - [Generate Range](#generate-range)
    - [Generate Email](#generate-email)
    - [Generate Pan](#generate-pan)
    - [Generate Phone](#generate-phone)
    - [Generate SSN](#generate-ssn)
    
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

## Generate
โดยการปกปิดตัวแบบ Generate จะมีอยู่ทั้งหมด 5 ตัว

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

ทดลองใช้ Gen range

``````markdown

``````
---
## Generate email
สร้างที่อยู่อีเมลแบบสุ่ม โดยมีโดเมนคือ example.com

|Generate Type|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|-------------------------|----|---------------|
|gen_rend_email|gen_rnd_email() |SELECT gen_rnd_email();|(อีเมล)@example.com| 

ทดลองใช้ Gen email

---
## Generate pan
สร้างหมายเลขบัญชีแบบสุ่ม ควรใช้ฟังก์ชันนี้เพื่อจุดประสงค์ในการทดสอบเท่านั้น

|Generate Type|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|-------------------------|----|---------------|
|gen_pan| SELECT gen_rnd_pan([size in integer])|SELECT gen_rnd_pan();|ตัวเลขแบบสุ่มตั้งแต่ 0 จนถึง 9 ทั้งหมด 15 จำนวน| 

ทดลองใช้ Generate pan

---
## Generate phone
สร้างหมายเลขโทรศัพท์ของสหรัฐอเมริกาแบบสุ่ม หมายเลขที่สร้างขึ้นจะเพิ่มรหัสโทรออก 1 และอยู่ในรหัสพื้นที่ 555 รหัสพื้นที่ 555 ใช้ไม่ได้กับหมายเลขโทรศัพท์ใดๆ ในสหรัฐอเมริกา

|Masking Type|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|-------------------------|----|---------------|
|gen_rnd_us_phone|gen_rnd_us_phone()|SELECT gen_rnd_us_phone();|เบอร์โทรในฟอร์แมท อเมริกา (1-555-xxx-xxxx)| 

 ทดลองใช้ Generate phone
``````markdown

``````

---
## Generate SSN
สร้างหมายเลขประกันสังคมของสหรัฐอเมริกาแบบสุ่มและไม่ถูกต้องในรูปแบบ AAA-BBB-CCCC ควรใช้ฟังก์ชันนี้เพื่อจุดประสงค์ในการทดสอบเท่านั้น

|Masking Type|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|-------------------------|----|---------------|
|gen_rnd_ssn|gen_rnd_ssn()|SELECT gen_rnd_ssn();|เลขประกันสังคมของ อเมริกา (xxx-xx-xxxx)| 

 ทดลองใช้ Generate SSN
``````markdown

``````