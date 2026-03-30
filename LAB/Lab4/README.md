# Urban Heat Stress Risk in Chonburi

---

## 1. Research Question
**คำถามวิจัย**  พื้นที่เมืองใดในจังหวัดชลบุรีมีความเสี่ยงต่อ **Urban Heat Stress** สูงที่สุด?  

เพื่อสร้าง **แผนที่ความเสี่ยงเชิงพื้นที่ (Heat Risk Map)** เพื่อสนับสนุนการวางแผนพื้นที่เมืองและลดผลกระทบต่อประชากร

---

## 2. Factors and Rationale

| Factor | Scientific Rationale | Source / Reference |
|--------|-------------------|-----------------|
| LST (Land Surface Temperature) | อุณหภูมิพื้นผิวโดยตรงเป็นตัวชี้วัดความร้อนในเมือง | MODIS LST, Urban Heat Island studies |
| NDVI (Normalized Difference Vegetation Index) | พื้นที่มีพืชสูง → ลดความร้อนด้วยการระเหยน้ำ | Sentinel-2, Vegetation cooling effect |
| NDBI (Normalized Difference Built-up Index) | พื้นที่อาคาร/ปูน → เก็บความร้อนสูง | Sentinel-2, Urbanization studies |
| Nighttime Light (NTL) | บ่งชี้ความหนาแน่นของกิจกรรมเมือง → heat generation | VIIRS, Urbanization proxy |

---

## 3. Weighting of Factors

| Factor | Weight | Justification |
|--------|--------|---------------|
| LST | 0.4 | ปัจจัยสำคัญที่สุด เป็นตัวชี้วัดความร้อนจริง |
| NDVI | 0.2 | Vegetation มีผลลดความร้อน แต่ผลรองลงมาจาก LST |
| NDBI | 0.2 | Built-up ส่งผลเพิ่มความร้อนเชิงพื้นที่ |
| Nighttime Light | 0.2 | Human activity → Heat generation, แต่ผลไม่เด่นเท่า LST |

> น้ำหนักมาจาก **Urban Heat Island และ Expert Knowledge**

---

## 4. Spatial Model

**Normalization:**  
ใช้ **Min-Max Normalization (0–1)**  
\[
X_{norm} = \frac{X - X_{min}}{X_{max} - X_{min}}
\]  
- NDVI ถูกกลับค่าเป็น \(1 - NDVI_{norm}\) เพราะ vegetation สูง → ลดความร้อน

**Weighted Combination:**  
\[
HeatRisk = 0.4 * LST_{norm} + 0.2 * (1-NDVI_{norm}) + 0.2 * NDBI_{norm} + 0.2 * NTL_{norm}
\]

**Classification Thresholds**  
- Low 0–0.3  
- Medium 0.3–0.6  
- High 0.6–1.0  

> Threshold มาจากการ Normalize

---

## 5. Sensitivity Analysis

**วิธีทดสอบ**  
- ปรับ **น้ำหนัก LST ±20%**  
- ตรวจสอบผลกระทบต่อ Heat Risk Index และ Classification  

**ผลลัพธ์**  
- Mean difference (+20% LST) = 0.00343  
- Mean difference (-20% LST) = -0.00386  
- Max uncertainty = 0.298  
- Mean uncertainty = 0.0295  

**ข้อสรุป**  
- โมเดล **Robust** ต่อการเปลี่ยน LST ในพื้นที่ส่วนใหญ่  
- บางพื้นที่มีความไม่แน่นอนสูง → ต้องระมัดระวังเมื่อตีความ

---

## 6. Validation

**Data Used**  
- LST จริง (MODIS)  
- Nighttime Light (VIIRS)  

**Correlation**  
- Heat vs LST = 0.978 → สอดคล้องสูง  
- Heat vs NTL = 0.695 → Urbanization มีผลแต่ไม่เด่นเท่า LST

**Limitations**  
- ไม่มี Ground Truth ระดับ pixel ของ Heat Stress  
- หากใช้จริง: สามารถใช้ sensor อุณหภูมิพื้นผิว / Weather station / Citizen sensor ตรวจสอบ  

**Recommendations**  
- โมเดลเหมาะสำหรับ **ระดับจังหวัด/อำเภอ**  
- การอัปเดตรายปี โหลดข้อมูลใหม่ → Normalize → Combine → Classify
- โมเดลเหมาะสำหรับ **ระดับจังหวัด/อำเภอ**  
- การอัปเดตรายปี: โหลดข้อมูลใหม่ → Normalize → Combine → Classify
