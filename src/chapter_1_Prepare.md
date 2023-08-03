# Prepare

จะประกอบไปด้วยการเตรียมข้อมูลเพื่อใช้ในการทดสอบเบื้องต้น โดยที่จะเป็นการนำไฟล์ Dictionary เข้าไปในตัว Percona เพื่อใช้ในการ Masking Dictionary คือการนำข้อมูลจากใน Dic มาใช้แทนข้อมูลจริง

## Course Overview

- [Percona Data Masking Tutorial](#percona-data-masking-tutorial)
    - [Concept](#concept)
    - [MAC](#นำเข้าไฟล์-ด้วย-mac)
    - [WINDOW](#วิธีนำเข้าโดยใช้ผ่านระบบ-window)
---

## Concept

เตรียมตัวก่อนทำการฝึก Data Masking โดยจะฝึกการ นำเข้าไฟล์จกาในเครื่องเข้ามาใน Docker ก่อน

---

## นำเข้าไฟล์ ด้วย mac

***INFILE Command***

แต่กรณีของ Mac จะไม่มีหน้า files ให้เห็นในโปรแกรม Docker เราจะใช้คำสั่ง docker cp นำเข้าไฟล์ออกมาแทน

รูปแบบคำสั่ง docker ps 

```bash
docker cp (filename) (container-id):(destination path)
```

(filename) คือ ชื่อไฟล์และนามสกุลไฟล์ที่ต้องการนำเข้า
(container-id) คือ ไอดีของตัว container ที่มีไฟล์ที่เราต้องการส่งออก
(destination path) คือ ตำแหน่งไฟล์ที่เราจะเก็บไฟล์ที่ส่งออกมาไว้

ยกตัวอย่างการใช้คำสั่ง

```bash
docker cp news.sql c832e6d2ef9d:/var/lib/mysql-files
```

สามารถกดเข้าดูใน container ได้ว่ามีไฟล์หรือไม่

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
