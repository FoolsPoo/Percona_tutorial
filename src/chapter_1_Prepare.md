# Prepare

จะประกอบไปด้วยการเตรียมข้อมูลเพื่อใช้ในการทดสอบเบื้องต้น

## Course Overview

- [Percona Data Masking Tutorial](#percona-data-masking-tutorial)
    - [Concept](#concept)
    - [Data Masking Technique](#data-masking-technique)
    - [Docker](#docker)
---

## Concept

เตรียมตัวก่อนทำการฝึก Data Masking โดยจะฝึกการ นำเข้าไฟล์จกาในเครื่องเข้ามาใน Docker ก่อน

---

## นำเข้าไฟล์ ด้วย mac

## INFILE Command

เป็นคำสั่งที่เอาไว้ใช้นำเข้าไฟล์ข้อมูลลงในตารางของเราใน mysql 

คำสั่งการนำเข้าไฟล์ข้อมูลจะเป็นแบบนี้

```bash
LOAD DATA INFILE '(file path)' 
INTO TABLE (table name);
```

ตรงส่วน '(file path)' คือ ตำแหน่งที่ไฟล์ข้อมูลเราอยู่ เราสามารถใส่ Path ของไฟล์ที่เราจะเอานำเข้าไปใส่ได้ 
ยกตัวอย่าง '/var/lib/mysql-files/news.sql'

ส่วน '(table name)' คือ ชื่อของตารางที่เราจะนำเข้าไฟล์เข้าไปบันทึกลงในตารางนั้น

กรณีที่ไฟล์มีการวางคั่นข้อมูลแบบไหน เราจำเป็นต้องพิมพ์คำสั่งว่าในแต่ละแบบมีการวางคั่นข้อมูลแบบไหน 
เพื่อให้ตัว sql สามารถแยกข้อมูลออก โดยมีตามนี้

FIELDS TERMINATED BY ‘ ' = การวางคั่นข้อมูลระหว่างคอลัมน์
ENCLOSED BY ' ' = การวางปิดข้อมูล
LINES TERMINATED BY '’ = การเข้าบรรทัดใหม่

และ เราสามารถใช้คำสั่ง IGNORE X ROWS เพื่อเป็นการข้ามนำเข้าข้อมูลได้ x แถวตามที่เรากำหนด

ยกตัวอย่างการใช้คำสั่งก็ตามภาพเลยครับ

```bash
LOAD DATA INFILE '/var/lib/mysql-files/news.sql' 
INTO TABLE news 
FIELDS TERMINATED BT ‘,’
ENCLOSED BY '"' LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

แน่นอนว่าถ้ากรณีเรารัน sql ผ่าน docker container เราต้องนำไฟล์เข้าไปข้างในตัว container เสียก่อน

กรณีของ Windows เราสามารถเข้าโปรแกรม docker desktop เข้าไปที่ตัว container ของ percona จะเห็นหน้า files อยู่ เราสามารถลากไฟล์ที่เราต้องการเข้าไปข้างในในตำแหน่งที่ต้องการได้เลย

แต่กรณีของ Mac จะไม่มีหน้า files ให้เห็น เราจะใช้คำสั่ง docker cp นำเข้าไฟล์ออกมาแทน

รูปแบบคำสั่ง docker ps กรณีนำเข้าไฟล์จะเป็นแบบนี้

```bash
docker cp (filename) (container-id):(destination path)
```

(filename) คือ ชื่อไฟล์และนามสกุลไฟล์ที่ต้องการนำเข้า
(container-id) คือ ไอดีของตัว container ที่มีไฟล์ที่เราต้องการส่งออก
(destination path) คือ ตำแหน่งไฟล์ที่เราจะเก็บไฟล์ที่ส่งออกมาไว้

ยกตัวอย่างการใช้คำสั่งก็ตามนี้เลย

```bash
docker cp news.sql c832e6d2ef9d:/var/lib/mysql-files
```

เท่านี้เราก็นำเข้าไฟล์ได้สำเร็จแล้ว สามารถกดเข้าดูใน container ได้ว่ามีไฟล์หรือไม่

---
 
## วิธีนำเข้าโดยใช้ผ่านระบบ window

สำหรับฝั่ง Window ให้หา path ของ secure-file-priv ก่อนว่าอยู่ที่ไหน โดยใช้คำสั่ง

``````markdown
show variables like "secure_file_priv"; 
``````
หรือ
``````markdown
SELECT @@global.secure_file_priv;
``````
เราจะได้ Path ของตัว ***secure-file-priv*** มา

และหลังจากที่เตรียม ไฟล์ text ที่เราจะนำมาใช้งานแล้ว ให้นำเอามาใส่ใน path ที่ ***secure-file-priv*** อยู่ใน Docker

และเราจะ load เข้าโดยใช้คำสั่ง

``````markdown
SELECT gen_dictionary_load('/var/lib/mysql-files/mydict','mydict');
``````

โดย **mydict** แรกคือชื่อไฟล์ที่ใส่เข้าไป และ **mydict** หลังคือชื่อ dictionary ที่เราจะตั้ง

หากขึ้น
``````markdown
+---------------------------------------------------------------+
| gen_dictionary_load('/var/lib/mysql-files/fruit.txt','fruit') |
+---------------------------------------------------------------+
| Dictionary load success                                       |
+---------------------------------------------------------------+
``````
ก็ถือว่าสำเร็จแล้ว 
