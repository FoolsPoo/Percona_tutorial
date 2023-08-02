# 06-Thailans_SSN

การเปลี่ยนเลขประจำตัวบัตรประชาชน เพื่อปกปิดเลขประจำตัวเดิมเพื่อเพิ่มความเป็นส่วนตัวกับผู้ใช้งาน 

## Table content


    
## การทำงานของ การสุ่ม บัตรประจำตัวประชาชนประเทศไทย

 Percona นั้นไม่มีระบบรองรับสิ่งที่เราทำได้คือการใช้ Load Dictionary แต่สิ่งที่เราต้องการคือ เลขประจำตัวบัตรประชาชนที่ถ้าเช็คแล้วยังคงถูกต้องอยู่ โดยเลขบัตรประชาชนนั้นมีสูตรที่ใช้คิดหาอยู่ 
**** เพิ่มข้อมูลจากใน Doc

แต่หากต้องการข้อมูลจำนวนเยอะๆ การสุ่มจากในเว็บคงไม่ใช่ทางออกหากต้องการข้อมูลตัวอย่างประมาณ 10,000 ตัวขึ้นไป เราจึงสร้าง คำสั่งเพื่อสุ่มเลขประจำตัวประชาชนขึ้นมา ที่สามารถใส่จำนวนที่ต้องการได้
และให้ทำลงใน ไฟล์ text เพื่อนำมาใช้งานได้ทันทีขึ้นมา

## Thai SSN randomizer

```bash
import random

def generate_random_thai_citizen_id():
   thai_citizen_id = []


   # The first digit should be between 1 and 8.
   thai_citizen_id.append(str(random.randint(1, 8)))


   # The following 11 digits can be any number between 0 and 9.
   for _ in range(11):
       thai_citizen_id.append(str(random.randint(0, 9)))


   # The last digit is a checksum calculated based on the previous 12 digits.
   last_digit = calculate_checksum(thai_citizen_id)
   thai_citizen_id.append(str(last_digit))


   return "".join(thai_citizen_id)


def calculate_checksum(id_digits):
   total = sum(int(id_digits[i]) * (13 - i) for i in range(len(id_digits)))
   checksum = (11 - (total % 11)) % 10
   return checksum

if __name__ == "__main__":
   # Generate 5 random Thai Citizen ID-like numbers
   generated_ids = []
   for _ in range(100000):
       thai_id = generate_random_thai_citizen_id()
       generated_ids.append(thai_id)

   # Save the generated IDs to a text file
   with open("/Users/captain/Desktop/faketest/output.txt", "w") as file:
       for thai_id in generated_ids:
           file.write(thai_id + "\n")
```

โดย คำสั่งนี้จำประกอบไปด้วย การสุ่ม SSN ของไทย และ จบท้ายด้วยการเขียนลงไปใน Text ไฟล์ เพื่อให้พร้อมต่อการนำมาใช้กับ Dictionary ของ Percona 

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

``````
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