# Data_Science_Assingment
Career Coraline : Assignments for Data Scientist Position
Data sets: [m5-forecasting-accuracy](https://www.kaggle.com/c/m5-forecasting-accuracy)
# 1_M5_Forecasting _Accuracy_Presentation

## <h3>1. Business Understanding and Problem Formulation</h3>

<p> จาก Description ของ M5 Forecasting - Accuracy ใน kaggle เค้าต้องการให้คาดการณ์ยอดขายรายวันสำหรับ 28 วันข้างหน้า โดยใช้ข้อมูลครอบคลุมร้านค้าในสามรัฐของสหรัฐอเมริกา (แคลิฟอร์เนีย เท็กซัส และวิสคอนซิน) และรวมถึงระดับสินค้า แผนก หมวดหมู่ผลิตภัณฑ์ และรายละเอียดร้านค้า นอกจากนี้ยังมีตัวแปรอธิบาย เช่น ราคา โปรโมชั่น วันในสัปดาห์ และกิจกรรมพิเศษต่างๆ</p>

---

## <h3>2. Data Understanding / EDA process</h3>

ในขั้นตอน EDA process จะใช้เบสจาก __[ref](https://www.kaggle.com/code/anshuls235/time-series-forecasting-eda-fe-modelling/notebook)__ นี้ ซึ่งจะมีการทำ
* Label Encoding
* Lag features 
* Mean Encoding --> หาค่าเฉลี่ยของยอดขายโดยอิงตาม [item,
state,
store,
category,
department,
category & department,
store & item,
category & item,
department & item,
state & store,
state, store and category,
store, category and department]
* Rolling Window Statistics
<div style="text-align:center"><img src="img\3hotmk.gif" /></div>


* Expanding Window Statistics
<div style="text-align:center"><img src="img\output_B4KHcT.gif" /></div>

* Trend --> selling_trend (ยอดขายเฉลี่ยรายวันลบด้วยยอดขายเฉลี่ยของแต่ละสินค้า)


---
## <h3>3. Modeling: Model / Feature selection</h3>

โมเดลที่ใช้เป็น XGBRegressor 
```python: 
model = XGBRegressor(   objective='reg:squarederror',  
                        booster = 'gblinear',      
                        n_estimators=500,          
                        eta=0.3,                   
                        seed = 20)
```
ซึ่งข้อมูลที่ใช้เทรนจะไปดึงตาม stor_id [CA_1, CA_2, CA_3, CA_4, TX_1, TX_2, TX_3, WI_1, WI_2, WI_3]
จากนั้นจะแบ่งให้
* ช่วงวันที่ 1-1913 เป็นข้อมูลเทรน(Train)
* ช่วงวันที่ 1914 - 1942 เป็นชุดตรวจสอบ(Validation)
* ช่วงวันที่ 1942 เป็นต้นไป( 28 วันหลังจากนั้น) เป็นตัววัดผล(Evaluation)

<h3>Feature importance</h3>
<div style="text-align:center" width="300" ><img src="img\feature-selection2.png" /></div>

<p>จากภาพข้างต้น จะเห็นว่า โมเดลให้ความสำคัญกับตัวแปร selling_trend, store_item_sold_avg, cat_sold_avg, item_sold_avg และ store_cat_dept_old_avg ตามลำดับ</p>

---

## <h3>4. Model Evaluation</h3>

<p> ในสเต็ปนี้เรามีแค่ validation จริงจากไฟล์ sale_train_validation.csv ดังนั้นจึงนำผลลัพธ์ที่โมเดลคาดการณ์มาคิด accuracy ได้ประมาณ 76.07% 

<b style="color:red;" >เพิ่มเติม</b> ถ้าเราปรับให้โมเดลใช้ Loss function อย่าง weighted cross entropy อาจจะทำให้คาดการณ์ได้ดีขึ้น

</p>

---
## <h3>5. Business Impact / Insights</h3>

<p>จาก Feature importance จะเห็นว่า ฟีเจอร์อย่าง selling_trend, store_item_sold_avg, cat_sold_avg, item_sold_avg และ store_cat_dept_old_avg จะส่งผลให้พยากรณ์ยอดขายของวันข้างหน้าได้ ในที่นี่ตีความหมายอย่างงี้

ในรายการสินค้า(ที่เราไม่ได้สนว่าสาขาไหน) selling_trend จะบอกเราว่า ถ้ามันมีแนวโน้มเพิ่มขึ้นคือเป็นสินค้าที่ขายได้ตามแนวโน้ม ทีนี่พอเรามาดูตัวแปรอื่นๆที่เหลือ แนวโน้มของ store_item_sold_avg, cat_sold_avg, item_sold_avg จะช่วยให้ทายสินค้าที่อิงตามหมวด store, cat และ item ได้</p>

---
