# Baby Poshak — Setup গাইড (GitHub + Firebase)

## ধাপ ১: Firebase প্রজেক্ট বানান
1. https://console.firebase.google.com এ যান → **Add project** → নাম দিন (যেমন `baby-poshak`)।
2. বাম মেনু থেকে **Build → Firestore Database** → **Create database** → **Start in production mode** সিলেক্ট করুন।
3. বাম মেনু থেকে **Build → Authentication** → **Get started** → **Email/Password** enable করুন।
4. Authentication → **Users** ট্যাবে গিয়ে নিজের admin ইমেইল/পাসওয়ার্ড দিয়ে একটা ইউজার তৈরি করুন — এটাই Admin প্যানেলে লগইনের জন্য ব্যবহার হবে।
5. **Project settings (⚙️)** → **General** → নিচে স্ক্রল করে **Add app → Web (</>)** → একটা nickname দিন → SDK config কপি করুন।
6. এই config দিয়ে `firebase-config.js` ফাইলের ভেতরের মানগুলো replace করুন।

## ধাপ ২: Firestore Security Rules
Firestore → **Rules** ট্যাবে গিয়ে এটা বসান:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /products/{doc} {
      allow read: if true;
      allow write: if request.auth != null;
    }
    match /orders/{doc} {
      allow create: if true;
      allow read, update, delete: if request.auth != null;
    }
  }
}
```
এতে যেকেউ প্রোডাক্ট দেখতে পারবে ও অর্ডার দিতে পারবে, কিন্তু শুধু লগইন করা Admin প্রোডাক্ট এডিট বা অর্ডার স্ট্যাটাস চেঞ্জ করতে পারবে।

## ধাপ ৩: GitHub এ আপলোড ও Pages এ হোস্ট করা
1. GitHub এ নতুন একটা repository বানান (যেমন `baby-poshak`)।
2. `index.html` এবং `firebase-config.js` (config বসানোর পর) এই দুইটা ফাইল রিপোতে আপলোড করুন।
3. Repo → **Settings → Pages** → Source: **Deploy from a branch** → branch: `main`, folder: `/root` → **Save**।
4. কিছুক্ষণ পর একটা লিংক পাবেন — যেমন `https://yourusername.github.io/baby-poshak/` — এটাই আপনার লাইভ অ্যাপ, সবার জন্য একই ডেটা শেয়ার হবে।

## প্রাথমিক প্রোডাক্ট
প্রথমবার Firestore এ কোনো প্রোডাক্ট থাকবে না — Admin লগইন করে "+ নতুন প্রোডাক্ট" দিয়ে যোগ করে নিন।

## নোট
- Admin প্যানেলে ঢুকতে Firebase Authentication এ বানানো ইমেইল/পাসওয়ার্ড লাগবে।
- পেমেন্ট (bKash/Nagad) এখানে শুধু "মেথড সিলেক্ট" — আসল পেমেন্ট গেটওয়ে ইন্টিগ্রেশন আলাদা এবং তাদের নিজস্ব merchant account/API লাগবে; আপাতত অর্ডার WhatsApp এ পাঠিয়ে ম্যানুয়ালি কনফার্ম করার ব্যবস্থা আছে।
