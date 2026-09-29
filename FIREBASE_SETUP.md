# إعداد Firebase - الطريقة الصحيحة

## 1️⃣ إنشاء مشروع Firebase
1. ادخل: https://console.firebase.google.com
2. اضغط **"إضافة مشروع"** (Add Project)
3. اسم المشروع: `suhbat-quran` (أو أي اسم)
4. فعّل **Google Analytics** (اختياري)
5. اضغط **"إنشاء مشروع"**

## 2️⃣ تفعيل Authentication
1. في القائمة اليمنى: **Authentication** → **Sign-in method**
2. فعّل **"مجهول" (Anonymous)** ← **حفظ**
3. هذا يسمح بالتسجيل التلقائي بدون كلمة مرور

## 3️⃣ تفعيل Firestore Database
1. في القائمة: **Firestore Database** → **إنشاء قاعدة بيانات**
2. اختر: **ابدأ في وضع الاختبار** (Test mode) - مؤقتاً
3. اختر الموقع: **eur3 (أوروبا الغربية)** أو الأقرب لك
4. اضغط **تمكين**

## 4️⃣ الحصول على إعدادات التطبيق
1. في **إعدادات المشروع** (⚙️ Project Settings) → **عام**
2. مرر لأسفل إلى **"تطبيقاتك"** → اضغط أيقونة **الويب** (</>)
3. اسم التطبيق: `صحبة القرآن`
4. فعّل **Firebase Hosting** (اختياري)
5. اضغط **تسجيل التطبيق**
6. **انسخ كائن `firebaseConfig`** - هذا ما نحتاجه

## 5️⃣ تعديل قواعد الأمان (مهم!)
في **Firestore** → **القواعد** (Rules)، استبدل الكل بـ:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // كل مستخدم يصل لبياناته فقط
    match /users/{userId}/data/{docId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // النشاط العائلي - قراءة للجميع، كتابة للمستخدمين المسجلين
    match /family_activity/{docId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null 
        && request.resource.data.userId == request.auth.uid;
    }
    
    // المفضلة المشتركة (اختياري)
    match /users/{userId}/favorites/{docId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```
اضغط **نشر** (Publish).

## 6️⃣ تحديث ملف الإعدادات
استبدل محتوى `js/firebase-config.js` بما نسخته:

```javascript
// js/firebase-config.js
window.FirebaseConfig = {
  apiKey: "AIzaSyXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  authDomain: "suhbat-quran.firebaseapp.com",
  projectId: "suhbat-quran",
  storageBucket: "suhbat-quran.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef1234567890"
};
```

## 7️⃣ تحديث `js/firebase.js`
في السطر 11، استبدل `FIREBASE_CONFIG` بـ:

```javascript
var FIREBASE_CONFIG = window.FirebaseConfig;
```

## 8️⃣ اختبار
1. افتح `index.html` في المتصفح
2. افتح **Developer Tools** (F12) → **Console**
3. يجب أن ترى: `Firebase initialized, user: [UID]`
4. جرب إضافة مفضلة أو قراءة سورة
5. في Firebase Console → **Firestore** → **Data** ستجد البيانات تحت `users/[UID]/data/`

---

## ✅ ما سيعمل تلقائياً:
| الميزة | الحالة |
|----------|--------|
| مزامنة الإعدادات | ✅ |
| المفضلة | ✅ |
| سجل القراءة | ✅ |
| النشاط العائلي (مشترك) | ✅ |
| الورد اليومي | ✅ |
| تسجيل الصلاة | ✅ |
| النقاط/المستويات | ✅ |
| العمل دون اتصال | ✅ |

---

## ⚠️ ملاحظات هامة:
- **لا تحتاج لوحة تحكم** - كل شيء من الكود
- **مجاني** - ضمن حدود Spark Plan (50K قراءة/يوم، 20K كتابة/يوم)
- **البيانات مشفرة** - لكل مستخدم UID خاص به
- **النسخ الاحتياطي** - استخدم `Cloud.exportAll()` / `importAll()`

---

## 🔧 استكشاف الأخطاء:
| المشكلة | الحل |
|-----------|------|
| `permission-denied` | تحقق من قواعد Firestore |
| `auth/network-request-failed` | تحقق من الإنترنت / API Key |
| البيانات لا تظهر | تأكد من `firebase-config.js` صحيح |
| يعمل محلياً فقط | Firebase اختياري - localStorage يعمل دائماً |
<tool_call>
<function=write>
<parameter=content>
# Firebase Setup - Complete Guide for صحبة القرآن

## 1. Create Firebase Project
1. Go to: https://console.firebase.google.com
2. Click **"Add Project"**
3. Project name: `suhbat-quran`
4. Enable Google Analytics (optional)
5. Create project

## 2. Enable Authentication
1. Left menu: **Authentication** → **Sign-in method**
2. Enable **Anonymous** → **Save**
3. This allows auto-sign-in without passwords

## 3. Enable Firestore Database
1. Left menu: **Firestore Database** → **Create database**
2. Choose: **Start in test mode** (temporary)
3. Location: **eur3 (Europe West)** or closest
4. Enable

## 4. Get App Config
1. **Project Settings** (⚙️) → **General**
2. Scroll to **"Your apps"** → Click **Web** (</>)
3. App name: `صحبة القرآن`
4. Enable Firebase Hosting (optional)
5. **Register app**
6. **Copy the `firebaseConfig` object** - this is what we need

## 5. Update Security Rules (CRITICAL!)
In **Firestore** → **Rules**, replace ALL with:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // User private data
    match /users/{userId}/data/{docId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    // Family activity - shared read, own write
    match /family_activity/{docId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null 
        && request.resource.data.userId == request.auth.uid;
    }
    
    // Favorites
    match /users/{userId}/favorites/{docId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```
Click **Publish**.

## 6. Update Config File
Replace `js/firebase-config.js` with your copied config:

```javascript
// js/firebase-config.js
window.FirebaseConfig = {
  apiKey: "AIzaSyXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  authDomain: "suhbat-quran.firebaseapp.com",
  projectId: "suhbat-quran",
  storageBucket: "suhbat-quran.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef1234567890"
};
```

## 7. Update firebase.js
Line 11 in `js/firebase.js`:
```javascript
var FIREBASE_CONFIG = window.FirebaseConfig;
```

## 8. Test
1. Open `index.html` in browser
2. DevTools (F12) → Console
3. Should see: `Firebase initialized, user: [UID]`
4. Try adding favorite/reading surah
5. Check Firebase Console → Firestore → Data → `users/[UID]/data/`

---

## ✅ Features Working Automatically:
| Feature | Status |
|---------|--------|
| Settings sync | ✅ |
| Favorites | ✅ |
| Reading history | ✅ |
| Family activity (shared) | ✅ |
| Daily wird | ✅ |
| Prayer logging | ✅ |
| Points/levels | ✅ |
| Offline support | ✅ |

---

## ⚠️ Key Points:
- **Zero user config** - works automatically
- **Free tier** - Spark Plan limits (50K reads/day, 20K writes/day)
- **Encrypted per user** - each user has unique UID
- **Backup/Restore** - `Cloud.exportAll()` / `importAll()`
- **Fallback** - localStorage works if Firebase fails

---

## 🔧 Troubleshooting:
| Issue | Fix |
|-------|-----|
| `permission-denied` | Check Firestore Rules |
| `auth/network-request-failed` | Check internet / API Key |
| Data not showing | Verify `firebase-config.js` |
| Local only | Firebase optional - localStorage always works |