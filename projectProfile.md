# 🍽️ PLANNING SISTEM KANTIN - LARAVEL

## 📋 Deskripsi Website

Website Sistem Kantin adalah aplikasi manajemen kantin sekolah/kampus yang memungkinkan admin dan petugas untuk mengelola menu makanan/minuman, kategori, dan transaksi penjualan. Sistem ini dilengkapi dengan autentikasi berbasis role untuk memisahkan akses antara Admin dan Petugas Kantin.

---

## 🎯 Tujuan Project

- Melatih kemampuan CRUD Laravel
- Implementasi autentikasi custom
- Praktik role-based access control
- Integrasi Laravel dengan Bootstrap 5
- Manajemen relasi database (one-to-many, many-to-many)

---

## 🔐 Fitur Berdasarkan Role

### **Admin (Full Access)**

- ✅ Dashboard dengan statistik (total penjualan, menu terlaris, pendapatan hari ini)
- ✅ CRUD User (username, password, role)
- ✅ CRUD Kategori Menu (nama kategori)
- ✅ CRUD Menu (nama, kategori, harga, stok, foto, deskripsi)
- ✅ CRUD Transaksi (lihat semua transaksi, tambah, edit, hapus)
- ✅ Laporan Penjualan (filter berdasarkan tanggal)

### **Petugas Kantin**

- ✅ Dashboard sederhana (menu hari ini, stok menipis)
- ✅ Lihat daftar menu dan stok
- ✅ CRUD Transaksi (input penjualan, lihat transaksi hari ini)
- ❌ Tidak bisa mengelola user, kategori, atau menu

---

## 🗄️ Struktur Database

### **Tabel: users**

| Field      | Type                  | Keterangan         |
| ---------- | --------------------- | ------------------ |
| id         | bigint (PK)           | Auto increment     |
| username   | varchar(50)           | Unique             |
| password   | varchar(255)          | Hash dengan bcrypt |
| role       | enum('admin','petugas') | Role user        |
| created_at | timestamp             | -                  |
| updated_at | timestamp             | -                  |

### **Tabel: kategoris**

| Field          | Type         | Keterangan                 |
| -------------- | ------------ | -------------------------- |
| id             | bigint (PK)  | Auto increment             |
| nama_kategori  | varchar(100) | Makanan/Minuman/Snack      |
| created_at     | timestamp    | -                          |
| updated_at     | timestamp    | -                          |

### **Tabel: menus**

| Field       | Type                      | Keterangan            |
| ----------- | ------------------------- | --------------------- |
| id          | bigint (PK)               | Auto increment        |
| kategori_id | bigint (FK)               | Relasi ke kategoris   |
| nama_menu   | varchar(100)              | Nama menu             |
| harga       | decimal(10,2)             | Harga satuan          |
| stok        | int                       | Jumlah stok           |
| foto        | varchar(255)              | Path foto (nullable)  |
| deskripsi   | text                      | Deskripsi (nullable)  |
| status      | enum('aktif','nonaktif')  | Status menu           |
| created_at  | timestamp                 | -                     |
| updated_at  | timestamp                 | -                     |

### **Tabel: transaksis**

| Field             | Type          | Keterangan          |
| ----------------- | ------------- | ------------------- |
| id                | bigint (PK)   | Auto increment      |
| user_id           | bigint (FK)   | Petugas yang input  |
| tanggal_transaksi | date          | Tanggal transaksi   |
| total_harga       | decimal(10,2) | Total transaksi     |
| created_at        | timestamp     | -                   |
| updated_at        | timestamp     | -                   |

### **Tabel: detail_transaksis**

| Field         | Type          | Keterangan             |
| ------------- | ------------- | ---------------------- |
| id            | bigint (PK)   | Auto increment         |
| transaksi_id  | bigint (FK)   | Relasi ke transaksis   |
| menu_id       | bigint (FK)   | Relasi ke menus        |
| jumlah        | int           | Qty pembelian          |
| harga_satuan  | decimal(10,2) | Harga saat transaksi   |
| subtotal      | decimal(10,2) | jumlah * harga_satuan  |
| created_at    | timestamp     | -                      |
| updated_at    | timestamp     | -                      |

