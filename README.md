# Next.js SaaS Starter

This is a starter template for building a SaaS application using **Next.js** with support for authentication, Stripe integration for payments, and a dashboard for logged-in users.

**Demo: [https://next-saas-start.vercel.app/](https://next-saas-start.vercel.app/)**

## Features

- Marketing landing page (`/`) with animated Terminal element
- Pricing page (`/pricing`) which connects to Stripe Checkout
- Dashboard pages with CRUD operations on users/teams
- Basic RBAC with Owner and Member roles
- Subscription management with Stripe Customer Portal
- Email/password authentication with JWTs stored to cookies
- Global middleware to protect logged-in routes
- Local middleware to protect Server Actions or validate Zod schemas
- Activity logging system for any user events

## Tech Stack

- **Framework**: [Next.js](https://nextjs.org/)
- **Database**: [Postgres](https://www.postgresql.org/)
- **ORM**: [Drizzle](https://orm.drizzle.team/)
- **Payments**: [Stripe](https://stripe.com/)
- **UI Library**: [shadcn/ui](https://ui.shadcn.com/)

## Getting Started

```bash
git clone https://github.com/nextjs/saas-starter
cd saas-starter
pnpm install
```

## Running Locally

[Install](https://docs.stripe.com/stripe-cli) and log in to your Stripe account:

```bash
stripe login
```

Use the included setup script to create your `.env` file:

```bash
pnpm db:setup
```

Run the database migrations and seed the database with a default user and team:

```bash
pnpm db:migrate
pnpm db:seed
```

This will create the following user and team:

- User: `test@test.com`
- Password: `admin123`

You can also create new users through the `/sign-up` route.

Finally, run the Next.js development server:

```bash
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser to see the app in action.

You can listen for Stripe webhooks locally through their CLI to handle subscription change events:

```bash
stripe listen --forward-to localhost:3000/api/stripe/webhook
```

## Testing Payments

To test Stripe payments, use the following test card details:

- Card Number: `4242 4242 4242 4242`
- Expiration: Any future date
- CVC: Any 3-digit number

## Going to Production

When you're ready to deploy your SaaS application to production, follow these steps:

### Set up a production Stripe webhook

1. Go to the Stripe Dashboard and create a new webhook for your production environment.
2. Set the endpoint URL to your production API route (e.g., `https://yourdomain.com/api/stripe/webhook`).
3. Select the events you want to listen for (e.g., `checkout.session.completed`, `customer.subscription.updated`).

### Deploy to Vercel

1. Push your code to a GitHub repository.
2. Connect your repository to [Vercel](https://vercel.com/) and deploy it.
3. Follow the Vercel deployment process, which will guide you through setting up your project.

### Add environment variables

In your Vercel project settings (or during deployment), add all the necessary environment variables. Make sure to update the values for the production environment, including:

1. `BASE_URL`: Set this to your production domain.
2. `STRIPE_SECRET_KEY`: Use your Stripe secret key for the production environment.
3. `STRIPE_WEBHOOK_SECRET`: Use the webhook secret from the production webhook you created in step 1.
4. `POSTGRES_URL`: Set this to your production database URL.
5. `AUTH_SECRET`: Set this to a random string. `openssl rand -base64 32` will generate one.

## Other Templates

While this template is intentionally minimal and to be used as a learning resource, there are other paid versions in the community which are more full-featured:

- https://achromatic.dev
- https://shipfa.st
- https://makerkit.dev
- https://zerotoshipped.com
- https://turbostarter.dev

---

## LaundryKu — Fase 1–4

Repo ini sedang diubah dari SaaS starter generik menjadi aplikasi laundry Indonesia.
Cakupan, aturan hitung, dan batasannya ada di [PRD-FASE-1.md](PRD-FASE-1.md) (POS & nota)
[PRD-FASE-2.md](PRD-FASE-2.md) (antrian, WhatsApp, laporan), dan
[PRD-FASE-3.md](PRD-FASE-3.md) (paket, penagihan, onboarding), dan
[PRD-FASE-4.md](PRD-FASE-4.md) (email undangan & reset password).

### Deploy ke produksi

Panduan lengkap VPS + EasyPanel ada di [DEPLOY_EASYPANEL.md](DEPLOY_EASYPANEL.md).
Ringkasnya: `Dockerfile` sudah menjalankan migrasi dan menyiapkan paket langganan
otomatis setiap container start, jadi tidak ada langkah manual yang bisa terlupa.

Satu hal yang paling sering salah: **`BASE_URL` wajib diisi domain asli**. Kalau
tidak, tautan undangan dan reset password mengarah ke `localhost` tanpa error apa pun.

### Menjalankan di lokal

Butuh PostgreSQL yang sudah jalan. Isi `.env` (lihat `.env.example`), lalu:

```bash
pnpm install
pnpm db:migrate
pnpm db:seed
pnpm db:seed:laundry
pnpm db:seed:paket
pnpm dev
```

`pnpm db:seed` membuat user awal `test@test.com` / `admin123`.
`pnpm db:seed:laundry` mengisi 2 outlet, 8 layanan, dan 3 pelanggan contoh.
`pnpm db:seed:paket` menyiapkan paket Gratis/Pro/Bisnis — **wajib**, tanpa ini halaman
Langganan akan error. Ketiganya aman dijalankan berulang.

Akun yang mendaftar lewat `/sign-up` akan diarahkan ke onboarding untuk membuat outlet
pertama dan memilih layanan awal.

### Halaman yang sudah ada

| Route | Isi |
|---|---|
| `/dashboard/pos` | POS kasir: pilih pelanggan & outlet, input cucian, hitung otomatis, simpan + DP |
| `/dashboard/antrian` | Papan antrian produksi — kolom per status, tombol maju/mundur, penanda terlambat |
| `/dashboard/laporan` | Omzet, piutang, layanan terlaris, per outlet — Owner / Manajer |
| `/dashboard/notifikasi` | Template & sakelar WhatsApp + riwayat pengiriman — Owner / Manajer |
| `/dashboard/langganan` | Paket aktif, pemakaian vs batas, upgrade, riwayat tagihan |
| `/dashboard/mulai` | Onboarding tenant baru: outlet pertama + layanan awal |
| `/pricing` | Halaman harga publik dalam Rupiah |
| `/lupa-password` | Minta tautan reset password |
| `/reset-password/[token]` | Buat password baru lewat tautan dari email |
| `/dashboard/pesanan` | Daftar pesanan, filter status, pencarian |
| `/dashboard/pesanan/[id]` | Detail, riwayat status, pembayaran, tombol ubah status |
| `/dashboard/pesanan/[id]/nota` | Nota siap cetak thermal 58 mm / 80 mm |
| `/dashboard/pelanggan` | CRUD pelanggan (hapus diblokir kalau sudah punya pesanan) |
| `/dashboard/layanan` | CRUD layanan & harga — Owner / Manajer |
| `/dashboard/outlet` | CRUD outlet — Owner ubah, Manajer lihat saja |

### Catatan penting

- **Uang disimpan sebagai integer Rupiah**, bukan desimal. Semua rumus terpusat di
  [lib/laundry/pricing.ts](lib/laundry/pricing.ts) — jangan hitung ulang di komponen.
- **Harga tidak pernah dipercaya dari client.** Server membaca ulang harga dari database
  saat menyimpan pesanan.
- Tabel domain laundry pakai `timestamptz` (UTC di DB, tampil WIB). Tabel bawaan starter
  (`users`, `teams`, dll) masih `timestamp` tanpa timezone — belum diperbaiki.
- **Jangan pakai subquery berkorelasi di dalam `` sql`` `` drizzle.** Drizzle merender kolom
  tanpa prefix tabel, jadi `where ${orders.customerId} = ${customers.id}` menjadi
  `where "customer_id" = "id"` dan diam-diam salah. Pakai LEFT JOIN + GROUP BY.
- **Stripe sudah dicabut seluruhnya** di Fase 3, diganti adapter Xendit/Midtrans. Tanpa
  kredensial, penagihan berjalan dalam **mode simulasi**: tagihan tetap tercatat, tapi
  pelunasannya lewat halaman simulasi internal. Isi `BILLING_PROVIDER` beserta kuncinya
  di `.env` untuk mengaktifkan pembayaran asli.
- **Batas paket ditegakkan di server action**, bukan disembunyikan di UI — lihat
  [lib/billing/batas.ts](lib/billing/batas.ts).
- **Webhook penagihan wajib lolos verifikasi tanda tangan**, dan `lunasiInvoice`
  idempoten karena gateway mengirim ulang sampai dapat balasan 200.
- **Jangan menambahkan `<head>` manual di root layout.** Next App Router mengelola
  `<head>` sendiri; menyisipkannya manual membuat skrip streaming tidak terkirim, dan
  semua komponen yang menunggu promise `SWRConfig` tersuspend selamanya tanpa pesan error.
- **Email juga punya mode simulasi.** Tanpa `RESEND_API_KEY` atau `SMTP_URL`, isi email
  dirender dan dicatat di tabel `email_log`, tapi tidak dikirim. Isi `EMAIL_PROVIDER`
  (`resend` atau `smtp`) untuk mengirim sungguhan.
- **Token reset password disimpan sebagai hash**, dan tautannya disamarkan sebelum isi
  email masuk ke log — kalau tidak, `email_log` jadi daftar kunci cadangan setiap akun.
- **Client Postgres di-cache di `globalThis`** ([lib/db/drizzle.ts](lib/db/drizzle.ts)).
  Tanpa itu setiap hot-reload membuat koneksi baru tanpa menutup yang lama, dan server
  Postgres kehabisan slot (`sorry, too many clients already`) — ikut mematikan proyek lain
  yang berbagi server yang sama.
- **WhatsApp berjalan dalam mode simulasi** selama `WA_TOKEN` kosong: pesan dirender dan
  dicatat di tabel `notifications`, tapi tidak dikirim. Isi `WA_PROVIDER` (`fonnte` atau
  `wablas`) dan `WA_TOKEN` di `.env` untuk mengirim sungguhan. Token sengaja **tidak**
  disimpan di database.
- **Angka laporan jangan dihitung lewat satu JOIN.** Menggabung `orders` dengan `payments`
  atau `order_items` dalam satu query melipatgandakan baris dan menggelembungkan omzet —
  lihat `getRingkasanLaporan` di [lib/laundry/queries-fase2.ts](lib/laundry/queries-fase2.ts).
- **Logika yang dipakai server component jangan ditaruh di berkas `'use client'`.**
  TypeScript tidak menangkapnya; errornya baru muncul saat halaman dibuka. Modul netral
  seperti [lib/laundry/periode.ts](lib/laundry/periode.ts) adalah tempatnya.

### Belum ada (menyusul)

Verifikasi alamat email saat pendaftaran · panel super-admin (lihat semua tenant,
suspend, MRR) · antar-jemput & kurir ·
laporan laba rugi (butuh tabel pengeluaran) · nomor WhatsApp per tenant · harga khusus
per outlet lewat UI (tabel `service_prices` sudah ada, formnya belum) · penugasan anggota
ke outlet lewat UI · reset password & email · tracking publik · PWA offline · faktur PPN.
