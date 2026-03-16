# 🕋 Kalkulator HPP Umroh — PT. Tamima Jaya Wisata

> Aplikasi web kalkulasi Harga Pokok Penjualan (HPP) paket umroh berbasis React + TypeScript + Tailwind CSS. Berjalan sepenuhnya di browser — tidak memerlukan server backend.

[![TypeScript](https://img.shields.io/badge/TypeScript-5.9-blue?logo=typescript)](https://www.typescriptlang.org/)
[![React](https://img.shields.io/badge/React-19-61dafb?logo=react)](https://react.dev/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-4.x-38bdf8?logo=tailwindcss)](https://tailwindcss.com/)
[![Vite](https://img.shields.io/badge/Vite-7.x-646cff?logo=vite)](https://vitejs.dev/)
[![CI](https://github.com/YOUR_USERNAME/hpp-umroh-calculator/actions/workflows/ci.yml/badge.svg)](https://github.com/YOUR_USERNAME/hpp-umroh-calculator/actions)

---

## ✨ Fitur Utama

| Fitur | Keterangan |
|---|---|
| 🔐 **Autentikasi** | Login, register, session management, role (owner/admin/user) |
| 📊 **6 Tab Kalkulasi** | Operasional, Visa, Land Arrangement, City Tour, Tiket, Final |
| 🏨 **Hotel Engine** | Makkah/Madinah/Transit, multi-hotel, tipe kamar, BRN |
| 💰 **HPP Calculator** | Kalkulasi real-time SAR/USD/IDR per pax |
| 📋 **Result Section** | Rincian hotel, HPP per tipe kamar, komponen aktif |
| 💾 **Auto-save** | Riwayat 15 estimasi, disimpan ke localStorage |
| 📤 **Export** | WhatsApp draft, Print popup, PDF (via browser print) |
| 👥 **Admin Panel** | Manajemen user, approval, email notification log |
| ⚙️ **Settings** | Konfigurasi default nilai, nama app, email admin |
| 📱 **Responsive** | Mobile-friendly, bottom summary bar |

---

## 🚀 Quick Start

### Prerequisites

- Node.js ≥ 18
- npm ≥ 9

### Development

```bash
git clone https://github.com/YOUR_USERNAME/hpp-umroh-calculator.git
cd hpp-umroh-calculator
npm install
npm run dev
```

Buka [http://localhost:5173](http://localhost:5173)

### Production Build

```bash
npm run build
# Output: dist/index.html (single-file, semua CSS+JS inline)
```

### Preview Build

```bash
npm run preview
```

---

## 🔑 Akun Default

| Role | Email | Password |
|---|---|---|
| Owner | `owner@tamima.com` | `OwnerTamima123` |
| Admin | `admin@tamima.com` | `AdminTamima123` |

> Ganti password setelah login pertama melalui **Settings → App Settings**.

---

## 🗂️ Struktur Project

```
src/
├── types/              # TypeScript interfaces & types
│   └── index.ts
├── constants/          # Storage keys, defaults, system users
│   └── index.ts
├── utils/              # Pure helper functions
│   ├── formatters.ts   # formatNumber, formatCurrency (IDR/USD/SAR)
│   ├── auth.ts         # normalizeEmail, isValidEmail, createUserId
│   └── invoiceCode.ts  # sanitizeTravelCode, createInvoiceCode
├── services/           # Data layer (localStorage)
│   ├── storage.ts      # CRUD users, session, settings, email log
│   └── defaultFormData.ts  # Factory: default form state
├── engines/            # Pure calculation functions (NO side effects)
│   ├── calculations.ts # calculateAll() — main HPP engine
│   └── hotelEngine.ts  # Hotel totals, room pricing, LA per pax
├── hooks/              # React state management
│   ├── useAuth.ts      # Login, register, logout, user management
│   ├── useEstimate.ts  # Form state + calculation trigger
│   ├── useHistory.ts   # Auto-save, load history, confirm modal
│   └── useSettings.ts  # App settings state + save/reset
├── components/
│   ├── ui/             # Reusable atoms
│   │   ├── Input.tsx
│   │   ├── Select.tsx
│   │   ├── Section.tsx   # Collapsible accordion
│   │   ├── Toggle.tsx
│   │   └── AuthCard.tsx  # ActionButton + AuthCard
│   ├── hotel/
│   │   └── HotelCard.tsx # Hotel form with room type management
│   └── calculator/
│       └── ResultSection.tsx  # 3 result sections
├── pages/              # Tab views
│   ├── AuthPage.tsx        # Login + Register
│   ├── OperasionalTab.tsx  # Data dasar, handling, ops, muthowwif
│   ├── VisaTab.tsx         # Visa mode + fee/ujroh
│   ├── LATab.tsx           # Hotels + BRN + LA calculation
│   ├── CityTourTab.tsx     # City tour form
│   ├── TiketTab.tsx        # Tiket + domestik + haramain
│   ├── FinalTab.tsx        # Proposal doc + print/PDF + WA
│   ├── AdminPanel.tsx      # User management + email log
│   └── SettingsTab.tsx     # App settings form
├── App.tsx             # Root orchestrator (thin)
├── main.tsx            # Entry point
└── index.css           # Tailwind + custom CSS
```

---

## 🧮 Arsitektur Kalkulasi

```
FormData (state)
    │
    ▼
engines/calculations.ts → calculateAll(formData): CalculationResult
    │
    ├── opsHandling  = (totalOpsSar / totalPax) + hargaPaxSar
    ├── visa         = hppVisaGroup | (visaOnlyPrice × rate + armada/pax)
    ├── feeUjroh     = feePerPax / totalPax / sarToIdr
    ├── cityTour     = armada/pax + meals/pax + tiket + lainLain
    ├── tiket        = haramainTotal / totalPax
    └── landArrangement = getLaPerPax(formData)
                              │
                              └── engines/hotelEngine.ts
                                   └── Σ(hotelTotal) / totalPax + BRN/pax

grandTotalSar = opsHandling + visa + feeUjroh + cityTour + tiket + LA
grandTotalUsd = grandTotalSar × (sarToIdr / usdToIdr)
grandTotalIdr = grandTotalUsd × usdToIdr
```

**Prinsip:** Semua fungsi kalkulasi adalah **pure functions** — tidak ada side effects, tidak ada akses state/localStorage. Mudah di-unit test.

---

## 🗃️ Data Storage

Semua data disimpan di **localStorage** (tidak ada backend):

| Key | Konten |
|---|---|
| `umrah-estimate-history-v2` | 15 estimasi terakhir (auto-save 400ms debounce) |
| `tamima-auth-users-v1` | Daftar user terdaftar |
| `tamima-auth-session-v1` | Session user aktif |
| `tamima-app-settings-v1` | Konfigurasi aplikasi |
| `tamima-admin-email-log-v1` | Log notifikasi registrasi |

---

## 🖨️ Export & Output

### WhatsApp Draft
- Klik tombol **WhatsApp** di bottom bar atau tab Final
- Masukkan nomor WA tujuan
- Pesan terformat otomatis dengan semua komponen HPP

### Print / PDF
- Klik **Save PDF** atau **Print** di tab Final
- Popup window terbuka dengan dokumen A4
- Klik **Print** → pilih **Save as PDF** di dialog browser

### Single-File Build
Project dikonfigurasi dengan `vite-plugin-singlefile` — build menghasilkan satu file `index.html` dengan semua CSS dan JS ter-inline. Bisa dibagikan langsung tanpa hosting.

---

## 🔧 Development

### Type Check

```bash
npx tsc --noEmit
```

### Lint (jika ESLint dikonfigurasi)

```bash
npm run lint
```

### Environment Variables

Salin `.env.example` ke `.env`:

```bash
cp .env.example .env
```

| Variable | Default | Keterangan |
|---|---|---|
| `VITE_APP_NAME` | `PT. TAMIMA JAYA WISATA` | Nama aplikasi |
| `VITE_PRIMARY_ADMIN_EMAIL` | *(lihat .env.example)* | Email admin utama |

---

## 🚢 Deployment

### GitHub Pages (otomatis via CI)

Push ke branch `main` → GitHub Actions otomatis build dan deploy ke GitHub Pages.

Setup:
1. Fork/clone repo
2. Settings → Pages → Source: **GitHub Actions**
3. Push ke `main`

### Vercel / Netlify

```bash
npm run build
# Upload folder dist/ ke Vercel/Netlify
# atau connect repo langsung
```

Build command: `npm run build`
Output directory: `dist`

### Self-hosted / Shared Hosting

```bash
npm run build
# Upload dist/index.html ke hosting manapun
# Atau buka langsung di browser (offline-capable)
```

---

## 🧪 Testing

Unit test untuk kalkulasi engine:

```bash
# (coming soon — vitest setup)
npm run test
```

Fungsi yang prioritas untuk test:
- `calculateAll()` — main HPP engine
- `getLaPerPax()` — hotel + BRN per pax
- `getRoomTypeFinalPricing()` — HPP per tipe kamar
- `createInvoiceCode()` — format kode faktur

---

## 🤝 Contributing

Lihat [CONTRIBUTING.md](CONTRIBUTING.md) untuk panduan kontribusi.

---

## 📄 License

[MIT](LICENSE) © 2025 PT. Tamima Jaya Wisata
