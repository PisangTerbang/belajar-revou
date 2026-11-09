# 🎯 Icon Clicker — Design Document

## Overview

Single-page web game sederhana bertema "clicker". Pemain mengklik ikon utama untuk mengumpulkan poin, lalu menggunakan poin tersebut untuk membeli upgrade yang mempercepat pengumpulan poin.

---

## Tech Stack

| Layer      | Pilihan         | Alasan                                      |
|------------|-----------------|---------------------------------------------|
| Markup     | HTML5           | Single file, tidak perlu build tool         |
| Styling    | Vanilla CSS     | Tidak ada dependency, mudah dikustomisasi   |
| Logic      | Vanilla JS (ES6+)| Ringan, tidak perlu framework               |
| Persistensi| localStorage    | Simpan progress tanpa backend               |

---

## Visual Design

### Color Palette

| Token         | Nilai                        | Digunakan untuk             |
|---------------|------------------------------|-----------------------------|
| `bg-deep`     | `#1a1a2e`                    | Background utama            |
| `bg-mid`      | `#16213e`                    | Gradient tengah             |
| `bg-accent`   | `#0f3460`                    | Gradient bawah              |
| `text-primary`| `#ffffff`                    | Teks utama                  |
| `text-muted`  | `#a0c4ff`                    | Label, subtitle             |
| `gold`        | `#ffd700`                    | Skor, efek klik, highlight  |
| `glow-blue`   | `rgba(100, 200, 255, 0.4)`   | Box shadow tombol           |

### Typography

- Font: `'Segoe UI', sans-serif` (sistem, tidak perlu load)
- Heading: `2rem`, weight `700`
- Score: `1.6rem`, weight `700`
- Label: `0.7rem`, uppercase, letter-spacing `1px`

### Animasi

| Nama         | Durasi  | Fungsi                                      |
|--------------|---------|---------------------------------------------|
| `floatUp`    | 0.9s    | Teks `+N` mengambang saat klik              |
| `bump`       | 0.12s   | Scale skor saat bertambah                   |
| `clicked`    | 0.12s   | Tombol utama mengecil saat ditekan          |
| `float`      | 8–20s   | Partikel bergerak ke atas di background     |
| toast slide  | 0.4s    | Notifikasi milestone muncul dari atas       |

---

## Layout Structure

```
body (flex column, center)
├── .particles           ← background dekoratif (fixed)
├── .toast               ← notifikasi milestone (fixed top)
└── .container
    ├── h1               ← judul game
    ├── .subtitle        ← deskripsi singkat
    ├── .scoreboard
    │   ├── .score-card  ← Total Klik
    │   ├── .score-card  ← Per Klik
    │   └── .score-card  ← Auto / detik
    ├── .click-area
    │   └── .icon-btn    ← tombol klik utama (160×160px, bulat)
    ├── .upgrades-title
    ├── .upgrades
    │   └── .upgrade-btn × N
    └── .reset-btn
```

---

## Game Mechanics

### State

```js
state = {
  clicks:      number,   // total poin saat ini
  perClick:    number,   // poin per klik manual
  autoPerSec:  number,   // poin otomatis per detik
  upgrades:    { [id]: level }
}
```

### Loop

- **Manual click** → `clicks += perClick`
- **Auto tick** → `requestAnimationFrame`, hitung delta waktu → `clicks += autoPerSec * delta`
- **Render** → update DOM setiap ada perubahan state

### Upgrade System

Setiap upgrade punya:

| Field            | Deskripsi                                               |
|------------------|---------------------------------------------------------|
| `id`             | Identifier unik                                         |
| `baseCost`       | Harga level 1                                           |
| `costMultiplier` | Pengali harga per level (`cost = base × mult^level`)    |
| `maxLevel`       | Batas maksimum pembelian                                |
| `effect(state)`  | Fungsi yang memodifikasi state saat dibeli              |

Stats (`perClick`, `autoPerSec`) dihitung ulang dari nol setiap kali upgrade dibeli (`recalcStats()`), bukan ditumpuk langsung, agar tidak ada drift.

### Daftar Upgrade

| ID         | Icon | Nama     | Efek          | Base Cost | Multiplier | Max Lv |
|------------|------|----------|---------------|-----------|------------|--------|
| `cursor`   | 🖱️  | Cursor+  | +1 per klik   | 10        | ×1.5       | 20     |
| `lightning`| ⚡   | Petir    | +3 per klik   | 50        | ×1.6       | 15     |
| `robot`    | 🤖   | Auto Bot | +1 / detik    | 100       | ×1.8       | 10     |
| `rocket`   | 🚀   | Roket    | +5 / detik    | 500       | ×2.0       | 8      |
| `star`     | ⭐   | Bintang  | ×2 per klik   | 1,000     | ×3.0       | 5      |

### Milestone System

Saat total klik mencapai threshold, dua hal terjadi:
1. Toast notifikasi muncul selama 3 detik
2. Icon tombol utama berubah

| Threshold  | Pesan                        | Icon |
|------------|------------------------------|------|
| 100        | 100 Klik! Lumayan!           | 🌟   |
| 500        | 500 Klik! Keren!             | 🔥   |
| 1,000      | 1.000 Klik! Luar biasa!      | 💥   |
| 5,000      | 5.000 Klik! Gila sih!        | 🚀   |
| 10,000     | 10.000 Klik! Legend!         | 👑   |
| 50,000     | 50.000 Klik! Dewa!           | 🌈   |
| 100,000    | 100.000 Klik! GOAT!          | 🏆   |

---

## Persistensi

Data yang disimpan ke `localStorage` (key: `iconclicker`):

```json
{
  "clicks": 1234.5,
  "upgrades": {
    "cursor": 3,
    "robot": 1
  }
}
```

`perClick` dan `autoPerSec` **tidak** disimpan — dihitung ulang via `recalcStats()` saat load.

---

## Accessibility

- Semua tombol pakai elemen `<button>` native
- `aria-label` pada tombol utama dan upgrade
- Animasi visual murni dekoratif (tidak membawa informasi kritis)
- Kontras warna teks utama memenuhi WCAG AA pada background gelap

---

## File Structure

```
belajar-revou/
├── index.html    ← seluruh game (HTML + CSS + JS, single file)
└── design.md     ← dokumen desain ini
```

---

## Possible Future Improvements

- [ ] Sound effects (Web Audio API)
- [ ] Prestige / rebirth system
- [ ] Animasi partikel saat milestone
- [ ] Dark/light theme toggle
- [ ] Leaderboard via backend sederhana
- [ ] PWA (offline support, installable)
