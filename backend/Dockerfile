FROM node:18-alpine AS base

WORKDIR /usr/src/app

# تثبيت تبعيات النظام لـ Prisma
RUN apk add --no-cache openssl

FROM base AS builder

# نسخ ملفات تعريف التبعيات
COPY package*.json ./
COPY prisma ./prisma/

# تثبيت التبعيات
RUN npm ci

# نسخ بقية الملفات
COPY . .

# توليد عميل Prisma
RUN npx prisma generate

# بناء التطبيق
RUN npm run build

FROM base AS runner

# نسخ التبعيات الضرورية
COPY --from=builder /usr/src/app/node_modules ./node_modules
COPY --from=builder /usr/src/app/package*.json ./
COPY --from=builder /usr/src/app/dist ./dist
COPY --from=builder /usr/src/app/prisma ./prisma

# أمر التشغيل
CMD ["npm", "run", "start:migrate"]