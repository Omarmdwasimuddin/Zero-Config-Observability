# NestJS Zero-Config Observability (Observe) — সহজ বাংলা গাইড

## এটা কী?

**Observe** হলো NestJS-এর নিজস্ব zero-config observability tool — মানে খুব কম setup-এই তোমার application-এর request, performance, error ইত্যাদি monitor করা যায়। নিচে ধাপে ধাপে পুরো setup process দেওয়া হলো।

---

## ধাপ ১: Observe Dashboard-এ যাওয়া

1. **[https://nestjs.com/](https://nestjs.com/)** ওয়েবসাইটে যাও
2. **Observe** নামের অপশনে ক্লিক করো

![Observe অপশনে ক্লিক](https://github.com/user-attachments/assets/44b1ddba-2b71-4801-acc1-b3fb26e47830)

3. এরপর **"Instrument your app"** অপশনে ক্লিক করো

![Instrument your app](https://github.com/user-attachments/assets/1950cf9c-267c-46f8-91eb-6d3f069c81db)

4. Login করার জন্য **"Continue with GitHub"** সিলেক্ট করো

![Continue with GitHub](https://github.com/user-attachments/assets/d74d1fb5-d9e3-45aa-994d-86c2ea01cacf)

5. Login হয়ে গেলে **[https://www.observe.nestjs.com/dashboard](https://www.observe.nestjs.com/dashboard)** — এই dashboard পেজ খুলে যাবে

![Observe Dashboard](https://github.com/user-attachments/assets/b0cd2fff-1be6-4720-a396-4ba6a0f43c27)

---

## ধাপ ২: Project তৈরি করা

1. Dashboard-এ **CREATE PROJECT** বাটনে ক্লিক করো

![Create Project বাটন](https://github.com/user-attachments/assets/250116eb-bf98-49cc-a262-9716eccf6523)

2. একটা ফর্ম আসবে, সেখানে দিতে হবে:
   - **Name:** `my-nest-app`
   - **Team:** `students-api`
   - একটা **Avatar** সিলেক্ট করো
3. সবশেষে **CONFIRM** বাটনে ক্লিক করো

> ⚠️ **গুরুত্বপূর্ণ নোট:** "Create"-এ ক্লিক করতেই হবে — না হলে Team option-এ কোনো value দেখাবে না।

![Project form](https://github.com/user-attachments/assets/72ad8f8a-999f-402c-b80a-c6b3a9fecb97)

![Project তৈরি হওয়ার পর](https://github.com/user-attachments/assets/8bb9ccf3-ffcc-4bcc-b561-a6c0df993924)

---

## ধাপ ৩: Application তৈরি করা

1. যে project তৈরি করেছ (`my-nest`), সেটাতে ক্লিক করো

![my-nest project-এ ক্লিক](https://github.com/user-attachments/assets/f0a77c19-3766-4f2d-ac68-fd3344005e42)

2. **"Create application"** অপশনে ক্লিক করো, এরপর আবার **CREATE APPLICATION** বাটনে ক্লিক করো

![Create Application](https://github.com/user-attachments/assets/01a23af2-6aed-413b-9498-928486be3be0)

3. Application-এর নাম দাও: `my-application`

![Application নাম দেওয়া](https://github.com/user-attachments/assets/b4b624d3-2017-4933-8446-79402e2c5ee1)

---

## ধাপ ৪: API Key তৈরি করা

1. সাইডবার থেকে **API KEYS** অপশনে ক্লিক করো

![API Keys সাইডবার](https://github.com/user-attachments/assets/b87006e3-35dd-4d59-9485-47dd30429fba)

2. **ADD KEY** বাটনে ক্লিক করো

![Add Key বাটন](https://github.com/user-attachments/assets/9bf947fc-c5d4-416e-9f19-7424aa625859)

3. ফর্মে দাও:
   - **Name:** `my-api-keys`
   - **Application:** `my-application`
4. **CONFIRM** বাটনে ক্লিক করো

![API Key ফর্ম](https://github.com/user-attachments/assets/fede9fc8-6192-426b-ab85-408c7d691a65)

5. যে **App Key** আর **App Secret** পাবে, সেগুলো copy করে রাখো — এগুলো একটু পরে `.env` file-এ বসাতে হবে

![App Key ও App Secret copy করা](https://github.com/user-attachments/assets/315f416c-3478-43f5-b424-968b6f5eb0db)

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

## সংক্ষেপে (Quick Summary)

| ধাপ | কাজ |
|---|---|
| ১ | nestjs.com → Observe → GitHub দিয়ে login |
| ২ | Dashboard-এ Project তৈরি (Name + Team + Avatar) |
| ৩ | Project-এর ভিতরে Application তৈরি |
| ৪ | API Keys থেকে key তৈরি (App Key + App Secret পাওয়া) |
| ৫ | Key দুটো `.env`-এ বসানো |
| ৬ | `npm install @nestjs/observe` |
| ৭ | `app.module.ts`-এ `createObserveModule()` দিয়ে `ObserveModule.forRoot()` setup |
| ৮ | `main.ts`-এ `dotenv/config` import + `instrument: ObserveInstrument` পাস করা |
