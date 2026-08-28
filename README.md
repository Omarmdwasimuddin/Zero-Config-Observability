# NestJS Zero-Config Observability (Observe) 

## এটা কী?

**Observe** হলো NestJS-এর নিজস্ব zero-config observability tool — মানে খুব কম setup-এই তোমার application-এর request, performance, error ইত্যাদি monitor করা যায়। নিচে ধাপে ধাপে পুরো setup process দেওয়া হলো।

---

## ধাপ ১: Observe Dashboard-এ যাওয়া

1. **[https://nestjs.com/](https://nestjs.com/)** ওয়েবসাইটে যাও
2. **Observe** নামের অপশনে ক্লিক করো
3. এরপর **"Instrument your app"** অপশনে ক্লিক করো
4. Login করার জন্য **"Continue with GitHub"** সিলেক্ট করো
5. Login হয়ে গেলে **[https://www.observe.nestjs.com/dashboard](https://www.observe.nestjs.com/dashboard)** — এই dashboard পেজ খুলে যাবে

---

## ধাপ ২: Project তৈরি করা

1. Dashboard-এ **CREATE PROJECT** বাটনে ক্লিক করো
2. একটা ফর্ম আসবে, সেখানে দিতে হবে:
   - **Name:** `my-nest-app`
   - **Team:** `students-api`
   - একটা **Avatar** সিলেক্ট করো
3. সবশেষে **CONFIRM** বাটনে ক্লিক করো

> ⚠️ **গুরুত্বপূর্ণ নোট:** "Create"-এ ক্লিক করতেই হবে — না হলে Team option-এ কোনো value দেখাবে না।

---

## ধাপ ৩: Application তৈরি করা

1. যে project তৈরি করেছ (`my-nest`), সেটাতে ক্লিক করো
2. **"Create application"** অপশনে ক্লিক করো, এরপর আবার **CREATE APPLICATION** বাটনে ক্লিক করো
3. Application-এর নাম দাও: `my-application`

---

## ধাপ ৪: API Key তৈরি করা

1. সাইডবার থেকে **API KEYS** অপশনে ক্লিক করো
2. **ADD KEY** বাটনে ক্লিক করো
3. ফর্মে দাও:
   - **Name:** `my-api-keys`
   - **Application:** `my-application`
4. **CONFIRM** বাটনে ক্লিক করো
5. যে **App Key** আর **App Secret** পাবে, সেগুলো copy করে রাখো — এগুলো একটু পরে `.env` file-এ বসাতে হবে

---

## ধাপ ৫: `.env` File-এ Key বসানো

```bash
OBSERVE_APP_KEY=""
OBSERVE_APP_SECRET=""
```

উপরের quote-এর ভিতরে ধাপ ৪-এ পাওয়া App Key আর App Secret বসিয়ে দাও।

---

## ধাপ ৬: SDK Install করা

তোমার NestJS project-এর terminal-এ গিয়ে চালাও:

```bash
npm install @nestjs/observe
```

---

## ধাপ ৭: `app.module.ts` Setup করা

`createObserveModule()` থেকে প্রথমে `ObserveModule` আর `ObserveInstrument` তৈরি করে নিতে হবে:

```ts
import { Module } from '@nestjs/common';
import { createObserveModule } from '@nestjs/observe';

export const { ObserveModule, ObserveInstrument } = createObserveModule();

@Module({
  imports: [
    ObserveModule.forRoot({
      appKey: process.env.OBSERVE_APP_KEY!,
      appSecret: process.env.OBSERVE_APP_SECRET!,
      serviceId: 'my-nest-app',
    }),
  ],
})
export class AppModule {}
```

> **নোট:** `serviceId: 'my-nest-app'` হলো তোমার application/service-এর identifier। এটা তোমার নিজের application অনুযায়ী যেকোনো নাম দিতে পারো — documentation অনুযায়ী `serviceId` দেওয়া **required**।

---

## ধাপ ৮: `main.ts` Setup করা

`NestFactory.create()`-এ `instrument` option হিসেবে `ObserveInstrument` পাস করতে হবে:

```ts
const app = await NestFactory.create(AppModule, {
  instrument: ObserveInstrument,
});
```

Environment variable load করার জন্য `main.ts`-এর একদম শুরুতে `dotenv/config` import করতে হবে:

```ts
import 'dotenv/config';
```

এর জন্য `dotenv` package install করতে হবে:

```bash
npm install dotenv --save
```

### Final `main.ts` ফাইল দেখতে এমন হবে:

```ts
import 'dotenv/config';
import { NestFactory } from '@nestjs/core';
import { AppModule, ObserveInstrument } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, { instrument: ObserveInstrument });
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

---

## পুরো Workflow একনজরে

```
Observe Dashboard
      ↓
Project
      ↓
Application
      ↓
appKey + appSecret
      ↓
.env
      ↓
@nestjs/observe
      ↓
ObserveModule.forRoot()
      ↓
ObserveInstrument
      ↓
NestJS App
      ↓
API Request
      ↓
Observe Dashboard
```

মানে সহজ কথায় — Dashboard-এ Project ও Application বানিয়ে key নিয়ে আসো, সেই key `.env`-এ বসাও, SDK install করে `app.module.ts` আর `main.ts`-এ wire করে দাও। এরপর তোমার app-এ যত API request আসবে, সেগুলোর data automatic Observe Dashboard-এ চলে যাবে — দেখার জন্য আলাদা করে কিছু log/setup করা লাগবে না।

---
