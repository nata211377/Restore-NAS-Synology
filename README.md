
# NAS RAID 5 Recovery – Synology (Mei 2025)

Dokumentasi proses recovery RAID 5 pada NAS Synology milik kantor, dilakukan secara manual setelah disk mengalami kegagalan (bad sector) dan proses rebuild gagal.

---

## 🧾 Ringkasan Kasus

- **NAS Model**: Synology
- **RAID Type**: RAID 5
- **Jumlah Disk**: 4 HDD
- **Total Data**: ±4 TB
- **Masalah Utama**: Disk 3 mengalami *degraded* dan *bad sector* berat

---

## ⚠️ Gejala

- Volume masih bisa diakses → segera backup
- Status: `Storage Pool 1 - Degraded`
- Gagal rebuild meskipun disk sudah diganti

![RAID degraded](screenshots/raid-degraded.png)

---

## 🛠️ Langkah-Langkah Detail

### 1. **Backup Data**

```bash
Login ke File Station atau gunakan rsync/scp:

- Buat backup seluruh folder penting ke USB HDD
- Backup juga permission:
  - permissions_backup.txt
  - file .dss dari setiap folder (jika menggunakan ACL)
```

---

### 2. **Diagnosa Disk**

- Masuk ke `Storage Manager > HDD/SSD`
- Cek SMART:
  - Disk 3 = Bad sector berat (degraded)
  - Disk 1 = Health 87% (berisiko)
- Catat ukuran semua disk (untuk penggantian nanti)

---

### 3. **Percobaan Rebuild (Gagal)**

- Ganti disk 3 → masuk Storage Manager → pilih “Repair”
- Proses tetap gagal → diduga karena:
  - Disk 1 juga sudah mulai rusak
  - RAID 5 hanya toleran terhadap 1 disk failure

---

### 4. **Solusi Akhir: Build Ulang RAID**

#### a. Hapus Storage Pool
- Masuk ke `Storage Pool > Action > Remove`

#### b. Buat RAID Baru
- Gunakan:
  - Disk 1 (lama) – SMART 100%
  - Disk 2 (lama) – SMART 100%
  - Disk 3 (baru)
- Pilih RAID 5 saat pembuatan pool

#### c. Buat Volume & Share Folder

---

### 5. **Restore Data & Permission**

- Salin semua data dari backup eksternal ke share folder baru
- Restore file `.dss` dan `permissions_backup.txt` jika digunakan
- Setting ulang permission folder (bisa lewat File Station atau Control Panel)

---

## ✅ Hasil Akhir

- RAID 5 berhasil dibuat ulang dengan disk sehat
- Data ±4TB berhasil direstore
- Semua share folder dan permission berfungsi kembali

![RAID healthy](screenshots/raid-recovered.png)

---

## 📁 Tips Dokumentasi

- Simpan hasil backup permission dan struktur folder di tempat terpisah
- Lakukan `Data Scrubbing` rutin di Synology
- Aktifkan email notifikasi untuk alert disk failure

---

## 🧠 Pembelajaran

- RAID ≠ Backup. Tetap harus ada backup eksternal
- Rebuild hanya mungkin jika disk lain benar-benar sehat
- Dokumentasi sangat penting untuk disaster recovery
