# // VALO-EXPERT — Sistem Pakar Pemilihan Agent & Strategi Valorant

Aplikasi **Sistem Pakar** berbasis web yang dirancang untuk merekomendasikan pemilihan *Agent* terbaik serta strategi komposisi tim pada game *First-Person Shooter* **Valorant**. Sistem menganalisis karakteristik Map, kekosongan Role tim, gaya bermain individu, hingga kompensasi rekan *party* menggunakan metode **Forward Chaining** dan **Certainty Factor (CF)**.

---

## Fitur Utama

* **Analisis Lobby Interaktif (`konsultasi.php`)**: Form input kondisi pertandingan dilengkapi *background* Map memudar (*fade*) dan pratinjau *tactical radar minimap* secara *real-time*.
* **Mesin Inferensi Forward Chaining + Certainty Factor (`hasil.php`)**:
  * Menampilkan **Top 3 Rekomendasi Agent** lengkap dengan persentase kecocokan, deskripsi, serta strategi *Attack* dan *Defense*.
  * Menyediakan **Jejak Aturan (Rule Trace)** untuk transparansi perhitungan bobot CF per kriteria.
  * Menggunakan pola **Post-Redirect-Get (PRG)** untuk mencegah duplikasi data riwayat saat halaman di-*refresh*.
* **Tactical Board Interaktif**: Modal perbesar minimap yang memungkinkan pengguna menempatkan pin posisi **Agent** maupun **4 Ability** masing-masing Agent dengan fitur *drag-and-drop* dan klik kanan untuk menghapus pin.
* **Cetak Laporan PDF (`@media print`)**: Tata letak cetak khusus berformat A4 bersih yang otomatis menyembunyikan elemen navigasi web dan membuka seluruh jejak aturan.
* **Integrasi Valorant Public API + Fallback Lokal**: Menarik *portrait* Agent, ikon wajah, ikon 4 Ability, *splash* Map, dan Minimap secara otomatis dari `valorant-api.com` dengan penyimpanan *cache* (`localStorage`) serta cadangan file lokal `.webp`.
* **Panel Admin CRUD (`admin/`)**: Proteksi *session* untuk mengelola Data Agent, Data Kriteria & Map, Basis Aturan (CF), serta memantau dan menghapus log riwayat konsultasi.

---

## Metode Penalaran

1. **Forward Chaining**: Mencocokkan fakta/kondisi lobby yang dipilih pengguna dengan basis aturan pada tabel `tb_aturan`.
2. **Certainty Factor (CF)**:
   * Nilai CF tiap aturan dihitung dengan rumus:
     $$CF_{aturan} = CF_{pakar} \times CF_{user}$$
   * Penggabungan beberapa aturan yang mengarah pada Agent yang sama menggunakan rumus kombinasi sekuensial:
     $$CF_{combine} = CF_{old} + CF_{new} \times (1 - CF_{old})$$
   * Hasil akhir dikonversi ke dalam bentuk persentase ($CF_{combine} \times 100\%$) dan diurutkan dari nilai tertinggi.

---

## Tech Stack

* **Backend**: PHP Native
* **Database**: MySQL / MariaDB
* **Frontend**: HTML5, CSS3 (Custom Tactical HUD), JavaScript (Vanilla), Bootstrap 5
* **External API**: [Valorant-API](https://valorant-api.com/) (dengan *fallback* aset lokal `.webp`)

---

## Struktur Folder

```text
sistem-pakar-valo/
├── admin/
│   ├── dashboard.php        # Ringkasan statistik & log riwayat konsultasi
│   ├── kelola_agent.php     # CRUD data Agent & strategi Attack/Defense
│   ├── kelola_aturan.php    # CRUD basis aturan & bobot CF pakar
│   ├── kelola_kriteria.php  # CRUD data Map & kriteria kondisi lobby
│   ├── login.php            # Autentikasi admin
│   ├── logout.php           # Akhiri sesi admin
│   └── navbar.php           # Komponen navigasi panel admin
├── assets/
│   ├── css/
│   │   └── style.css        # Tema Tactical HUD & aturan @media print
│   ├── img/
│   │   ├── agents/          # Fallback gambar lokal Agent (.webp)
│   │   └── maps/            # Fallback splash & minimap lokal (.webp)
│   └── js/
│       └── valo-assets.js   # Integrasi Valorant API, caching, & fallback
├── config/
│   └── koneksi.php          # Konfigurasi koneksi database MySQL
├── database/
│   └── db_sispak_valorant.sql
├── index.php                # Halaman beranda, statistik live, & riwayat
├── konsultasi.php           # Form analisis lobby & live map preview
├── hasil.php                # Mesin inferensi, hasil CF, & Tactical Board
└── README.md