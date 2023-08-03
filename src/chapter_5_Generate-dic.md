# 05-Generate_Dictionary
ปกปิดข้อมูลด้วยการ สุ่มข้อมูลจากชุดคำสั่งแบบสุ่ม โดยนำข้อมููลมาจาก Dictionary ที่เราสร้างไว้ เพื่อใช้ในการปกปิดข้อมูลจริง

## Table content


    
## เตรียมตัวก่อนทำการ Data Masking

เตรียม ไฟล์ text fruit และ nut ที่ในไฟล์มี terms พวกนี้

fruit
```bash

apple
berry
pieapple

```

nut
```bash

wallnut
peanut
almond

```
## Logic
โดยการสุ่มด้วยวิธีนี้ใช้วิธีการสุ่มทั้งหมดจากความน่าจะเป็นในทุกๆรอบ กล่าวคือ หากมีข้อมูลทั้งหมด 100 ตัว เราจะต้องทำการสุ่ม 1 ใน 100 เสมอ ไม่ใช่ 1 ใน 100 แล้วไป 1 ใน 99 ทำให้ข้อมูลที่ออกมานั้นสามารถเกิดซ้ำได้ติดๆกัน การเพิ่ม ข้อมูลลงไปใน Dictionary จึงเป็นสิ่งสำคัญเพื่อทำให้การสุ่มนั้น เฉลี่ยและเกิดซ้ำได้ยากขึ้น 

---

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

ทดลองใช้ Generate Blacklist

``````markdown
SELECT gen_blacklist('apple', 'fruit', 'nut');
``````
โดย apple คือ str ที่อยู่ใน fruit โดยจะถูกแทนที่ด้วย nut แบบสุ่ม

---
## Generate Dictionary
สุ่มข้อมูล ที่อยู่ในใน Dictionary มาใช้

|Masking Type|terms ที่อยู่ใน dictionary|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|----------------------|--------------------------|----|---------------|
|gen_dictionary|name = {"mark"}|gen_dictionary(dictionary_name)|select gen_dictionary('name');|mark|

ทดลองใช้ Generate Dictionary

---
## Generate Dictionary Drop
โชว์ข้อมูลแค่หกตัวแรกและตัวเลขสี่ตัวสุดท้าย สตริงที่เหลือจะถูกแทนที่ด้วย "X"

|Masking Type|ข้อมูลที่ใช้กก่อนทำการ Masking|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|------------------------|-------------------------|----|---------------|
|gen_dicotionary_drop|test = {"test"}|gen_dictionary_drop(dictionary_name)|select gen_dictionary_drop('test');|Dictionary removed| 

ทดลองใช้ Generate Dictionary Drop

---
## Generate Dictionary Load
โชว์ข้อมูลที่มองเห็นเฉพาะตัวเลขสี่ตัวสุดท้าย ข้อมูลที่เหลือจะถูกแทนที่ด้วย "X"

|Masking Type|ข้อมูลที่ใช้กก่อนทำการ Masking|Parameter ที่ใช้ทำการ Masking|Code|Expected Result|
|------------|------------------------|-------------------------|----|---------------|
|gen_dictionary_load|test = {"test"}|gen_dictionary_load(path, dictionary_name)|select gen_dictionary_load ('var/lib/mysql-files/test.txt', 'test');|Dictionary| 

ทดลองใช้ Generate Dictionary Load

``````markdown

``````
