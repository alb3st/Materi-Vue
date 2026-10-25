# 📘 Panduan Vue.js 3 untuk Pemula

> Belajar Vue.js dari nol lewat satu proyek nyata: **Aplikasi To-Do List**.
> Cocok untuk mahasiswa semester 1 yang sudah mengenal dasar HTML, CSS, dan JavaScript.

**Versi:** Vue 3 (Options API) · **Diperbarui:** Oktober 2026 · **Penyusun:** Aldian Umbu

---

## 📋 Daftar Isi

0. [Cara Memakai Materi Ini](#0-cara-memakai-materi-ini)
1. [Apa itu Vue.js?](#1-apa-itu-vuejs)
2. [Instalasi dan Membuat Proyek](#2-instalasi-dan-membuat-proyek)
3. [Struktur Proyek dan Komponen](#3-struktur-proyek-dan-komponen)
4. [Menampilkan Data](#4-menampilkan-data)
5. [Data Binding](#5-data-binding)
6. [List Rendering](#6-list-rendering)
7. [Conditional Rendering](#7-conditional-rendering)
8. [Computed dan Methods](#8-computed-dan-methods)
9. [Watchers](#9-watchers)
10. [Props dan Event Antar Komponen](#10-props-dan-event-antar-komponen)
11. [Formulir dan Validasi](#11-formulir-dan-validasi)
12. [Peta Proyek To-Do List](#12-peta-proyek-to-do-list)
13. [Kesalahan yang Sering Terjadi](#13-kesalahan-yang-sering-terjadi)
14. [Kuis Singkat](#14-kuis-singkat)
15. [Perbedaan Vue 2 dan Vue 3](#15-perbedaan-vue-2-dan-vue-3)
16. [Glosarium](#16-glosarium)
17. [Referensi](#17-referensi)

---

## 0. Cara Memakai Materi Ini

**Yang perlu sudah Anda ketahui:**
- HTML dasar (tag, atribut)
- CSS dasar (class, warna)
- JavaScript dasar (variabel, fungsi, array, objek, `if`)

**Cara belajar yang disarankan:**
1. Baca penjelasan satu bab.
2. **Ketik sendiri** kodenya di VS Code (jangan hanya copy-paste).
3. Lihat hasilnya di browser.
4. Kerjakan latihan di akhir bab.

**Arti ikon:**

| Ikon | Arti |
|---|---|
| 💡 | Tips |
| ⚠️ | Hati-hati, sering salah di sini |
| 🧪 | Percobaan yang sebaiknya Anda coba sendiri |
| ✍️ | Latihan |

---

## 1. Apa itu Vue.js?

**Vue.js** adalah *framework* JavaScript untuk membuat tampilan website yang **interaktif**.

**Analogi sederhana:**
- Website biasa seperti **poster**: sekali dicetak, isinya tidak berubah.
- Website Vue seperti **papan skor digital**: begitu skor berubah, tampilan langsung ikut berubah tanpa Anda menyentuh layarnya.

### Ide utama Vue: Reaktivitas

Anda cukup **mengubah data**, lalu Vue **otomatis memperbarui tampilan**.

```
Data berubah  ──►  Vue mendeteksi  ──►  Tampilan ikut berubah
```

Tanpa Vue, Anda harus mencari elemen dengan `document.getElementById(...)` lalu mengubah isinya satu per satu. Dengan Vue, semua itu diurus otomatis.

### Dua gaya menulis Vue 3

Vue 3 punya dua gaya: **Options API** dan **Composition API**. Materi ini memakai **Options API** karena istilahnya mudah dipahami pemula: `data`, `computed`, `methods`, `watch`.

---

## 2. Instalasi dan Membuat Proyek

### Langkah 1: Pasang peralatan

1. **Node.js (versi LTS)** dari [nodejs.org](https://nodejs.org). Node.js adalah mesin yang menjalankan alat-alat pengembangan Vue di komputer Anda.
2. **VS Code** dengan ekstensi **Vue - Official**.

Cek di terminal bahwa Node.js sudah terpasang:

```bash
node -v
npm -v
```

Kalau muncul nomor versi, berarti berhasil.

> 💡 **npm** adalah "toko aplikasi" untuk paket JavaScript. Ia ikut terpasang bersama Node.js.

### Langkah 2: Buat proyek dengan Vite

**Vite** adalah alat yang membuat kerangka proyek dan menjalankan server pengembangan dengan sangat cepat.

```bash
npm create vite@latest todo-vue-demo -- --template vue
cd todo-vue-demo
npm install
npm run dev
```

Penjelasan tiap baris:

| Perintah | Fungsi |
|---|---|
| `npm create vite@latest ...` | Membuat folder proyek baru dari templat Vue |
| `cd todo-vue-demo` | Masuk ke folder proyek |
| `npm install` | Mengunduh semua paket yang dibutuhkan |
| `npm run dev` | Menjalankan server pengembangan |

Buka alamat yang tampil di terminal (biasanya `http://localhost:5173`).

> ⚠️ Kalau saat membuat proyek muncul pertanyaan tambahan, pilih opsi standar atau `No`.
> ⚠️ Pastikan Anda sudah `cd` ke folder proyek sebelum `npm install`. Salah folder adalah kesalahan paling umum.

### Langkah 3: Bersihkan file bawaan

Hapus file berikut karena tidak kita pakai:
- `src/components/HelloWorld.vue`
- `src/style.css`
- `src/assets/vue.svg`

### 🧪 Percobaan: Hot Reload

Biarkan `npm run dev` tetap berjalan. Ubah teks di `App.vue`, simpan (Ctrl + S), lalu lihat browser. Tampilan berubah **tanpa refresh**. Itulah *hot reload*.

---

## 3. Struktur Proyek dan Komponen

### Struktur folder yang kita tuju

```
todo-vue-demo/
├─ index.html              ← halaman dasar (hanya ada <div id="app">)
├─ package.json            ← daftar paket proyek
└─ src/
   ├─ main.js              ← pintu masuk aplikasi
   ├─ App.vue              ← komponen utama (induk)
   ├─ assets/
   │  └─ main.css          ← gaya (CSS) global
   └─ components/          ← komponen-komponen kecil
      ├─ TaskForm.vue
      ├─ TaskFilter.vue
      ├─ TaskAlert.vue
      ├─ TaskList.vue
      ├─ TaskItem.vue
      └─ TaskStats.vue
```

### Apa itu komponen?

**Komponen** adalah potongan tampilan yang bisa dipakai ulang.

**Analogi:** seperti **balok LEGO**. Satu balok punya bentuk dan fungsi sendiri. Dengan menyusun banyak balok, Anda membangun benda besar.

Dalam To-Do List kita:
- `TaskItem` = satu baris tugas
- `TaskList` = kumpulan `TaskItem`
- `App` = halaman yang menyusun semuanya

### Anatomi file `.vue`

Satu file `.vue` punya tiga bagian:

```vue
<template>
  <!-- 1. TAMPILAN (HTML) -->
  <h1>Halo, {{ nama }}!</h1>
</template>

<script>
// 2. LOGIKA (JavaScript)
export default {
  data() {
    return { nama: 'Budi' }
  }
}
</script>

<style>
/* 3. GAYA (CSS) */
h1 { color: teal; }
</style>
```

### File `main.js`: pintu masuk

```js
import { createApp } from 'vue'
import App from './App.vue'
import './assets/main.css'

createApp(App).mount('#app')
```

Artinya: *"Buat aplikasi dari komponen `App`, lalu tempelkan ke elemen `#app` di `index.html`."*

### Aturan penamaan

| Aturan | Contoh benar | Contoh kurang tepat |
|---|---|---|
| Nama file komponen **PascalCase** | `TaskItem.vue` | `taskitem.vue`, `task_item.vue` |
| Nama komponen **minimal dua kata** | `TaskItem` | `Item`, `List` |
| Nama menjelaskan fungsi | `TaskForm.vue` | `Komponen1.vue` |

> 💡 Kenapa dua kata? Supaya tidak bentrok dengan tag HTML bawaan seperti `<form>` atau `<list>`.

### Aturan emas komponen

> **Props turun, event naik.**
> Induk mengirim data ke anak lewat **props**. Anak memberi kabar ke induk lewat **event**.

Detailnya ada di [Bab 10](#10-props-dan-event-antar-komponen).

### ✍️ Latihan Bab 3
1. Buat proyek Vue baru dan jalankan.
2. Ubah isi `App.vue` menjadi `<h1>Halo, [nama Anda]!</h1>` dengan nama diambil dari `data()`.

---

## 4. Menampilkan Data

Ada tiga cara menampilkan data di template.

### Contoh kode

```vue
<template>
  <p>Mustache: {{ nama }}</p>
  <p>v-text: <span v-text="nim"></span></p>
  <p>v-html: <span v-html="teksTebal"></span></p>
</template>

<script>
export default {
  data() {
    return {
      nama: 'Budi Santoso',
      nim: '2024001',
      teksTebal: '<strong>Teks ini tebal</strong>'
    }
  }
}
</script>
```

### Penjelasan

| Cara | Fungsi | Catatan |
|---|---|---|
| `{{ data }}` (mustache / kumis) | Menampilkan data sebagai **teks biasa** | Paling sering dipakai |
| `v-text="data"` | Sama, tapi **mengganti seluruh isi elemen** | Mirip mustache |
| `v-html="data"` | Menampilkan data sebagai **HTML** (tag ikut dirender) | ⚠️ Berbahaya jika isinya dari pengguna |

> 💡 Di Vue 3, template boleh punya **lebih dari satu elemen di paling luar** (seperti contoh di atas).

### 🧪 Percobaan
Ganti `teksTebal` menjadi `'<script>alert(1)</script>'` lalu lihat hasil di mustache dan `v-html`. Mustache menampilkannya sebagai teks aman.

### ⚠️ Bahaya `v-html` (XSS)

Bayangkan pengguna mengetik ini di kolom tugas:

```html
<img src=x onerror="alert('Diretas!')">
```

- Dengan `{{ }}`: tampil sebagai **tulisan biasa**. Aman.
- Dengan `v-html`: browser **menjalankannya sebagai kode**. Penyerang bisa mencuri data.

> **Aturan:** pakai `v-html` hanya untuk teks yang **Anda tulis sendiri** di kode, **tidak pernah** untuk input pengguna.

Dalam proyek To-Do, `v-html` dipakai di `TaskAlert.vue` untuk menebalkan angka dari kode kita sendiri.

### ✍️ Latihan Bab 4
Tampilkan nama, NIM, dan program studi Anda memakai ketiga cara di atas. Mana yang tampil berbeda?

---

## 5. Data Binding

**Data binding** adalah menghubungkan data dengan tampilan.

### 5.1 `v-bind`: data → atribut (satu arah)

Mustache **tidak bisa** dipakai di dalam atribut. Gunakan `v-bind`, atau singkatannya `:`.

```vue
<template>
  <a :href="alamat">Kunjungi Vue</a>
  <p :class="{ merah: gagal }">Teks ini berubah warna</p>
  <div :style="{ borderColor: warna }" class="kotak">Kotak</div>
</template>

<script>
export default {
  data() {
    return {
      alamat: 'https://vuejs.org',
      gagal: true,
      warna: 'blue'
    }
  }
}
</script>
```

| Tulisan | Artinya |
|---|---|
| `:href="alamat"` | Isi `href` diambil dari data `alamat` |
| `:class="{ merah: gagal }"` | Class `merah` dipasang **hanya jika** `gagal` bernilai `true` |
| `:style="{ ... }"` | Gaya CSS dinamis |

> ⚠️ **Salah:** `<a href="{{ alamat }}">`. **Benar:** `<a :href="alamat">`.

### 5.2 `v-model`: data ↔ input (dua arah)

```vue
<template>
  <input v-model="nama" placeholder="Ketik nama...">
  <p>Halo, {{ nama }}!</p>

  <select v-model="warna">
    <option value="biru">Biru</option>
    <option value="hijau">Hijau</option>
  </select>
  <p>Pilihanmu: {{ warna }}</p>

  <label>
    <input type="checkbox" v-model="setuju">
    Saya setuju
  </label>
  <p>Setuju? {{ setuju }}</p>
</template>

<script>
export default {
  data() {
    return { nama: '', warna: 'biru', setuju: false }
  }
}
</script>
```

Ketik di input dan teks di bawahnya **ikut berubah seketika**. Itu artinya data dan tampilan saling terhubung.

```
v-bind  :  Data ───────► Tampilan
v-model :  Data ◄──────► Tampilan (input pengguna ikut mengubah data)
```

### Modifier `v-model`

| Modifier | Fungsi | Contoh |
|---|---|---|
| `.trim` | Membuang spasi di awal dan akhir | `v-model.trim="nama"` |
| `.number` | Mengubah isi input menjadi angka | `v-model.number="umur"` |
| `.lazy` | Data diperbarui saat selesai mengetik (blur/Enter) | `v-model.lazy="catatan"` |

### ✍️ Latihan Bab 5
Buat input warna (`<select>`) yang mengubah warna garis tepi sebuah kotak memakai `v-model` dan `:style`.

---

## 6. List Rendering

**List rendering** adalah menampilkan **banyak item dari array** tanpa menulis HTML berulang-ulang. Gunakan `v-for`.

### Contoh array

```vue
<template>
  <ul>
    <li v-for="(mhs, index) in daftarMhs" :key="mhs.nim">
      {{ index + 1 }}. {{ mhs.nama }} ({{ mhs.nim }})
    </li>
  </ul>
</template>

<script>
export default {
  data() {
    return {
      daftarMhs: [
        { nim: '2024001', nama: 'Ahmad Rizki' },
        { nim: '2024002', nama: 'Dewi Lestari' },
        { nim: '2024003', nama: 'Budi Santoso' }
      ]
    }
  }
}
</script>
```

Cara membaca: *"Untuk setiap `mhs` di dalam `daftarMhs`, buatkan satu `<li>`."* `index` adalah nomor urut mulai dari 0.

### Contoh objek

```vue
<template>
  <p v-for="(nilai, kunci) in kampus" :key="kunci">
    {{ kunci }}: {{ nilai }}
  </p>
</template>

<script>
export default {
  data() {
    return { kampus: { nama: 'Universitas Contoh', akreditasi: 'A' } }
  }
}
</script>
```

### Kenapa harus `:key`?

`:key` adalah **tanda pengenal unik** tiap item, seperti NIM pada mahasiswa. Dengan itu Vue tahu item mana yang berubah, bertambah, atau terhapus.

| | Contoh |
|---|---|
| ✅ Baik | `:key="mhs.nim"` atau `:key="task.id"` (nilai unik dan tetap) |
| ❌ Kurang baik | `:key="index"` (berubah jika urutan berubah atau ada yang dihapus) |

### ⚠️ Jangan gabung `v-if` dan `v-for` pada elemen yang sama

Kalau ingin menampilkan sebagian item saja, **saring dulu lewat computed** ([Bab 8](#8-computed-dan-methods)). Itulah yang dilakukan `filteredTasks` di To-Do List.

### ✍️ Latihan Bab 6
Tampilkan daftar 5 mata kuliah beserta SKS-nya dengan `v-for` dan `:key` yang benar.

---

## 7. Conditional Rendering

**Conditional rendering** adalah menampilkan sesuatu **hanya jika syarat terpenuhi**.

### 7.1 `v-if`, `v-else-if`, `v-else`

```vue
<template>
  <input v-model.number="nilai" type="number" placeholder="Nilai (0-100)">

  <p v-if="nilai >= 80">Nilai A 🎉</p>
  <p v-else-if="nilai >= 70">Nilai B 👍</p>
  <p v-else-if="nilai >= 60">Nilai C</p>
  <p v-else>Perlu belajar lagi 📚</p>
</template>

<script>
export default {
  data() {
    return { nilai: 75 }
  }
}
</script>
```

Cara kerjanya seperti `if / else if / else` di JavaScript. Hanya **satu** blok yang tampil.

> ⚠️ `v-else-if` dan `v-else` harus berada **tepat setelah** elemen `v-if`, tidak boleh ada elemen lain di antaranya.

### 7.2 `v-show`

```vue
<p v-show="nilai >= 60">✓ LULUS</p>
```

### Beda `v-if` dan `v-show`

| | `v-if` | `v-show` |
|---|---|---|
| Cara kerja | Elemen **dibuat / dihapus** dari halaman | Elemen **selalu ada**, hanya disembunyikan (`display: none`) |
| Biaya saat pertama tampil | **Rendah** (kalau false, tidak dibuat) | **Tinggi** (selalu dibuat) |
| Biaya saat sering di-toggle | **Tinggi** (buat dan hapus berulang) | **Rendah** (hanya ganti CSS) |
| Cocok untuk | Kondisi yang **jarang berubah** | Kondisi yang **sering berganti** |
| Bisa pakai `v-else`? | Ya | Tidak |

### 🧪 Percobaan dengan DevTools

1. Buka browser, tekan **F12**, lalu buka tab **Elements**.
2. Ubah nilai sehingga kondisi bergantian true/false.
3. Perhatikan: elemen `v-if` **hilang** dari daftar, sedangkan elemen `v-show` **tetap ada** dengan `style="display: none"`.

### Contoh di To-Do List
- `TaskAlert.vue`: rantai `v-if / v-else-if / v-else` untuk pesan jumlah tugas.
- `TaskList.vue`: `v-if` untuk daftar, `v-else` untuk tampilan "belum ada tugas".
- `TaskItem.vue`: `v-show` untuk tanda ✓ pada tugas yang selesai.

### ✍️ Latihan Bab 7
Buat tombol "Tampilkan/Sembunyikan Detail". Buat dua versi (`v-if` dan `v-show`), lalu bandingkan di DevTools.

---

## 8. Computed dan Methods

Keduanya berisi fungsi, tetapi tugasnya berbeda.

### Analogi

- **Computed** = **kalkulator yang menyimpan hasil**. Selama angkanya sama, ia tidak menghitung ulang.
- **Method** = **perintah**. Setiap kali dipanggil, ia bekerja lagi.

### Contoh

```vue
<template>
  <input v-model.number="panjang" type="number">
  <input v-model.number="lebar" type="number">

  <p>Luas (computed): {{ luas }}</p>
  <p>Luas (computed lagi): {{ luas }}</p>

  <p>Luas (method): {{ hitungLuas() }}</p>
  <p>Luas (method lagi): {{ hitungLuas() }}</p>

  <button @click="reset">Reset</button>
</template>

<script>
export default {
  data() {
    return { panjang: 10, lebar: 5 }
  },
  computed: {
    luas() {
      console.log('computed dihitung')
      return this.panjang * this.lebar
    }
  },
  methods: {
    hitungLuas() {
      console.log('method dihitung')
      return this.panjang * this.lebar
    },
    reset() {
      this.panjang = 0
      this.lebar = 0
    }
  }
}
</script>
```

### 🧪 Percobaan: membuktikan caching

Buka **F12 → Console**, lalu ubah angka di input.
- Pesan `computed dihitung` muncul **satu kali** walau `{{ luas }}` dipakai dua kali.
- Pesan `method dihitung` muncul **dua kali**.

### Kapan pakai yang mana?

| | Computed | Method |
|---|---|---|
| Tujuan | Menghasilkan **nilai turunan** dari data | Menjalankan **aksi** |
| Disimpan (cache)? | ✅ Ya | ❌ Tidak |
| Cara dipanggil di template | `{{ luas }}` (tanpa kurung) | `{{ hitungLuas() }}` atau `@click="reset"` |
| Boleh mengubah data? | ❌ Sebaiknya tidak | ✅ Ya |
| Contoh di To-Do | `activeTasks`, `filteredTasks` | `addTask`, `deleteTask` |

> 💡 **Rumus mudah:** kalau hasilnya hanya untuk **ditampilkan**, pakai computed. Kalau ada **sesuatu yang dilakukan** (mengubah data, mengirim, menghapus), pakai method.

### Computed di To-Do List

```js
computed: {
  totalTasks()     { return this.tasks.length },
  activeTasks()    { return this.tasks.filter(t => !t.completed).length },
  completedTasks() { return this.tasks.filter(t => t.completed).length },
  filteredTasks() {
    if (this.filter === 'active')    return this.tasks.filter(t => !t.completed)
    if (this.filter === 'completed') return this.tasks.filter(t => t.completed)
    return this.tasks
  }
}
```

### Methods di To-Do List

```js
methods: {
  addTask(text)  { this.tasks.push({ id: Date.now(), text, completed: false }) },
  toggleTask(id) {
    const task = this.tasks.find(t => t.id === id)
    if (task) task.completed = !task.completed
  },
  deleteTask(id) { this.tasks = this.tasks.filter(t => t.id !== id) }
}
```

### ✍️ Latihan Bab 8
Buat input berat dan tinggi badan, lalu hitung **BMI** dengan computed (`berat / (tinggi/100)²`). Tambah tombol "Reset" dengan method.

---

## 9. Watchers

**Watcher** = **pengawas** yang bereaksi ketika suatu data berubah.

**Analogi:** seperti **alarm gerak**. Begitu ada yang bergerak (data berubah), alarm langsung melakukan sesuatu.

### Contoh dasar

```vue
<template>
  <input v-model="teks" placeholder="Ketik sesuatu...">
  <p>{{ pesan }}</p>
</template>

<script>
export default {
  data() {
    return { teks: '', pesan: '' }
  },
  watch: {
    teks(baru, lama) {
      this.pesan = `Berubah dari "${lama}" menjadi "${baru}"`
    }
  }
}
</script>
```

Nama fungsi watcher **harus sama** dengan nama data yang diawasi. Fungsinya menerima nilai **baru** dan nilai **lama**.

### Opsi `deep` dan `immediate`

Dipakai di To-Do List:

```js
watch: {
  // Simpan ke localStorage setiap daftar tugas berubah
  tasks: {
    handler(newTasks) {
      localStorage.setItem('todo-demo-tasks', JSON.stringify(newTasks))
    },
    deep: true
  },

  // Ubah judul tab browser mengikuti jumlah tugas aktif
  activeTasks: {
    handler(count) {
      document.title = count > 0 ? `(${count}) To-Do List` : 'To-Do List'
    },
    immediate: true
  }
}
```

| Opsi | Artinya | Kapan dipakai |
|---|---|---|
| `deep: true` | Ikut memantau **perubahan di dalam** objek/array | Saat isi objek berubah (misalnya `task.completed`) |
| `immediate: true` | Watcher **langsung dijalankan sekali** saat aplikasi dimuat | Saat butuh nilai awal |

### 🧪 Percobaan
1. Hapus `deep: true`, lalu centang sebuah tugas. Penyimpanan **tidak terjadi**, karena Vue hanya melihat array-nya, bukan isi di dalamnya.
2. Kembalikan `deep: true`, centang lagi, lalu buka **F12 → Application → Local Storage**. Data tersimpan dan tetap ada setelah halaman di-refresh.

### Computed atau Watch?

| | Computed | Watch |
|---|---|---|
| Untuk | **Menghitung nilai** | **Menjalankan efek samping** |
| Contoh | Jumlah tugas aktif | Menyimpan ke localStorage, mengubah judul tab, memanggil API |

> 💡 Jika bisa diselesaikan dengan computed, pilih computed. Watcher untuk hal yang **"terjadi" akibat perubahan data**.

### ✍️ Latihan Bab 9
Buat input "umur". Tambahkan watcher yang menampilkan pesan "Anda belum dewasa" jika umur di bawah 18.

---

## 10. Props dan Event Antar Komponen

### Aturan emas: Props turun, event naik

```
        Induk (App.vue)
         │         ▲
  props  │         │  event ($emit)
 (data)  ▼         │  (kabar)
        Anak (TaskItem.vue)
```

- **Props** = data yang diberikan induk ke anak (seperti **titipan**).
- **Event** = kabar dari anak ke induk (seperti **laporan**).

### 10.1 Props

**Komponen anak** `KartuMahasiswa.vue`:

```vue
<template>
  <div class="kartu">
    <h3>{{ nama }}</h3>
    <p>NIM: {{ nim }}</p>
    <p>IPK: {{ ipk }} ({{ predikat }})</p>
  </div>
</template>

<script>
export default {
  name: 'KartuMahasiswa',
  props: {
    nama: { type: String, required: true },
    nim:  { type: String, required: true },
    ipk:  { type: Number, default: 0 }
  },
  computed: {
    predikat() {
      return this.ipk >= 3.5 ? 'Sangat Baik' : 'Baik'
    }
  }
}
</script>
```

**Induk** memakainya:

```vue
<template>
  <KartuMahasiswa nama="Budi" nim="2024001" :ipk="3.8" />
  <KartuMahasiswa nama="Dewi" nim="2024002" :ipk="3.4" />
</template>

<script>
import KartuMahasiswa from './components/KartuMahasiswa.vue'

export default {
  components: { KartuMahasiswa }
}
</script>
```

Satu komponen, dipakai dua kali dengan data berbeda. Itulah **reusability** (bisa dipakai ulang).

> ⚠️ **Kenapa `:ipk="3.8"` pakai titik dua?** Tanpa `:`, nilainya dianggap teks `"3.8"`, bukan angka.

> ⚠️ **Jangan ubah props langsung di komponen anak.** Props bersifat *baca saja*. Kalau perlu mengubah, minta induk lewat event.

### 10.2 Event dengan `$emit`

**Anak** `TombolSuka.vue`:

```vue
<template>
  <button @click="$emit('suka')">👍 Suka</button>
</template>

<script>
export default {
  name: 'TombolSuka',
  emits: ['suka']
}
</script>
```

**Induk:**

```vue
<template>
  <TombolSuka @suka="jumlah++" />
  <p>Jumlah suka: {{ jumlah }}</p>
</template>

<script>
import TombolSuka from './components/TombolSuka.vue'

export default {
  components: { TombolSuka },
  data() {
    return { jumlah: 0 }
  }
}
</script>
```

Alurnya: tombol diklik → anak memanggil `$emit('suka')` → induk mendengar lewat `@suka` → data `jumlah` bertambah → tampilan ikut berubah.

Event juga bisa **membawa data**: `$emit('hapus', task.id)`. Induk menerimanya di method: `@hapus="deleteTask"`.

> ⚠️ Selalu daftarkan event di `emits: [...]`. Kalau lupa, Console akan menampilkan peringatan.

### 10.3 `v-model` pada komponen

Dipakai pada `TaskFilter.vue` agar induk cukup menulis `<TaskFilter v-model="filter" />`.

```vue
<template>
  <button
    v-for="opsi in daftarOpsi"
    :key="opsi.nilai"
    :class="{ active: modelValue === opsi.nilai }"
    @click="$emit('update:modelValue', opsi.nilai)"
  >
    {{ opsi.label }}
  </button>
</template>

<script>
export default {
  name: 'TaskFilter',
  props: { modelValue: { type: String, required: true } },
  emits: ['update:modelValue'],
  data() {
    return {
      daftarOpsi: [
        { nilai: 'all', label: 'Semua' },
        { nilai: 'active', label: 'Aktif' },
        { nilai: 'completed', label: 'Selesai' }
      ]
    }
  }
}
</script>
```

Pasangan wajibnya: props bernama **`modelValue`** dan event bernama **`update:modelValue`**.

### Cara memakai komponen: 3 langkah

1. **Impor:** `import TaskItem from './TaskItem.vue'`
2. **Daftarkan:** `components: { TaskItem }`
3. **Pasang di template:** `<TaskItem :task="task" />`

### ✍️ Latihan Bab 10
Buat komponen `KartuBuku` dengan props `judul`, `penulis`, dan `tahun`. Pakai tiga kali di `App.vue` dengan data berbeda.

---

## 11. Formulir dan Validasi

**Validasi** adalah memeriksa isian pengguna sebelum diterima. Tujuannya mencegah data kosong atau salah.

### Prinsip formulir yang baik

1. Setiap kolom punya **`<label>`** yang jelas.
2. Beri **petunjuk** (`placeholder`) dan **batas** (`maxlength`).
3. Tampilkan **pesan error yang jelas** (apa yang salah dan cara memperbaikinya).
4. Pesan error **hilang** saat pengguna memperbaiki isiannya.

### Contoh dari `TaskForm.vue`

```vue
<template>
  <form novalidate @submit.prevent="submitTask">
    <label for="new-task">Tugas baru</label>

    <input
      id="new-task"
      v-model="newTask"
      type="text"
      maxlength="50"
      placeholder="Contoh: Belajar Vue.js"
      :class="{ invalid: errorMessage }"
    />
    <button type="submit">Tambah</button>

    <small>{{ newTask.length }}/50 karakter</small>
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
  </form>
</template>

<script>
export default {
  name: 'TaskForm',
  props: { existingTasks: { type: Array, default: () => [] } },
  emits: ['add-task'],
  data() {
    return { newTask: '', errorMessage: '' }
  },
  watch: {
    // Pesan error hilang begitu pengguna mulai mengetik lagi
    newTask() {
      if (this.errorMessage) this.errorMessage = ''
    }
  },
  methods: {
    validate(text) {
      if (text === '') return 'Tugas tidak boleh kosong.'
      if (text.length < 3) return 'Tugas minimal 3 karakter.'
      const duplikat = this.existingTasks.some(
        t => t.text.toLowerCase() === text.toLowerCase()
      )
      if (duplikat) return 'Tugas ini sudah ada di daftar.'
      return ''
    },
    submitTask() {
      const text = this.newTask.trim()
      const error = this.validate(text)
      if (error) {
        this.errorMessage = error
        return
      }
      this.$emit('add-task', text)
      this.newTask = ''
    }
  }
}
</script>
```

### Penjelasan bagian penting

| Bagian | Fungsi |
|---|---|
| `@submit.prevent` | Mencegah halaman reload saat form dikirim. Enter dan klik tombol sama-sama bekerja |
| `trim()` | Membuang spasi di awal dan akhir sebelum diperiksa dan disimpan |
| `validate()` | Mengembalikan pesan error, atau teks kosong jika valid |
| `:class="{ invalid: errorMessage }"` | Memberi garis merah pada input yang salah |
| `v-if="errorMessage"` | Pesan error hanya muncul jika ada error |
| `watch: newTask` | Menghapus pesan error ketika pengguna mulai mengetik |

### 🧪 Percobaan: uji validasinya

Coba kirim: (1) kosong, (2) hanya spasi `"   "`, (3) dua huruf `"ab"`, (4) tugas yang sudah ada dengan huruf besar/kecil berbeda, (5) 50 karakter penuh. Kasus mana yang paling sering terlewat oleh programmer pemula?

### ✍️ Latihan Bab 11
Buat form pendaftaran dengan **nama** (wajib, minimal 3 huruf) dan **email** (wajib, harus mengandung `@`). Tampilkan pesan error di bawah masing-masing kolom.

---

## 12. Peta Proyek To-Do List

Setelah memahami semua bab, lihat bagaimana semuanya menyatu.

### Alur data

```
                  App.vue
        (data: tasks, filter | computed | methods | watch)
           │ props ↓                         ↑ event
 ┌─────────┼───────────┬───────────┬─────────┴──────┐
 TaskForm  TaskFilter  TaskAlert  TaskList        TaskStats
                                     │
                                  TaskItem
```

### Peta konsep ke file

| Konsep | Ditemukan di |
|---|---|
| Struktur & komponen | Folder `components/`, `main.js`, `App.vue` |
| Mustache | `TaskItem.vue`, `TaskForm.vue` |
| `v-text` | `TaskStats.vue` |
| `v-html` | `TaskAlert.vue` |
| `v-bind` (`:class`, `:for`, `:checked`) | `TaskItem.vue`, `TaskFilter.vue` |
| `v-model` | `TaskForm.vue`, `App.vue` + `TaskFilter.vue` |
| `v-for` + `:key` | `TaskList.vue`, `TaskFilter.vue` |
| `v-if / v-else-if / v-else` | `TaskAlert.vue`, `TaskList.vue` |
| `v-show` | `TaskItem.vue` |
| Computed | `App.vue` |
| Methods | `App.vue`, `TaskForm.vue` |
| Watchers | `App.vue` (2), `TaskForm.vue` (1) |
| Props & `$emit` | Semua komponen anak |
| Formulir & validasi | `TaskForm.vue` |

---

## 13. Kesalahan yang Sering Terjadi

| Gejala | Penyebab umum | Solusi |
|---|---|---|
| `npm is not recognized` | Node.js belum terpasang atau terminal belum dibuka ulang | Pasang Node.js LTS, tutup dan buka lagi terminal |
| `npm install` gagal / tidak menemukan `package.json` | Salah folder | `cd` ke folder proyek dulu |
| `localhost:5173` tidak terbuka | `npm run dev` belum berjalan atau terhenti | Jalankan ulang, baca alamat yang tampil di terminal |
| `Failed to resolve component` | Lupa impor atau daftar di `components` | Lakukan 3 langkah memakai komponen (Bab 10) |
| Peringatan *"Component name should always be multi-word"* | Nama komponen satu kata | Ubah jadi dua kata (`TaskItem`) |
| Peringatan *"Extraneous non-emits event listeners"* | Lupa mendaftarkan `emits` | Tambahkan `emits: ['nama-event']` |
| `{{ }}` tampil mentah di atribut | Mustache dipakai di atribut | Pakai `:atribut="data"` |
| `Cannot read properties of undefined` | Data belum didefinisikan di `data()` | Tambahkan data tersebut |
| Watcher tidak jalan saat isi objek berubah | Tidak ada `deep: true` | Tambahkan `deep: true` |
| Peringatan mengubah props | Mengubah props langsung di anak | Kirim event ke induk |
| Teks `computed` tidak berubah | Memakai `luas()` seperti method, atau lupa `return` | Panggil tanpa kurung, pastikan ada `return` |
| `v-else` tidak bekerja | Ada elemen lain di antara `v-if` dan `v-else` | Letakkan tepat setelahnya |

> 💡 **Kebiasaan baik:** selalu buka **Console (F12)** saat belajar. Pesan error Vue biasanya menjelaskan masalahnya dengan cukup jelas.

---

## 14. Kuis Singkat

**1.** Directive mana yang menampilkan teks sebagai **HTML mentah**?
a) `v-text`  b) `v-html`  c) `v-model`  d) `v-bind`

**2.** Mana yang menyimpan (cache) hasilnya selama data tidak berubah?
a) Method  b) Watcher  c) Computed  d) Props

**3.** Arah aliran **props** adalah...
a) Anak → induk  b) Induk → anak  c) Dua arah  d) Antar anak

**4.** Kapan `deep: true` pada watcher dibutuhkan?
a) Saat data berupa angka  b) Saat memantau perubahan **di dalam** objek/array  c) Saat data kosong  d) Tidak pernah

**5.** Kenapa `v-for` sebaiknya memakai `:key` yang unik?
a) Supaya tampilan berwarna  b) Supaya Vue bisa melacak item yang berubah  c) Supaya kode lebih pendek  d) Tidak ada gunanya

**6.** Manakah yang elemennya tetap ada di DOM saat kondisinya false?
a) `v-if`  b) `v-show`  c) Keduanya  d) Tidak ada

