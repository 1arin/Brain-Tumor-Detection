# Brain-Tumor-Detection
Overview
โปรเจกต์นี้ใช้ Deep Learning และ Computer Vision เพื่อตรวจจับเนื้องอกในสมองจากภาพ MRI โดยอาศัยโมเดล Convolutional Neural Networks (CNNs) พร้อมเทคนิค Transfer Learning

โปรแกรมนี้ถูกพัฒนาขึ้นเพื่อจำแนกประเภทของเนื้องอกในสมองจากภาพ MRI โดยใช้โครงสร้างแบบ VGG16 พร้อมการปรับปรุงโครงสร้างให้เหมาะสมกับชุดข้อมูลเฉพาะ สามารถทำงานได้บน Google Colab และให้ความแม่นยำสูงถึง **96%**

# คุณสมบัติหลัก 🚀
- **ระบบจัดการข้อมูลอัจฉริยะ**
  - โหลดข้อมูลจาก Google Drive โดยตรง
  - สุ่มลำดับข้อมูลแบบไดนามิก
  - ปรับแต่งภาพแบบเรียลไทม์ด้วยฟังก์ชันเสริมเฉพาะ

ตัวอย่างฟังก์ชันเสริมสร้างภาพ
python
def augment_image(image):
    image = ImageEnhance.Brightness(image).enhance(random.uniform(0.8, 1.2))
    image = ImageEnhance.Contrast(image).enhance(random.uniform(0.8, 1.2))
    return np.array(image)/255.0

สถาปัตยกรรมโมเดลแบบปรับปรุง
- ใช้ VGG16 (ฐาน)
- Custom Head:
   - Flatten Layer
   - Dropout 30%
   - Dense 128 Units
   - Dropout 20%
   - Output Layer (Softmax)


## การติดตั้งและใช้งาน 
1. **เตรียมโครงสร้างข้อมูล**
     
  Google Drive ของคุณควรจัดเรียงข้อมูลดังนี้:
  dataset/
  ├── Training/
  │ ├── pituitary/
  │ ├── glioma/
  │ ├── notumor/
  │ └── meningioma/
  └── Testing/ [โครงสร้างเหมือน Training]

2. **เรียกใช้บน Google Colab**
     
  from google.colab import drive
  drive.mount('/content/drive', force_remount=True)
  
  กำหนดพาทข้อมูล
  train_dir = '/content/drive/MyDrive/dataset/Training/'
  test_dir = '/content/drive/MyDrive/dataset/Testing/'

3. **ฝึกโมเดล**
   ตัวชี้วัดประสิทธิภาพ
 
class	     Precision	Recall	F1-Score
Pituitary	 0.99	      1.00	  0.99
Glioma	   0.99	      0.97	  0.98
Notumor	   0.90	      0.94	  0.92
Meningioma 0.96	      0.92	  0.94

### Confusion Matrix
![image](https://github.com/user-attachments/assets/de81da29-ab64-4924-8ebd-9da261682e33)

ข้อควรทราบ 
1. ข้อกำหนดระบบ
GPU ประเภท Tesla T4 ขึ้นไป
RAM ไม่ต่ำกว่า 12 GB
พื้นที่เก็บข้อมูล 20 GB+

2. การปรับปรุงโมเดล
ตัวอย่างการปรับปรุงโครงสร้าง

base_model.trainable = True
for layer in base_model.layers[:-5]:
    layer.trainable = False
    
3. การแก้ปัญหาเฉพาะหน้า
หากพบข้อผิดพลาดในการโหลดข้อมูล: ตรวจสอบ permission ของ Google Drive
หากความแม่นยำต่ำ: ปรับค่า Dropout เป็น 0.4-0.5
หากเกิด Overfitting: เพิ่มขนาด Batch Size เป็น 32



