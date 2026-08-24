# Mening Xarajatlarim — Bot + Mini-app + Admin panel

Bu loyiha uchtasini o'z ichiga oladi:
- **Telegram bot** — `/start`, xush kelibsiz xabari, va botga yozilgan har qanday savolga AI (Claude) orqali javob beradi (foydalanuvchining xarajat tarixiga asoslanib).
- **Mini-app** — `/app` manzilida, xarajat/daromad, jamg'arma, oylik limit funksiyalari, endi **markaziy bazaga** ulangan.
- **Admin panel** — `/admin` manzilida: foydalanuvchilar ro'yxati, har bir foydalanuvchi haqida AI tahlil, xat tarqatish (broadcast), bot/ilova sozlamalari.

## ⚠️ MUHIM: xavfsizlik

Siz oldin bot tokeningizni ochiq chatga yozgansiz. **Hozirdan boshlab shu tokendan foydalanmang**:
1. Telegram'da @BotFather ga kiring
2. `/mybots` → botingizni tanlang → **API Token** → **Revoke current token**
3. Yangi tokenni oling va uni faqat `.env` faylga yozing (hech qachon chatga, GitHub'ga ochiq yubormang)

## 1. O'rnatish (lokal kompyuterda sinash uchun)

Kompyuteringizda [Node.js](https://nodejs.org) (18+) o'rnatilgan bo'lishi kerak.

```bash
cd xarajatlar-bot
npm install
cp .env.example .env
```

`.env` faylni oching va to'ldiring:
```
BOT_TOKEN=yangi_bot_tokeningiz
MINIAPP_URL=https://your-domain.com/app     # hozircha bo'sh qoldirsangiz ham bo'ladi
ADMIN_PASSWORD=kuchli_parol_oʻylab_toping
SESSION_SECRET=tasodifiy_uzun_matn_32_belgidan_koʻp
ANTHROPIC_API_KEY=sk-ant-...                # console.anthropic.com dan olinadi
PORT=3000
```

Ishga tushirish:
```bash
npm start
```

Server ishga tushgach:
- Bot Telegram'da darhol javob bera boshlaydi (polling rejimida)
- Admin panel: `http://localhost:3000/admin`
- Mini-app: `http://localhost:3000/app` (lekin Telegram tashqarisida ba'zi funksiyalar cheklangan bo'ladi, chunki u Telegram foydalanuvchi ma'lumotiga muhtoj)

## 2. Internetga chiqarish (hosting)

Bot va mini-app ishlashi uchun serverga joylashtirishingiz kerak. Eng oson yo'llardan biri — **Railway** yoki **Render**:

1. Loyihani GitHub'ga yuklang (`.env` faylni **yuklamang** — u `.gitignore`da)
2. Railway.app yoki Render.com'da "New Project" → GitHub repo'ni tanlang
3. "Environment Variables" bo'limida `.env`dagi barcha qiymatlarni qo'shing
4. Deploy bo'lgach, sizga masalan `https://sizning-loyiha.up.railway.app` kabi URL beriladi
5. Shu URL'ni `MINIAPP_URL=https://sizning-loyiha.up.railway.app/app` qilib environment variables'ga qo'shing va qayta deploy qiling
6. @BotFather'da: `/mybots` → botingiz → **Bot Settings** → **Menu Button** → shu mini-app URL'ni belgilang (bu foydalanuvchilarga pastda doimiy "Ilova" tugmasini chiqaradi)

## 3. Admin panelga kirish

`https://sizning-domeningiz/admin` manzilini oching, `.env`dagi `ADMIN_PASSWORD` bilan kiring.

Panelda:
- **Umumiy** — foydalanuvchilar soni, jami daromad/xarajat, so'nggi xat tarqatishlar
- **Foydalanuvchilar** — har bir foydalanuvchining daromad/xarajati, "AI tahlil" tugmasi orqali AI uning moliyaviy holatini qisqacha tahlil qiladi
- **Xat tarqatish** — matn yozib, barcha foydalanuvchilarga bir vaqtda yuborish
- **Sozlamalar** — /start xabari, AI yoqish/o'chirish, AI "shaxsiyati" (system prompt), standart oylik limit

## 4. AI yordamchi qanday ishlaydi

Foydalanuvchi botga (mini-app emas, oddiy chatga) istalgan savol yozsa — masalan "bu oy qancha sarfladim?" — bot uning so'nggi 50 ta yozuvini bazadan olib, Claude API'ga yuboradi va tabiiy tilda javob qaytaradi. Buni admin panelning "Sozlamalar" bo'limidan yoqish/o'chirish va yo'riqnomasini o'zgartirish mumkin.

## Loyiha tuzilishi

```
xarajatlar-bot/
├── server.js          # asosiy server
├── bot.js             # Telegram bot logikasi + AI suhbat
├── db.js              # SQLite baza sxemasi
├── lib/ai.js           # Claude API bilan ishlash
├── middleware/
│   ├── telegramAuth.js # mini-app so'rovlarini tekshirish
│   └── adminAuth.js     # admin sessiyasini tekshirish
├── routes/
│   ├── api.js          # mini-app API
│   └── admin.js        # admin panel API
└── public/
    ├── miniapp/         # xarajat/daromad ilovasi (HTML/CSS/JS)
    └── admin/           # admin panel (HTML/CSS/JS)
```

## Keyingi qadamlar (ixtiyoriy takomillashtirish)

- SQLite o'rniga PostgreSQL'ga o'tish (foydalanuvchilar ko'payganda)
- Webhook rejimiga o'tish (polling o'rniga) — tezroq va samaraliroq
- Kategoriyalarni admin paneldan sozlash imkoniyati
- Broadcast xabarlariga rasm/tugma qo'shish
