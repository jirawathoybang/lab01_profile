# Lab L1 · answers.md (TH)

ชื่อ-สกุล: จิรวัฒน์ หอยบาง
รหัสนักศึกษา: 67125740
ลิงก์ repo: https://github.com/jirawathoybang/lab01_profile

## Q1 · เครื่องมือ
ตอนรัน `flutter doctor` ครั้งแรก บรรทัด Android toolchain ขึ้น ✗ "Unable to locate Android SDK" เพราะเครื่องยังไม่มี Android SDK ผมแก้โดยติดตั้ง Android Studio จาก developer.android.com/studio ตามลิงก์ที่ doctor แนะนำ เลือก Setup แบบ Standard แล้วกด Accept license ทั้งสองอัน (android-sdk-license และ android-sdk-arm-dbt-license) จากนั้นติดตั้ง Android SDK Command-line Tools ใน SDK Manager และรัน `flutter doctor --android-licenses` เมื่อรัน doctor อีกครั้ง Android toolchain ขึ้น ✓ (ส่วน Xcode ยังขึ้น ! แต่ไม่ต้องใช้ เพราะ lab นี้รันบน Android)

ปัญหาที่สองเจอตอน `flutter run` ครั้งแรก build fail ที่ task `:app:mergeDebugJavaResource` ด้วย `VerifyException` ผมลอง `flutter clean` แล้วก็ยังไม่หาย สาเหตุน่าจะมาจาก locale ของเครื่องที่เป็น th-TH ทำให้ Java ใช้ปฏิทินพุทธศักราช (ปี 2569) ตอน Gradle สร้างไฟล์ zip ข้างในแอป วันที่จึงเกินช่วงที่ zip รองรับ ผมแก้ด้วยการตั้ง `export JAVA_TOOL_OPTIONS="-Duser.language=en -Duser.country=US"` แล้วรันใหม่ หลังจากนั้น build ผ่าน

## Q2 · Reload
ตอนที่ Likes เป็น 3 ผมแก้ title เป็น "Lab L1 done" แล้วกด `r` (hot reload) title เปลี่ยนเป็นข้อความใหม่ แต่ Likes ยังเป็น 3 เพราะ hot reload แค่ใส่โค้ดใหม่เข้าไปแล้วเรียก `build()` ใหม่ ส่วน State object เดิมที่เก็บ `_likes` ยังอยู่ ค่าจึงไม่หาย

พอกด `R` (hot restart) Likes กลับเป็น 0 เพราะ hot restart ทิ้ง widget tree และ State เดิมทั้งหมด แล้วรัน `main()` ใหม่ ทำให้ได้ `_HomePageState` ตัวใหม่ที่ `_likes = 0`

## Q3 · โค้ดและ Git
ถ้าเขียน `_likes++` เฉย ๆ ค่าในตัวแปรจะเพิ่ม แต่ Flutter ไม่รู้ว่ามีอะไรเปลี่ยน จึงไม่เรียก `build()` ใหม่ หน้าจอเลยยังแสดงเลขเดิม `setState` เป็นตัวบอก Flutter ว่า state เปลี่ยนแล้ว ให้ rebuild widget นี้ ข้อความ `Likes: $_likes` ถึงจะอัปเดตตาม

commit ที่เลือก: `Add like counter with setState`
commit นี้เปลี่ยน `HomePage` จาก StatelessWidget เป็น StatefulWidget แล้วเพิ่มคลาส `_HomePageState` ที่เก็บ `int _likes = 0` เพิ่มฟังก์ชัน `_like()` ที่เรียก `setState(() => _likes++)` เพิ่ม `Text('Likes: $_likes')` กับปุ่ม `FilledButton` ชื่อ Like และลบ `const` หน้า `Center` ออก เพราะข้อความ Likes เปลี่ยนค่าได้ จึงเป็น const ไม่ได้

## ตารางทดสอบ

| ประเภท | input หรือการกระทำ | ผลที่คาดหวัง | ผลจริง |
|---|---|---|---|
| Normal | เปิดแอป | title มีรหัส ไอคอน ชื่อ “Likes: 0” | เห็น “Lab L1 · 67125740” ไอคอนคน ชื่อ และ “Likes: 0” ตรงตามที่คาด |
| Normal | กด Like หนึ่งครั้ง | “Likes: 1” | ขึ้น “Likes: 1” |
| Boundary | กด Like เร็ว ๆ 20 ครั้ง | “Likes: 20” ไม่มี error | ขึ้น “Likes: 20” ไม่มี error |
| Boundary | หมุนจอเป็นแนวนอน | ยังอยู่กลางจอ ไม่ overflow | ยังอยู่กลางจอ ไม่มีแถบเหลืองดำ |
| Reload | กด 3 ครั้ง แล้ว r แล้ว R | หลัง r ได้ 3 · หลัง R ได้ 0 | หลัง r ยังเป็น 3 (title เปลี่ยน) · หลัง R กลับเป็น 0 |
| Failure | ปิด emulator แล้ว `flutter run` | “No supported devices connected.” | ขึ้น “No supported devices connected.” |

## การใช้ AI
ใช้ Claude ช่วยอธิบายขั้นตอนการติดตั้ง Android Studio และการ push ขึ้น GitHub ด้วย Personal Access Token ช่วยหาสาเหตุที่ build fail (locale ไทย) และช่วยร่างคำตอบในไฟล์นี้ ผมพิมพ์โค้ดใน main.dart เอง ตรวจคำตอบและผลทดสอบกับที่เห็นจริงบน emulator แล้ว 
