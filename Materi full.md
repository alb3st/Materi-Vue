# 📖 Penjelasan Vue.js untuk Pemula (Mudah Dipahami)

## 🎯 Apa itu Vue.js?

Vue.js adalah **framework JavaScript** yang membuat website menjadi **interaktif dan dinamis**. 

**Analogi sederhana**: 
- Website biasa seperti **buku** → tidak bisa berubah
- Website Vue.js seperti **aplikasi smartphone** → bisa berubah sesuai input pengguna

---

## 1️⃣ Vue Component & Template

### 🤔 Apa itu Component?

**Component** = Bagian kecil website yang bisa dipakai ulang berkali-kali.

**Analogi**: Seperti **LEGO**
- Anda punya satu balok LEGO yang sudah jadi (component)
- Balok itu bisa dipasang di mana saja, berkali-kali
- Tidak perlu buat ulang dari nol

### 📝 Template

**Template** = Struktur HTML dari component tersebut.

**Contoh Kode:**
```javascript
Vue.component('kartu-mahasiswa', {
  template: `
    <div class="card">
      <h3>Nama Mahasiswa</h3>
      <p>NIM: 123456</p>
    </div>
  `
});
```

**Cara Pakai:**
```html
<kartu-mahasiswa></kartu-mahasiswa>
<kartu-mahasiswa></kartu-mahasiswa>
```

**Hasilnya**: Muncul 2 kartu mahasiswa yang sama!

