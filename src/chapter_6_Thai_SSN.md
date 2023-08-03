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

