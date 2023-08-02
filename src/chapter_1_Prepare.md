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

OUTFILE Command

เป็นคำสั่งที่เอาไว้ใช้ส่งออกข้อมูลในตารางของเราใน mysql 

คำสั่งการนำเข้าไฟล์ข้อมูลจะเป็นแบบนี้

```bash
SELECT (column title) FROM (table_name)
INTO OUTFILE '(destination path/filename.txt)';
```

(column title) คือหัวข้อข้อมูลในแต่ละคอลัมน์ สามารถเลือกได้ว่าจะส่งออกข้อมูลคอลัมน์ไหนบ้าง

ส่วน '(table name)' คือ ชื่อของตารางที่เราจะส่งออกข้อมูล

ส่วน ‘(destination path/filename.txt)' คือ ตำแหน่งบันทึกไฟล์ข้อมูลที่จะส่งออก โดยเราสามารถเลือกตำแหน่งบันทึกได้เลยพร้อมตั้งชื่อและกำหนดนามสกุลไฟล์ 
ยกตัวอย่าง ‘/var/lib/mysql-files/newer.txt’

เราสามารถกำหนดได้ว่าจะวางคั่นข้อมูลแบบไหนได้ โดยหลักๆจะมี 3 คำสั่ง

FIELDS TERMINATED BY ‘ ' = การวางคั่นข้อมูลระหว่างคอลัมน์
ENCLOSED BY ' ' = การวางปิดข้อมูล
LINES TERMINATED BY '’ = การเข้าบรรทัดใหม่

และนี่คือตัวอย่างการใช้คำสั่ง OUTFILE

```bash
SELECT * FROM news 
INTO OUTFILE '/var/lib/mysql-files/newer.txt' 
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\r\n';
```

หลังจากที่ export เสร็จเรียบร้อยแล้ว เราสามารถออก mysql กลับเข้าไปในตัว container เพื่อหาไฟล์ได้ว่ามีอยู่หรือไม่ ถ้ากรณีที่เราอยากจะเอาไฟล์ออกมาข้างนอกตัว container เราสามารถทำได้โดยมีวิธีดังนี้

กรณีของ Windows เราสามารถเข้าโปรแกรม docker desktop เข้าไปที่ตัว container ของ percona จะเห็นหน้า files อยู่ ให้เราหาไฟล์และลากไฟล์ออกมาลงหน้า desktop หรือตำแหน่งไฟล์อื่นๆได้เลย

แต่กรณีของ Mac จะไม่มีหน้า files ให้เห็น เราจะใช้คำสั่ง docker cp ลากไฟล์ออกมาแทน





รูปแบบคำสั่ง docker ps กรณีส่งออกไฟล์จะเป็นแบบนี้

```bash
docker cp (container-id):(file path/filename) (destination path)
```

(container-id) คือ ไอดีของตัว container ที่มีไฟล์ที่เราต้องการส่งออก
(file path/filename) คือ ตำแหน่งของไฟล์ที่จะนำส่งออกพร้อมกับชื่อไฟล์และนามสกุล
(destination path) คือ ตำแหน่งไฟล์ที่เราจะเก็บไฟล์ที่ส่งออกมาไว้

ยกตัวอย่างการใช้คำสั่งก็ตามนี้เลย

```bash
docker cp c832e6d2ef9d:/var/lib/mysql-files/newer.txt /Users/captain/Desktop
```

เท่านี้เราก็ส่งออกไฟล์ได้สำเร็จแล้ว สามารถกดเข้าดูไฟล์เช็คได้เลยว่าตรงหรือไม่



INFILE Command

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
วิธีนำเข้าโดยใช้ผ่านระบบ window
