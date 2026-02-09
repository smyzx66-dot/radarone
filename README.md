# Radar ONE  
**خريطة تفاعلية فورية للتهديدات**

(أحداث في روسيا والمناطق الجديدة يتم جمعها من قنوات تيليجرام، معالجتها بواسطة نموذج LLM، وإرسالها للمشتركين عبر WebSocket وبوت تيليجرام)

---

## 🗣️ لغات README

- **English (الإنجليزية)**
- **Русский (الروسية)**

---

## 📚 المحتويات

1. نظرة عامة  
2. الميزات الرئيسية  
3. بنية المشروع  
4. التشغيل السريع عبر Docker  
5. التشغيل بدون Docker (للتطوير)  
6. الإعدادات ومتغيرات البيئة  
7. قاعدة البيانات  
8. الباك-إند (FastAPI)  
9. الواجهة الأمامية (الخريطة والإشعارات)  
10. بوت تيليجرام  
11. السجلات (Logs)  
12. التطوير والمساهمة  
13. الترخيص  
14. التواصل  

---

## 🌐 نظرة عامة

يقوم Radar ONE بجمع الرسائل من قنوات تيليجرام العامة التي تُبلغ عن تهديدات جوية وصاروخية وغيرها.

يتم تحليل الرسائل عبر نموذج LLM (إما OpenAI GPT o3-mini أو Ollama) لاستخراج:

- **المنطقة** — الاسم الرسمي الدقيق لإحدى المناطق الفيدرالية الروسية  
- **نوع التهديد** —  
  `UAV` هجوم طائرة مسيرة  
  `AIR` تهديد جوي  
  `ROCKET` تهديد صاروخي  
  `UB` هجوم قارب مسير  
  `ALL` جميع الأنواع  
- **الحالة** —  
  `HD` خطر عالي  
  `MD` خطر متوسط  
  `AC` لا يوجد خطر

يتم حفظ البيانات في PostgreSQL وإرسالها للمشتركين عبر:

- WebSocket — تحديث مباشر للخريطة  
- بوت تيليجرام — إشعارات فورية وتقارير يدوية

---

## 🚀 الميزات الرئيسية

| الميزة | التنفيذ |
|--------|---------|
| تحديث فوري | `listener.py` يفحص القنوات كل 10 ثواني و PostgreSQL يستخدم LISTEN/NOTIFY |
| تحليل تلقائي | `analyzer.py` يستخدم LLM ويعود إلى Ollama عند فشل OpenAI |
| الاشتراك بالمناطق | البوت يخزن الاشتراكات في جدول `subscriptions` |
| خريطة تفاعلية | MapLibre GL تغيّر ألوان المناطق حسب مستوى الخطر |
| WebSocket API | `snapshot` و `region_update` |
| إشعارات تيليجرام | رسائل HTML منسقة |
| إدارة المستخدمين | الأدمن يمكنه حظر/فك الحظر وإرسال بث عام |
| السجلات | `logger.py` يسجل في الملف والدخول اليومي لمدة 30 يوم |
| Docker Compose | تشغيل كامل المشروع بحاويات |

---

## 🏗️ بنية المشروع

Telegram Channels → listener.py ↔ analyzer.py ↓ PostgreSQL ↓ FastAPI Backend ↔ Telegram Bot ↓ WebSocket ↓ Frontend (NGINX)

---

## 📦 التشغيل السريع عبر Docker

**المتطلبات:** Docker 20.10+

```bash
git clone https://github.com/justarist/radarone.git
cd radarone

cp .env.example .env

docker compose up -d

بعد التشغيل:

API: http://localhost/api/statuses

الخريطة: http://localhost

WebSocket: ws://localhost/ws


إيقاف:

docker compose down


---

🛠️ التشغيل بدون Docker (للتطوير)

git clone https://github.com/justarist/radarone.git
cd radarone

python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

uvicorn main:app --host 0.0.0.0 --port 8000

لتشغيل الواجهة:

cd frontend
python -m http.server 8080


---

🔧 الإعدادات ومتغيرات البيئة

مثال .env

# السيرفر
HOST=0.0.0.0
PORT=8000
POLL_FALLBACK_SEC=15

# قاعدة البيانات
DB_USER=postgres
DB_PASSWORD=760942
DB_NAME=attacks


---

🗄️ قاعدة البيانات

يتم إنشاء الجداول تلقائياً عند أول تشغيل باستخدام db.py.


---

⚙️ الباك-إند (FastAPI)

المسارات الأساسية:

/api/statuses — حالة جميع المناطق

/ws — اتصال WebSocket مباشر


المهام الخلفية:

الاستماع لقنوات تيليجرام

تحليل الرسائل

تشغيل البوت في Thread منفصل



---

🗺️ الواجهة الأمامية

ملفات HTML و JS ثابتة متصلة بـ WebSocket وتعرض الخريطة مع تحديثات فورية للألوان حسب الخطر.


---

🤖 بوت تيليجرام

الوظائف:

الاشتراك بالمناطق

استقبال إشعارات

إرسال تقارير يدوية

أوامر الأدمن



---

📝 السجلات (Logs)

يتم حفظ السجلات في:

logs/radarone.log

تدوير يومي لمدة 30 يوم.


---

👨‍💻 التطوير والمساهمة

مرحب بأي مساهمة عبر Pull Request.


---

📄 الترخيص

يتم تحديده في ملف LICENSE.


---

📞 التواصل

راجع مستودع GitHub للمزيد من التفاصيل.