### ✅ Keuntungan:
- **DRY (Don't Repeat Yourself)** - Tidak nulis kode yang sama berulang
- **Mudah maintenance** - Ubah 1 tempat, semua berubah
- **Kode terorganisir** - Rapi dan mudah dibaca

---

## 2️⃣ Menampilkan Data (Mustaches & Directives)

### 🎭 Mustaches `{{ }}`

**Mustaches** = Cara menampilkan data dari JavaScript ke HTML.

**Kenapa disebut Mustaches?** Karena bentuknya seperti kumis → `{{ }}`

**Contoh:**
```javascript
data: {
  nama: "Budi"
}
```

```html
<p>Halo, {{ nama }}!</p>
```

**Hasil di Browser:** Halo, Budi!

### 📌 Directive v-text

**Sama seperti mustaches**, tapi ditulis sebagai attribute HTML.

```html
<p v-text="nama"></p>
```

**Hasil:** Budi

### 🎨 Directive v-html

**Untuk menampilkan HTML** (bukan text biasa).

```javascript
data: {
  pesan: "<strong>Teks Tebal</strong>"
}
```

```html
<div v-html="pesan"></div>
```

**Hasil:** **Teks Tebal** (benar-benar tebal, bukan tulisan "<strong>")

### 🎓 Kapan Pakai yang Mana?

| Situasi | Gunakan |
|---------|---------|
| Menampilkan text biasa | `{{ }}` (paling umum) |
| Menampilkan di dalam tag | `v-text` |
| Menampilkan HTML | `v-html` |

---

## 3️⃣ Conditional Rendering (Pengandaian)

### 🔀 Apa itu Conditional?

**Conditional** = Menampilkan sesuatu **HANYA JIKA** kondisi tertentu terpenuhi.

**Analogi Sehari-hari:**
- **JIKA** hujan, **MAKA** bawa payung
- **JIKA** nilai >= 60, **MAKA** tampilkan "LULUS"

### 🎯 v-if, v-else-if, v-else

**Seperti IF-ELSE dalam programming.**

**Contoh:**
```javascript
data: {
  nilai: 85
}
```

```html
<div v-if="nilai >= 80">
  Nilai A - Excellent!
</div>
<div v-else-if="nilai >= 70">
  Nilai B - Good!
</div>
<div v-else-if="nilai >= 60">
  Nilai C - Pass
</div>
<div v-else>
  Nilai D/E - Gagal
</div>
```

**Yang Muncul di Browser:** Nilai A - Excellent!

**Cara Kerja:**
1. Cek nilai >= 80? **YA** → Tampilkan "Nilai A", **STOP**
2. (Tidak cek yang lain karena sudah ketemu)

### 👁️ v-show

**Mirip v-if**, tapi bedanya:

| v-if | v-show |
|------|--------|
| Element **dihapus** dari HTML jika false | Element **disembunyikan** (tetap ada di HTML) |
| Lebih hemat memori | Lebih cepat untuk toggle on/off |

**Contoh:**
```html
<div v-show="nilai >= 60">
  ✅ LULUS
</div>
```

**Kapan Pakai v-show?**
- Kalau element sering **ditampilkan/disembunyikan** (toggle)
- Contoh: Menu dropdown, popup

**Kapan Pakai v-if?**
- Kalau element **jarang berubah**
- Contoh: Tampilan berbeda untuk user login/logout

---

## 4️⃣ Data Binding

### 🔗 Apa itu Data Binding?

**Data Binding** = Menghubungkan data JavaScript dengan HTML.

**Analogi:** Seperti **kabel charger**
- Data JavaScript = listrik di stop kontak
- HTML = handphone
- Binding = kabel yang menghubungkan

### ➡️ One-Way Binding (v-bind atau `:`)

**Arah:** JavaScript → HTML (satu arah)

Data **hanya** dari JavaScript ke HTML. HTML tidak bisa mengubah data JavaScript.

**Contoh:**
```javascript
data: {
  warna: "merah"
}
```

```html
<div v-bind:style="{ color: warna }">
  Teks ini berwarna
</div>

<!-- Shorthand (cara singkat) -->
<div :style="{ color: warna }">
  Teks ini berwarna
</div>
```

**Hasil:** Teks berwarna merah

**Kegunaan v-bind:**
- Mengubah warna, ukuran, posisi
- Mengubah src gambar
- Mengubah href link
- Mengubah class CSS

### ⬅️➡️ Two-Way Binding (v-model)

**Arah:** JavaScript ↔ HTML (dua arah)

Data bisa **bolak-balik**:
- JavaScript ubah data → HTML ikut berubah
- User ubah di HTML → JavaScript ikut berubah

**Contoh:**
```javascript
data: {
  nama: ""
}
```

```html
<input v-model="nama" type="text">
<p>Halo, {{ nama }}!</p>
```

**Cara Kerja:**
1. User ketik "Budi" di input
2. Variable `nama` di JavaScript otomatis jadi "Budi"
3. Tampilan `<p>` otomatis jadi "Halo, Budi!"

**Tidak perlu coding apa-apa!** Vue otomatis sinkronisasi.

### 📊 Perbandingan

```
ONE-WAY (v-bind):
[JavaScript] ───────> [HTML]
    Mobil berjalan satu arah

TWO-WAY (v-model):
[JavaScript] <──────> [HTML]
    Mobil bisa maju-mundur
```

**Kapan Pakai Two-Way?**
- Input text
- Checkbox
- Radio button
- Select dropdown
- Textarea

**Kapan Pakai One-Way?**
- Menampilkan gambar
- Mengubah style/class
- Link/href
- Attribute HTML lainnya

---

## 5️⃣ Computed Properties & Methods

### 🧮 Computed Properties

**Computed** = Data yang **dihitung otomatis** dari data lain.

**Analogi:** Seperti **rumus Excel**
- Anda punya cell A1 = 10
- Anda punya cell B1 = 5
- Cell C1 = A1 + B1 → otomatis jadi 15
- Kalau A1 diubah jadi 20, C1 otomatis jadi 25

**Contoh:**
```javascript
data: {
  panjang: 10,
  lebar: 5
},
computed: {
  luas() {
    return this.panjang * this.lebar;
  }
}
```

```html
<p>Panjang: {{ panjang }}</p>
<p>Lebar: {{ lebar }}</p>
<p>Luas: {{ luas }}</p>
```

**Hasil:** Luas: 50

**Ubah panjang jadi 20** → Luas otomatis jadi 100!

### ⚙️ Methods

**Methods** = Fungsi yang **dipanggil manual** (harus diklik/ditrigger).

**Contoh:**
```javascript
data: {
  angka: 0
},
methods: {
  tambah() {
    this.angka++;
  }
}
```

```html
<p>Angka: {{ angka }}</p>
<button @click="tambah">Tambah</button>
```

**Cara Kerja:**
- Klik tombol → method `tambah()` jalan → angka bertambah

### 🆚 Computed vs Methods

| Computed | Methods |
|----------|---------|
| **Otomatis** jalan saat data berubah | **Manual** dipanggil |
| Hasilnya di-**cache** (disimpan) | Selalu **hitung ulang** |
| Untuk **kalkulasi** data | Untuk **aksi** (klik, submit) |
| `{{ luas }}` (tanpa `()`) | `@click="tambah()"` (pakai `()`) |

**Analogi:**
- **Computed** = Lampu sensor otomatis (deteksi gerakan → nyala sendiri)
- **Methods** = Lampu saklar manual (harus pencet sendiri)

**Kapan Pakai Computed?**
- Menghitung total harga
- Menghitung diskon
- Filter data
- Format data

**Kapan Pakai Methods?**
- Submit form
- Klik tombol
- Load data dari server
- Reset form

---

## 6️⃣ Watchers (Pengamat)

### 👀 Apa itu Watcher?

**Watcher** = Kode yang **memantau** perubahan data dan **bereaksi** saat data berubah.

**Analogi:** Seperti **CCTV**
- CCTV memantau rumah
- Ada gerakan → alarm berbunyi
- Watcher memantau data
- Data berubah → jalankan kode

**Contoh:**
```javascript
data: {
  pencarian: ""
},
watch: {
  pencarian(nilaiBaru, nilaiLama) {
    console.log("Pencarian berubah dari:", nilaiLama, "ke:", nilaiBaru);
    // Bisa jalankan kode lain, misal:
    // - Cari data ke database
    // - Tampilkan notifikasi
    // - Update data lain
  }
}
```

```html
<input v-model="pencarian" placeholder="Ketik untuk mencari...">
```

**Cara Kerja:**
1. User ketik "vue" → watcher jalan
2. User ubah jadi "vuejs" → watcher jalan lagi
3. Setiap perubahan → watcher selalu jalan

### 🎯 Kegunaan Watcher

1. **Search/Autocomplete**
   - User ketik → cari data ke server
   
2. **Validasi Real-time**
   - User isi form → cek apakah valid
   
3. **Sync Data**
   - Data A berubah → update data B

4. **Analytics/Logging**
   - Track perubahan user

### 🆚 Watcher vs Computed

| Watcher | Computed |
|---------|----------|
| Untuk **side effects** (API call, notifikasi) | Untuk **kalkulasi** sederhana |
| Bisa **async** (pakai setTimeout, fetch) | Harus **sync** |
| Tidak return value | Harus return value |

**Contoh Watcher (BENAR):**
```javascript
watch: {
  pencarian(nilai) {
    // Call API
    fetch('/api/search?q=' + nilai);
  }
}
```

**Contoh Computed (BENAR):**
```javascript
computed: {
  luasSegitiga() {
    return 0.5 * this.alas * this.tinggi;
  }
}
```

---

## 7️⃣ List Rendering (v-for)

### 🔁 Apa itu v-for?

**v-for** = Menampilkan data **berulang** dari array/object.

**Analogi:** Seperti **fotokopi**
- Anda punya 1 template kartu nama
- Anda punya data 100 orang
- v-for = mesin fotokopi yang bikin 100 kartu otomatis

### 📋 v-for dengan Array (Index-based)

**Array** = Daftar data berurutan (seperti list).

**Contoh:**
```javascript
data: {
  mahasiswa: [
    { nama: "Budi", nim: "001" },
    { nama: "Ani", nim: "002" },
    { nama: "Citra", nim: "003" }
  ]
}
```

```html
<div v-for="(mhs, index) in mahasiswa" :key="index">
  {{ index + 1 }}. {{ mhs.nama }} ({{ mhs.nim }})
</div>
```

**Hasil:**
```
1. Budi (001)
2. Ani (002)
3. Citra (003)
```

**Penjelasan Kode:**
- `mhs` = satu data mahasiswa per loop
- `index` = urutan (0, 1, 2, ...)
- `:key` = identitas unik (wajib untuk performa)

### 🗂️ v-for dengan Object (Name-based)

**Object** = Data dengan label/key.

**Contoh:**
```javascript
data: {
  kampus: {
    nama: "Universitas ABC",
    alamat: "Jl. Pendidikan No. 1",
    akreditasi: "A"
  }
}
```

```html
<div v-for="(value, key) in kampus" :key="key">
  <strong>{{ key }}:</strong> {{ value }}
</div>
```

**Hasil:**
```
nama: Universitas ABC
alamat: Jl. Pendidikan No. 1
akreditasi: A
```

**Penjelasan:**
- `value` = isi data (Universitas ABC, Jl. Pendidikan, A)
- `key` = nama label (nama, alamat, akreditasi)

### ⚠️ Pentingnya `:key`

**`:key` WAJIB** untuk v-for agar Vue bisa track element dengan benar.

```html
<!-- ❌ SALAH (tanpa key) -->
<div v-for="item in items">

<!-- ✅ BENAR (dengan key) -->
<div v-for="item in items" :key="item.id">
```

**Analogi:** Seperti **nomor antrian**
- Tanpa nomor → bingung siapa duluan
- Dengan nomor → jelas urutannya

### 🎯 Kegunaan v-for

1. **Daftar produk** (e-commerce)
2. **Daftar berita** (news portal)
3. **Chat messages**
4. **To-do list**
5. **Tabel data**

---

## 8️⃣ Filters (Formatting Data)

### 🎨 Apa itu Filter?

**Filter** = Fungsi untuk **mengubah tampilan** data tanpa mengubah data aslinya.

**Analogi:** Seperti **filter Instagram**
- Foto asli tetap sama
- Tampilan berubah (hitam-putih, vintage, dll)
- Data asli tetap sama
- Tampilan berubah (uppercase, format rupiah, dll)

### 📝 Cara Membuat Filter

**Contoh Filter Uppercase:**
```javascript
// Definisi filter (di luar Vue instance)
Vue.filter('uppercase', function(text) {
  return text.toUpperCase();
});

// Data
data: {
  nama: "budi santoso"
}
```

```html
<!-- Penggunaan filter dengan | (pipe) -->
<p>{{ nama | uppercase }}</p>
```

**Hasil:** BUDI SANTOSO

**Data asli `nama` tetap:** "budi santoso"

### 💰 Contoh Filter Currency (Rupiah)

```javascript
Vue.filter('rupiah', function(angka) {
  return 'Rp ' + angka.toLocaleString('id-ID');
});

data: {
  harga: 150000
}
```

```html
<p>{{ harga | rupiah }}</p>
```

**Hasil:** Rp 150.000

### 📅 Contoh Filter Date

```javascript
Vue.filter('tanggal', function(date) {
  const options = { year: 'numeric', month: 'long', day: 'numeric' };
  return new Date(date).toLocaleDateString('id-ID', options);
});

data: {
  waktu: new Date()
}
```

```html
<p>{{ waktu | tanggal }}</p>
```

**Hasil:** 30 November 2025

### 🔗 Chaining Filters (Kombinasi)

**Bisa pakai lebih dari 1 filter!**

```html
<p>{{ nama | lowercase | capitalize }}</p>
```

**Urutan:**
1. `nama` → "BUDI SANTOSO"
2. Filter `lowercase` → "budi santoso"
3. Filter `capitalize` → "Budi santoso"

### 🎯 Kegunaan Filter

1. **Format uang** (rupiah, dollar)
2. **Format tanggal** (dd/mm/yyyy, long date)
3. **Text transform** (uppercase, lowercase, capitalize)
4. **Truncate** (potong text panjang)
5. **Number format** (1000 → 1.000)

### ⚠️ Filter vs Computed

**Kapan Pakai Filter?**
- Untuk **format tampilan** sederhana
- **Reusable** (dipakai di banyak tempat)
- Tidak perlu logic kompleks

**Kapan Pakai Computed?**
- Untuk **kalkulasi** kompleks
- Pakai banyak data
- Logic rumit

---

## 9️⃣ Custom Components dengan Props

### 🧩 Apa itu Props?

**Props** = Data yang **dikirim** dari parent ke child component.

**Analogi:** Seperti **memberikan paket**
- Parent = Anda
- Child = Teman Anda
- Props = Paket yang Anda kirim

### 📦 Cara Kerja Props

**1. Buat Component dengan Props**
```javascript
Vue.component('kartu-mahasiswa', {
  props: ['nama', 'nim', 'jurusan'],
  template: `
    <div class="card">
      <h3>{{ nama }}</h3>
      <p>NIM: {{ nim }}</p>
      <p>Jurusan: {{ jurusan }}</p>
    </div>
  `
});
```

**2. Gunakan Component dengan Kirim Data**
```html
<kartu-mahasiswa 
  nama="Budi"
  nim="123456"
  jurusan="Teknik Informatika">
</kartu-mahasiswa>

<kartu-mahasiswa 
  nama="Ani"
  nim="789012"
  jurusan="Sistem Informasi">
</kartu-mahasiswa>
```

**Hasil:** 2 kartu dengan data berbeda!

### 🔢 Props dengan Tipe Data

**Props bisa berupa:**
- String (text)
- Number (angka)
- Boolean (true/false)
- Array (list)
- Object (data kompleks)

**Contoh Props Number:**
```javascript
Vue.component('harga-produk', {
  props: ['harga'],
  template: `<p>Rp {{ harga.toLocaleString() }}</p>`
});
```

```html
<!-- Pakai : (v-bind) untuk number, boolean, array, object -->
<harga-produk :harga="150000"></harga-produk>
```

### 🎯 Keuntungan Component + Props

1. **Reusable** - Pakai berkali-kali
2. **Dynamic** - Data bisa beda-beda
3. **Maintainable** - Mudah di-update
4. **Organized** - Kode rapi

**Contoh Real-world:**
- Card produk di e-commerce
- Post di social media
- Item di to-do list
- Notifikasi

---

## 🎓 Ringkasan Lengkap

### Urutan Belajar yang Ideal:

```
1. Mustaches {{ }} 
   ↓
2. v-if / v-show (conditional)
   ↓
3. v-bind (one-way binding)
   ↓
4. v-model (two-way binding)
   ↓
5. v-for (looping)
   ↓
6. Methods (fungsi)
   ↓
7. Computed (kalkulasi otomatis)
   ↓
8. Watchers (pengamat)
   ↓
9. Filters (formatting)
   ↓
10. Components + Props
```

### Quick Reference Table:

| Fitur | Syntax | Kegunaan |
|-------|--------|----------|
| Display data | `{{ data }}` | Tampilkan text |
| Conditional | `v-if="kondisi"` | Tampilkan jika true |
| Show/hide | `v-show="kondisi"` | Sembunyikan jika false |
| One-way bind | `:attribute="data"` | Data JS → HTML |
| Two-way bind | `v-model="data"` | Data JS ↔ HTML |
| Loop | `v-for="item in items"` | Ulang element |
| Method | `@click="method()"` | Panggil fungsi |
| Computed | `computed: { ... }` | Kalkulasi otomatis |
| Watcher | `watch: { ... }` | Pantau perubahan |
| Filter | `{{ data \| filter }}` | Format tampilan |
| Component | `Vue.component(...)` | Buat komponen |
| Props | `props: ['name']` | Terima data |

---

## 💡 Tips Mengajar untuk Orang Awam

### 1. **Mulai dari Analogi Real-life**
Selalu kaitkan dengan kehidupan sehari-hari:
- v-if = lampu yang nyala kalau gelap
- v-for = mesin fotokopi
- Props = paket kiriman

### 2. **Show, Don't Tell**
- Buka browser
- Ketik di input
- Tunjukkan perubahan real-time
- "Lihat, saat saya ketik, text di bawah langsung berubah!"

### 3. **Satu Konsep per Sesi**
Jangan mixing:
- Hari 1: Mustaches & v-if
- Hari 2: v-model & data binding
- Hari 3: v-for
- dst...

### 4. **Buat Latihan Sederhana**
Contoh project pemula:
- **To-do list** (v-model, v-for, methods)
- **Kalkulator** (v-model, computed)
- **Form pendaftaran** (v-model, v-if, validation)

### 5. **Encourage Experimentation**
- "Coba ubah warnanya"
- "Coba tambah data mahasiswa"
- "Coba ganti textnya"

### 6. **Troubleshooting Bersama**
Kalau error:
- Buka Console (F12)
- Baca pesan error bareng-bareng
- Debug step-by-step

---

## 🚀 Kesimpulan

Vue.js membuat website menjadi:
- ✅ **Interaktif** - Merespon user
- ✅ **Dynamic** - Data berubah real-time
- ✅ **Organized** - Kode terstruktur
- ✅ **Maintainable** - Mudah di-update

**Kunci sukses belajar Vue.js:**
1. **Praktek** lebih banyak dari teori
2. **Eksperimen** dengan kode
3. **Bertahap** jangan terburu-buru
4. **Baca dokumentasi** official Vue.js

**Selamat mengajar! 🎉**
