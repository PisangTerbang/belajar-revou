---
inclusion: always
---

# Icon Clicker — Project Steering

Proyek ini adalah single-page web game "Icon Clicker".

Dokumen desain lengkap ada di:

#[[file:design.md]]

## Panduan Pengerjaan

- Semua kode berada dalam satu file `index.html` (HTML + CSS + JS)
- Jangan tambahkan framework atau dependency eksternal
- Ikuti color palette, tipografi, dan mekanik yang sudah didefinisikan di `design.md`
- Setiap perubahan mekanik game (upgrade baru, milestone baru, dll.) harus diperbarui juga di `design.md`
- State management menggunakan pola `recalcStats()` — jangan akumulasi langsung ke state, selalu hitung ulang dari upgrade level
