Urban Heat Stress Risk in Chonburi

## 1. Research Question 
พื้นที่เมืองใดในจังหวัดชลบุรีมีความเสี่ยงต่อ **Urban Heat Stress** สูงที่สุด?  

**เป้าหมาย**  
สร้าง **แผนที่ความเสี่ยงเชิงพื้นที่ (Heat Risk Map)** เพื่อสนับสนุนการวางแผนพื้นที่เมืองและลดผลกระทบต่อประชากร

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

## 4. Spatial Model Overview
- **Normalization:** Min-Max (0–1)  
- NDVI ถูกกลับค่า \(1 - NDVI_{norm}\) เพราะ vegetation สูง → ลดความร้อน  
- **Weighted Combination:**  
\[
HeatRisk = 0.4 * LST_{norm} + 0.2 * (1-NDVI_{norm}) + 0.2 * NDBI_{norm} + 0.2 * NTL_{norm}
\]  
- **Classification Thresholds:**  
  - Low: 0–0.3  
  - Medium: 0.3–0.6  
  - High: 0.6–1.0