### **Relasi Database**

```
users (1) ----< (N) transaksis
kategoris (1) ----< (N) menus
menus (1) ----< (N) detail_transaksis
transaksis (1) ----< (N) detail_transaksis
```

---

## 🛠️ Spesifikasi Teknis

### **Tech Stack**

- **Backend:** Laravel 10/11
- **Frontend:** Bootstrap 5 + jQuery (optional)
- **Database:** MySQL
- **Autentikasi:** Laravel Session (manual/custom auth)

### **Package/Library**

- **Laravel UI** (untuk template dasar) - optional
- **Intervention Image** (untuk upload & resize foto)
- **DataTables** (untuk tabel interaktif) - optional
- **Laravel Debugbar** (untuk development) - optional

### **Environment Requirements**

- PHP >= 8.1
- Composer
- MySQL/MariaDB
- Node.js & NPM (untuk compile Bootstrap)

---

## 🗺️ Halaman & Route

### **Public Routes**

```php
GET  /              → Redirect ke login
GET  /login         → Form login
POST /login         → Proses login
POST /logout        → Logout
```

### **Admin Routes (Middleware: auth, role:admin)**

```php
GET  /dashboard              → Dashboard admin
GET  /users                  → List users
GET  /users/create           → Form tambah user
POST /users                  → Simpan user
GET  /users/{id}/edit        → Form edit user
PUT  /users/{id}             → Update user
DELETE /users/{id}           → Hapus user

// Kategori
GET  /kategoris              → List kategori
GET  /kategoris/create       → Form tambah kategori
POST /kategoris              → Simpan kategori
GET  /kategoris/{id}/edit    → Form edit kategori
PUT  /kategoris/{id}         → Update kategori
DELETE /kategoris/{id}       → Hapus kategori

// Menu
GET  /menus                  → List menu
GET  /menus/create           → Form tambah menu
POST /menus                  → Simpan menu
GET  /menus/{id}/edit        → Form edit menu
PUT  /menus/{id}             → Update menu
DELETE /menus/{id}           → Hapus menu

// Transaksi
GET  /transaksis             → List semua transaksi
GET  /transaksis/create      → Form tambah transaksi
POST /transaksis             → Simpan transaksi
GET  /transaksis/{id}        → Detail transaksi
GET  /transaksis/{id}/edit   → Form edit transaksi
PUT  /transaksis/{id}        → Update transaksi
DELETE /transaksis/{id}      → Hapus transaksi

// Laporan
GET  /laporan                → Halaman laporan penjualan
```

### **Petugas Routes (Middleware: auth, role:petugas)**

```php
GET  /dashboard              → Dashboard petugas
GET  /menus                  → Lihat daftar menu (read-only)
GET  /transaksis             → List transaksi hari ini
GET  /transaksis/create      → Form tambah transaksi
POST /transaksis             → Simpan transaksi
GET  /transaksis/{id}        → Detail transaksi
```

---

## 🎨 Desain UI (Bootstrap 5)

### **Template Structure**

```
┌─────────────────────────────────────────┐
│  Navbar (Logo, Menu, Username, Logout)  │
├──────────┬──────────────────────────────┤
│          │                              │
│ Sidebar  │     Content Area             │
│          │   (Dynamic content)          │
│ - Dashboard                             │
│ - Master Data                           │
│ - Transaksi                             │
│ - Laporan                               │
│          │                              │
└──────────┴──────────────────────────────┘
```

### **Color Scheme**

- **Primary:** `#0d6efd` (Bootstrap blue)
- **Success:** `#198754` (untuk tombol tambah)
- **Danger:** `#dc3545` (untuk tombol hapus)
- **Warning:** `#ffc107` (untuk alert stok)
- **Info:** `#0dcaf0` (untuk informasi)

### **Components**