<details>
<summary>Lihat jawaban</summary>

1. **b** `v-html`. (Ingat bahayanya untuk input pengguna.)
2. **c** Computed.
3. **b** Induk → anak.
4. **b** Saat memantau perubahan di dalam objek/array.
5. **b** Supaya Vue bisa melacak item yang berubah.
6. **b** `v-show`. Elemennya hanya disembunyikan dengan CSS.
</details>

---

## 15. Perbedaan Vue 2 dan Vue 3

Banyak tutorial lama di internet masih memakai Vue 2. Kenali perbedaannya supaya tidak bingung.

| Hal | Vue 2 (lama) | Vue 3 (materi ini) |
|---|---|---|
| Membuat aplikasi | `new Vue({ ... })` | `createApp(App).mount('#app')` |
| `data` | Boleh objek `data: { ... }` | **Harus fungsi** `data() { return { ... } }` |
| Komponen global | `Vue.component('nama', {...})` | `app.component('nama', {...})` atau file `.vue` |
| **Filter** `{{ x \| uppercase }}` | Ada (`Vue.filter`) | **Dihapus.** Pakai computed atau method |
| Elemen akar template | Hanya boleh **satu** | Boleh **banyak** |
| Mekanisme reaktivitas | `Object.defineProperty` | `Proxy` (lebih lengkap dan cepat) |
| `v-model` pada komponen | `value` + event `input` | `modelValue` + event `update:modelValue` |
| Status | Sudah tidak didukung (akhir masa pakai Desember 2023) | Versi yang aktif dikembangkan |

