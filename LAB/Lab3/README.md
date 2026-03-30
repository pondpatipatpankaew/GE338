# 1. การเตรียมข้อมูลสำหรับการจำแนกพื้นที่

**พื้นที่ศึกษาคือ บริเวณจังหวัดระยองและจันทบุรี**

## 1.1 การกำหนด Class
ในการศึกษานี้เลือกใช้ **5 คลาส** ได้แก่:
- **Water (น้ำ)**  
- **Trees (ป่าไม้)**  
- **Crops (เกษตรกรรม)**  
- **Built-up (พื้นที่เมือง)**  
- **Bare land (พื้นที่ดินเปล่า)**  
เลือกคลาสเหล่านี้เพื่อให้ครอบคลุมลักษณะพื้นที่หลักในพื้นที่ศึกษา

---

## 1.2 วิธีการสร้าง Training Samples
ใช้ข้อมูลจาก **Dynamic World** เป็นแหล่งอ้างอิง (Reference Dataset)  
- ใช้เป็น **pseudo ground truth**  
- **ไม่ต้องลงพื้นที่จริง**  

**ข้อดี :**  
- ครอบคลุมพื้นที่ทั้งหมด  
- ประหยัดเวลา  

**ข้อจำกัด :**  
- อาจมีความคลาดเคลื่อนจากโมเดลต้นทาง

---

## 1.3 การแบ่ง Train / Validation
**วิธีที่ใช้:** Random Split (80% Training / 20% Testing)  

**ข้อดี :**  
- ใช้งานง่าย  
- คำนวณเร็ว  

**ข้อจำกัด :**  
- เกิด Spatial Autocorrelation → ทำให้ accuracy สูงเกินจริง

---

## 1.4 Feature ที่ใช้
ใช้ทั้งหมด **6 Features** :  
- **Spectral Bands:** Blue, Green, Red, NIR  
- **Spectral Indices:** NDVI, NDWI  

**เหตุผล :**  
- NDVI ใช้แยก แยกพืช  
- NDWI ใช้แยก แยกน้ำ  
- NIR  สำคัญในการแยก vegetation

---

# 2. Algorithm Comparison

## 2.1 โมเดลที่ใช้
- **Random Forest (Supervised)**  
- **KMeans (Unsupervised)**  

## 2.2 ผลลัพธ์

| Model         | Accuracy | Kappa   |
|---------------|---------|---------|
| Random Forest | 0.8257  | 0.6349  |
| KMeans        | 0.4191  | 0.1785  |

## 2.3 การวิเคราะห์
**Random Forest**  
- Accuracy สูง (82.57%)  
- จำแนก Water และ Trees ได้ดี  

**KMeans**  
- Accuracy ต่ำ (41.91%)  
- ไม่สามารถแยก class จริงได้  

**สรุป**  
Random Forest มีประสิทธิภาพสูงกว่าอย่างชัดเจน เนื่องจากใช้ข้อมูล label ในการเรียนรู้

---

## 2.4 การเปรียบเทียบ Feature
- ใช้เฉพาะ Bands - Accuracy ต่ำ  
- ใช้ Bands + NDVI/NDWI -  Accuracy สูงขึ้น  
ทำให้ Spectral Indices ช่วยเพิ่มความแม่นยำอย่างมีนัยสำคัญ

---

# 3. Feature Importance

## 3.1 Feature สำคัญ
- **NDVI**  
- **NIR**  
- **NDWI**  

## 3.2 การตีความเชิงกายภาพ
- NDVI สูง - พืชหนาแน่น  
- NDWI สูง - น้ำ  
- NIR - vegetation reflectance สูง  

สอดคล้องกับความเป็นจริง

## 3.3 ผลของการตัด Feature
- ไม่มี NDVI → Accuracy ลด  
- ไม่มี NDWI → จำแนกน้ำแย่ลง  

---

# 4. Uncertainty Analysis

## 4.1 Class ที่สับสนมากที่สุด
- Trees ↔ Crops  
- Built-up ↔ Bare land  

**สาเหตุ:**  
- Spectral signature คล้ายกัน  
- อยู่ในพื้นที่ transition

## 4.2 พื้นที่ที่โมเดลไม่แน่ใจ  
- ขอบระหว่างป่าและเกษตร  
- พื้นที่เมืองที่มี vegetation ปะปน

## 4.3 การอธิบายให้ Stakeholder
- โมเดลมีความแม่นยำสูงในพื้นที่ที่มีลักษณะชัดเจน เช่น แหล่งน้ำและป่าทึบ  
- แต่มีความไม่แน่นอนในพื้นที่ที่มีการใช้ที่ดินแบบผสม

---

# คำถามวิเคราะห์

1. **ถ้าเพิ่ม Training Samples อีก 2 เท่า ความแม่นยำจะเพิ่มขึ้นเท่าไหร่? ทดสอบและอธิบายผล**  
   - Accuracy เพิ่มเล็กน้อย จากผลเดิม

2. **Spatial Autocorrelation ในข้อมูล Training มีผลต่อความแม่นยำที่รายงานอย่างไร?**  
   - ทำให้ Accuracy สูงเกินจริง  
   - เพราะ pixel ใกล้กันมีค่าเหมือนกัน

3. **Class ใดที่โมเดลทำได้แย่ที่สุด — แก้ได้ด้วยวิธีใดบ้าง?**  
   - Crops  
   - Bare land  

**แนวทางแก้ :**  
- เพิ่ม Training Data  
- เพิ่ม Feature (เช่น SWIR)  
- Balance Class

4. **ถ้าต้องทำซ้ำ Lab นี้สำหรับพื้นที่อื่น อะไรคือสิ่งที่ต้องเปลี่ยน และอะไรที่ใช้ซ้ำได้?**  
- ต้องเปลี่ยน Training Data, พื้นที่ศึกษา, Distribution ของ Class  
- อันที่ใช้ซ้ำได้ Pipeline, Model, Feature
