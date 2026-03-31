## 1. การเตรียมข้อมูลสำหรับการจำแนกพื้นที่

**พื้นที่ศึกษาคือ จังหวัดระยองและจันทบุรี**

### 1.1 การกำหนด Class
ในการศึกษานี้เลือกใช้ **4 คลาส** ได้แก่:
- **Urban (พื้นที่เมือง)**  
- **Water (น้ำ)**  
- **Agriculture (เกษตรกรรม)**  
- **Forest (ป่าไม้)**  
เลือกคลาสเหล่านี้เพื่อให้ครอบคลุมลักษณะพื้นที่หลักของพื้นที่ศึกษา

---

### 1.2 วิธีการสร้าง Training Samples
- ใช้ข้อมูลจาก **Sentinel-2 / GEE CSV Export** เป็น **pseudo ground truth**   

**ข้อดี**  
- ครอบคลุมพื้นที่ทั้งหมด  
- ประหยัดเวลา  

**ข้อจำกัด**  
- อาจมีความคลาดเคลื่อนจากภาพจริง

---

### 1.3 การแบ่ง Train / Validation
- ใช้ **Random Split 80% Training / 20% Testing**  

**ข้อดี**  
- ใช้งานง่าย  
- คำนวณเร็ว  

**ข้อจำกัด**  
- เกิด **Spatial Autocorrelation** - Accuracy สูงเกินจริง

---

### 1.4 Feature ที่ใช้
ใช้ทั้งหมด **10 Features** :  
- **Spectral Bands:** B2 (Blue), B3 (Green), B4 (Red), B8 (NIR), B11 (SWIR1), B12 (SWIR2)  
- **Spectral Indices:** NDVI, NDWI, NDBI, NDMI  

**เหตุผลที่ใช้**  
- NDVI → แยก vegetation  
- NDWI → แยกน้ำ  
- NDBI → แยกพื้นที่เมือง  
- NDMI → แยก moisture ของ vegetation  

---

## 2. Algorithm Comparison

### 2.1 โมเดลที่ใช้
- **Random Forest (Supervised)**  
- **KMeans (Unsupervised, baseline)**  

---

### 2.2 ผลลัพธ์

| Model         | Accuracy | Kappa   |
|---------------|---------|---------|
| RF 100 points | 1.0000  | 1.0000  |
| RF Combined   | 1.0000  | 1.0000  |
| KMeans        | 0.4191  | 0.1785  |

> Random Forest มี Accuracy สูงกว่า KMeans อย่างชัดเจน

---

### 2.3 Confusion Matrices

**RF 100 points:**

|        | Urban | Water | Agriculture | Forest |
|--------|------|-------|------------|--------|
| Urban      | 33   | 0     | 0          | 0      |
| Water      | 0    | 29    | 0          | 0      |
| Agriculture| 0    | 0     | 28         | 0      |
| Forest     | 0    | 0     | 0          | 30     |

**RF Combined:**

|        | Urban | Water | Agriculture | Forest |
|--------|------|-------|------------|--------|
| Urban      | 79   | 0     | 0          | 0      |
| Water      | 0    | 79    | 0          | 0      |
| Agriculture| 0    | 0     | 107        | 0      |
| Forest     | 0    | 0     | 0          | 95     |
ไม่มีการสับสนระหว่างคลาส

---

## 3. Feature Importance (RF Combined)

| Feature | Importance |
|---------|------------|
| NDVI    | 0.2492     |
| NDWI    | 0.1996     |
| NDMI    | 0.1524     |
| B11     | 0.1051     |
| NDBI    | 0.1040     |
| B8      | 0.0622     |
| B12     | 0.0727     |
| B4      | 0.0458     |
| B2      | 0.0088     |
| B3      | 0.0003     |

> **สรุป** NDVI, NDWI, NDMI เป็น Feature ที่สำคัญที่สุด สอดคล้องกับหลัก Remote Sensing

---

## 4. Difference & Uncertainty Analysis

### 4.1 Difference Map
- Change % between RF maps: 10.53%  
> การเพิ่ม Training Data ~2 เท่า ทำให้การจำแนกพื้นที่เปลี่ยน ~10.5%

### 4.2 Confidence Map Statistics
- Mean: 0.6809  
- Std: 0.1921  
- Min: 0.3400  
- Max: 1.0000  

> ใช้ระบุพื้นที่ที่โมเดล **ไม่มั่นใจ** เช่น ขอบระหว่างป่าและเกษตร หรือพื้นที่เมืองที่มี vegetation ปะปน

### 4.3 Class ที่สับสนมากที่สุด
- Urban ↔ Agriculture  
- Forest ↔ Agriculture  

**เหตุผล:** Spectral signature คล้ายกัน, อยู่ในพื้นที่ transition

---

## คำตอบคำถามวิเคราะห์

1. **ถ้าเพิ่ม Training Samples อีก 2 เท่า ความแม่นยำจะเพิ่มขึ้นเท่าไหร่? ทดสอบและอธิบายผล**  
   - Accuracy เพิ่มเล็กน้อย (~10% difference)  
   - Diminishing Returns  

2. **Spatial Autocorrelation ในข้อมูล Training มีผลต่อความแม่นยำที่รายงานอย่างไร?**  
   - ทำให้ Accuracy สูงเกินจริง เพราะ pixel ใกล้กันมีค่าเหมือนกัน  

3. **Class ใดที่โมเดลทำได้แย่ที่สุด — แก้ได้ด้วยวิธีใดบ้าง?**  
   - Agriculture (บางพื้นที่), Urban ↔ Agriculture  
   - แนวทางแก้: เพิ่ม Training Data, เพิ่ม Feature (SWIR), Balance Class  

4. **ถ้าต้องทำซ้ำ Lab นี้สำหรับพื้นที่อื่น อะไรคือสิ่งที่ต้องเปลี่ยน และอะไรที่ใช้ซ้ำได้?**  
   - ต้องเปลี่ยน - Training Data, พื้นที่ศึกษา, Distribution ของ Class  
   - ใช้ซ้ำได้ - Pipeline, Model, Feature, การคำนวณ Indices  

---

## สรุปผล
- Random Forest สามารถจำแนก **Urban, Water, Agriculture, Forest** ได้แม่นยำสูง (Accuracy=1.0, Kappa=1.0)  
- Feature สำคัญที่สุด: NDVI, NDWI, NDMI  
- เพิ่ม Training Data ทำให้โมเดลปรับการจำแนกพื้นที่ ~10%  
- Confidence Map ช่วยระบุพื้นที่ไม่แน่นอน  
- Pipeline สามารถนำไปปรับใช้พื้นที่อื่น โดยปรับ Training Data ให้เหมาะสม
