# Contributing Guide

Terima kasih atas minat Anda untuk berkontribusi! Berikut panduan untuk berkontribusi pada project ini.

## 🔄 Workflow

1. **Fork** repository ini
2. **Clone** fork Anda: `git clone https://github.com/YOUR_USERNAME/hpp-umroh-calculator.git`
3. **Buat branch** fitur/fix: `git checkout -b feat/nama-fitur` atau `git checkout -b fix/nama-bug`
4. **Commit** perubahan dengan format konvensional (lihat di bawah)
5. **Push** ke fork Anda: `git push origin feat/nama-fitur`
6. **Buat Pull Request** ke branch `main`

## 📝 Commit Convention

Gunakan format [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <deskripsi singkat>

[body opsional]
```

**Types:**
| Type | Kapan digunakan |
|---|---|
| `feat` | Fitur baru |
| `fix` | Bug fix |
| `refactor` | Refactoring tanpa perubahan fungsional |
| `style` | Perubahan styling/UI |
| `docs` | Perubahan dokumentasi |
| `test` | Penambahan/perbaikan test |
| `chore` | Perubahan build, deps, config |

**Contoh:**
```
feat(hotel): tambah tipe kamar Quint di hotel transit
fix(calc): perbaiki pembulatan fee ujroh per pax
docs(readme): update instruksi deployment
```

## 🏗️ Arsitektur — Aturan Penting

### 1. Jangan ubah calculation engine
File `src/engines/calculations.ts` dan `src/engines/hotelEngine.ts` berisi logika bisnis inti. **Jangan ubah formula kalkulasi** tanpa diskusi dan review terlebih dahulu.

### 2. Pure functions di engines/
Semua fungsi di `src/engines/` harus:
- Tidak memiliki side effects
- Tidak mengakses localStorage/state
- Menerima input → mengembalikan output
- Mudah di-unit test

### 3. Komponen harus React.memo
Semua komponen baru wajib menggunakan `React.memo` untuk performa.

### 4. Tidak ada duplikasi komponen
Sebelum membuat komponen baru, cek apakah sudah ada di:
- `src/components/ui/` — atom components
- `src/components/hotel/` — hotel-specific
- `src/components/calculator/` — result display

### 5. Type safety
- Tidak ada `any` kecuali benar-benar tidak bisa dihindari
- Semua props harus typed dengan interface
- Import type dengan `import type { ... }`

## 📁 Lokasi File Baru

| Apa | Di mana |
|---|---|
| Interface/type baru | `src/types/index.ts` |
| Konstanta/config | `src/constants/index.ts` |
| Helper pure function | `src/utils/` |
| localStorage operation | `src/services/storage.ts` |
| Kalkulasi baru | `src/engines/` |
| State management | `src/hooks/` |
| Komponen atom | `src/components/ui/` |
| Komponen halaman/tab | `src/pages/` |

## 🧪 Testing

Sebelum membuat PR, pastikan:

```bash
# Type check — harus 0 error
npx tsc --noEmit

# Build — harus berhasil
npm run build
```

## 🐛 Bug Reports

Gunakan GitHub Issues dengan template berikut:

```markdown
**Deskripsi bug:**
...

**Langkah reproduksi:**
1. ...
2. ...

**Expected behavior:**
...

**Actual behavior:**
...

**Browser/OS:**
...
```

## 💡 Feature Requests

Buka GitHub Issue dengan label `enhancement` dan jelaskan:
- Masalah yang ingin diselesaikan
- Solusi yang diusulkan
- Alternatif yang dipertimbangkan