- **Cards** untuk statistik dashboard
- **Tables** dengan striped/hover untuk list data
- **Modals** untuk konfirmasi hapus
- **Forms** dengan validasi Bootstrap
- **Badges** untuk status menu (aktif/nonaktif)
- **Alerts** untuk notifikasi sukses/error

---

## 🔄 Alur Kerja (Workflow)

### **1. Login → Dashboard**

```
User Input (username + password)
  ↓
Validasi Autentikasi
  ↓
Cek Role (admin/petugas)
  ↓
Redirect ke Dashboard sesuai Role
```

### **2. Kelola Master Data (Admin Only)**

```
Admin Login
  ↓
Kelola User → Tambah/Edit/Hapus
Kelola Kategori → Tambah/Edit/Hapus
Kelola Menu → Tambah/Edit/Hapus (dengan upload foto)
```

### **3. Proses Transaksi**

```
Petugas/Admin → Halaman Transaksi
  ↓
Pilih Menu (dari dropdown/modal)
  ↓
Input Jumlah
  ↓
Sistem hitung Subtotal otomatis
  ↓
Tambah item lain (jika ada)
  ↓
Klik "Simpan Transaksi"
  ↓
Validasi Stok
  ↓
Kurangi Stok Menu
  ↓
Simpan ke Database
  ↓
Redirect dengan notifikasi sukses
```

### **4. Laporan (Admin Only)**

```
Admin → Halaman Laporan
  ↓
Filter berdasarkan Tanggal (dari - sampai)
  ↓
Tampilkan tabel transaksi
  ↓
Total Pendapatan
  ↓
Export PDF/Excel (optional)
```

---

## 📝 Fitur Tambahan (Opsional)

1. ✅ **Validasi Form** - Request validation di setiap input
2. ✅ **Alert Stok** - Notifikasi jika stok < 5
3. ✅ **Search & Filter** - Di halaman menu dan transaksi
4. ⭐ **Export PDF/Excel** - Untuk laporan penjualan
5. ✅ **Foto Default** - Jika menu tidak punya foto
6. ⭐ **Soft Delete** - Untuk data yang dihapus
7. ✅ **Pagination** - Di semua daftar data
8. ✅ **Middleware Auth** - Role-based access control
9. ⭐ **Chart/Grafik** - Statistik penjualan (Chart.js)
10. ⭐ **Print Struk** - Cetak nota transaksi

---

## 📊 Contoh Data Dashboard

### **Dashboard Admin**

- Total Penjualan Hari Ini: Rp 2.450.000
- Total Transaksi Hari Ini: 87 transaksi
- Menu Terlaris: Nasi Goreng (35 porsi)

### **Dashboard Petugas**

- Transaksi Hari Ini: 12 transaksi
- Total Penjualan: Rp 340.000
- Menu Stok Menipis:
  - Es Teh (3 gelas tersisa)
  - Nasi Goreng (4 porsi tersisa)

---

## ✅ Checklist Development

### **1. Setup Project**

- [v] Install Laravel (`composer create-project laravel/laravel sistem-kantin`)
- [v] Setup database di `.env`
- [v] Install Bootstrap 5 (`npm install bootstrap @popperjs/core`)
- [v] Compile assets (`npm run dev`)

### **2. Database**

- [v] Buat Migration (users, kategoris, menus, transaksis, detail_transaksis)
- [ ] Buat Model dengan relasi
- [ ] Buat Seeder (user admin default, kategori, menu sample)
- [ ] Run migration & seeder

### **3. Autentikasi**

- [ ] Buat LoginController
- [ ] Buat middleware `CheckRole`
- [ ] Setup route autentikasi
- [ ] Buat view login

### **4. Backend Development**

**Admin:**
- [ ] UserController (CRUD)
- [ ] KategoriController (CRUD)
- [ ] MenuController (CRUD + upload foto)
- [ ] TransaksiController (CRUD)
- [ ] LaporanController (filter & tampil)
- [ ] DashboardController