### Pengganti filter di Vue 3

Dulu (Vue 2): `{{ harga | rupiah }}`. Sekarang gunakan method atau computed:

```vue
<template>
  <p>Harga: {{ formatRupiah(harga) }}</p>
</template>

<script>
export default {
  data() {
    return { harga: 150000 }
  },
  methods: {
    formatRupiah(angka) {
      return 'Rp ' + angka.toLocaleString('id-ID')
    }
  }
}
</script>
```

---

## 16. Glosarium

| Istilah | Arti sederhana |
|---|---|
| **Framework** | Kerangka kerja berisi aturan dan alat siap pakai untuk membangun aplikasi |
| **Komponen** | Potongan tampilan yang bisa dipakai ulang (seperti balok LEGO) |
| **SFC** | *Single File Component*, file `.vue` berisi template, script, dan style |
| **Template** | Bagian HTML dari komponen |
| **Directive** | Atribut khusus Vue berawalan `v-` (misalnya `v-if`) |
| **Reaktivitas** | Tampilan otomatis ikut berubah saat data berubah |
| **Props** | Data kiriman dari induk ke anak |
| **Emit** | Cara anak mengirim kabar ke induk |
| **Computed** | Nilai turunan yang disimpan dan hanya dihitung ulang jika perlu |
| **Method** | Fungsi yang menjalankan aksi |
| **Watcher** | Pengawas yang bereaksi saat data berubah |
| **Hot reload** | Tampilan ter-update otomatis saat file disimpan |
| **Vite** | Alat pembuat proyek dan server pengembangan |
| **npm** | Pengelola paket JavaScript |
| **XSS** | Serangan dengan menyisipkan kode berbahaya lewat input |
| **DOM** | Struktur elemen halaman web yang dibaca browser |

---

## 17. Referensi

- [Dokumentasi resmi Vue.js](https://vuejs.org/)
- [Panduan Vue (Introduction)](https://vuejs.org/guide/introduction.html)
- [Vue Style Guide](https://vuejs.org/style-guide/)
- [Vite](https://vite.dev/)
- [Node.js](https://nodejs.org/)

---

**Diperbarui:** Oktober 2026 · **Versi:** 2.0 (Vue 3) · **Lisensi:** untuk keperluan pembelajaran
