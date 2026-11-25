# Onamning ish hisobi

Mobil-first, HTML/CSS/JS app. Local-first: ma'lumotlar brauzerda saqlanadi. Firebase Google sign-in/cloud sync yoqish uchun konfiguratsiya talab qilinadi.

## Local ishga tushirish
`index.html`ni to'g'ridan-to'g'ri file:// orqali ochmang (module va Service Worker uchun server kerak). VS Code Live Server yoki:

```bash
python -m http.server 8000
```

So'ng `http://localhost:8000` ni oching.

## Firebase / Google login va sync sozlash
1. Firebase Console: https://console.firebase.google.com/ da project yarating.
2. Authentication -> Sign-in method -> Google provider-ni yoqing.
3. Firestore Database -> Create database (production mode tavsiya).
4. Project settings -> Your apps -> Web app yaratib Firebase config oling.
5. `app.js` boshidagi `firebaseConfig` qiymatlarini to'ldiring.
6. Firestore Rules quyidagicha o'rnating:

```rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid}/workdata/{docId} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

7. Hosted app uchun hosting domainini Firebase Authentication -> Settings -> Authorized domains ro'yxatiga qo'shing.

Cloud sync yoqilganda asosiy state `users/{uid}/workdata/main` documentida saqlanadi. Cloudga saqlash faqat Google login qilganda ishlaydi. Login bo'lmasa localStorage ishlatiladi.

## PWA
HTTPS hosting (GitHub Pages, Firebase Hosting va boshqalar) yoki localhost talab qilinadi. Hostingga project filesini yuklang, telefonda Chrome orqali sahifani ochib Add to Home Screen/Install ni tanlang.

## Backup / restore
Backup JSON faylni yuklab oladi. Restore mavjud records va archive yozuvlari bilan birlashtiradi; keyin cloud sync login bo'lsa yangilanadi.

## Oylik arxiv
Oyni arxivlash snapshot yaratadi. Arxivlangan oy records tahririga to'siq qo'yiladi. "Arxivni qayta ochish" snapshotni olib tashlab oy qayta tahrirlanishiga imkon beradi (arxiv snapshot qayta ochilganda o'chadi).

## Cheklovlar va eslatma
- Bu dastlabki MVP. Cloud data merging/real-time sync conflict resolution, offline queue, multi-device conflict management va server-side archive enforcement hali production darajasida emas.
- Service worker static filesni cache qiladi; Firestore SDK/API calls avtomatik offline queue sifatida bu implementatsiyada sozlanmagan.
- Arxiv lock UI darajasida. Browser storage ni o'zgartirib bypass qilish mumkin. Production uchun arxivni Firestore security rules/transaction orqali enforce qilish kerak.
- CSV eksportda current rate ishlatiladi; closed archive snapshot esa arxivlangan paytdagi rate va earningsni saqlaydi.