**Petugas:**
- [ ] DashboardController (khusus petugas)
- [ ] TransaksiController (terbatas)

### **5. Frontend Development**

**Layout:**
- [ ] Master layout (navbar + sidebar)
- [ ] Login page

**Admin Views:**
- [ ] Dashboard
- [ ] User (index, create, edit)
- [ ] Kategori (index, create, edit)
- [ ] Menu (index, create, edit)
- [ ] Transaksi (index, create, show)
- [ ] Laporan

**Petugas Views:**
- [ ] Dashboard
- [ ] Menu (index - read only)
- [ ] Transaksi (index, create, show)

### **6. Validasi**

- [ ] UserRequest (validasi form user)
- [ ] KategoriRequest (validasi form kategori)
- [ ] MenuRequest (validasi form menu + foto)
- [ ] TransaksiRequest (validasi form transaksi)

### **7. Testing**

- [ ] Test login sebagai admin
- [ ] Test login sebagai petugas
- [ ] Test CRUD users (admin only)
- [ ] Test CRUD kategori (admin only)
- [ ] Test CRUD menu (admin only)
- [ ] Test CRUD transaksi (admin & petugas)
- [ ] Test pengurangan stok otomatis
- [ ] Test middleware role-based access
- [ ] Test upload foto menu
- [ ] Test laporan penjualan

### **8. Final Touch**

- [ ] Tambahkan loading indicator
- [ ] Tambahkan confirmation dialog untuk hapus
- [ ] Responsive design check
- [ ] Error handling
- [ ] Optimasi query (eager loading)

---

## 💡 Tips Development

### **1. Mulai dari yang sederhana**

```
Login → Dashboard → CRUD Kategori → CRUD Menu → Transaksi → Laporan
```

### **2. Gunakan Laravel Resources**

```bash
php artisan make:model Kategori -mcr
# m = migration
# c = controller
# r = resource controller
```

### **3. Relasi Eloquent**

```php
// Model Menu
public function kategori() {
    return $this->belongsTo(Kategori::class);
}

// Model Kategori
public function menus() {
    return $this->hasMany(Menu::class);
}
```

### **4. Query Optimization**

```php
// Gunakan eager loading
$menus = Menu::with('kategori')->paginate(10);
```

### **5. Upload Foto**

```php
// Simpan di storage/app/public/menus
$path = $request->file('foto')->store('menus', 'public');

// Jangan lupa link storage
php artisan storage:link
```

---

## 🚀 Cara Menjalankan Project

### **1. Clone/Setup**

```bash
git clone <repo-url>
cd sistem-kantin
composer install
npm install
cp .env.example .env
php artisan key:generate
```

### **2. Database**

```bash
# Edit .env sesuai database kamu
php artisan migrate --seed
```

### **3. Storage**

```bash
php artisan storage:link
```

### **4. Run Server**

```bash
# Terminal 1
php artisan serve

# Terminal 2 (jika pakai Vite)
npm run dev
```

### **5. Login Default**

**Admin:**
- Username: `admin`
- Password: `admin123`

**Petugas:**
- Username: `petugas1`
- Password: `petugas123`

---

## 📚 Referensi

- [Laravel Documentation](https://laravel.com/docs)
- [Bootstrap 5 Documentation](https://getbootstrap.com/docs/5.3)
- [Laravel Eloquent Relationships](https://laravel.com/docs/eloquent-relationships)
- [Laravel Validation](https://laravel.com/docs/validation)

---

## 📞 Troubleshooting

### **Error: Class 'App\Models\Kategori' not found**

```bash
composer dump-autoload
```

### **Error: SQLSTATE[42S02]: Base table or view not found**

```bash
php artisan migrate:fresh --seed
```

### **Foto tidak muncul**

```bash
php artisan storage:link
```

### **Error 419 Page Expired**

Pastikan ada `@csrf` di setiap form

---

**Happy Coding! 🚀**

_Project ini dibuat untuk tujuan pembelajaran Laravel CRUD dan role-based authentication._