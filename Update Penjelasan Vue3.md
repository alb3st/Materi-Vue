# 🛠️ Membangun Aplikasi To-Do List dengan Vue.js 3

> Panduan **langkah demi langkah** membuat aplikasi To-Do List dari nol: instalasi, struktur folder, urutan pembuatan file, sampai pengujian.
> Setiap langkah menghasilkan tampilan yang bisa **dijalankan dan diuji** sebelum lanjut ke langkah berikutnya.

**Versi:** Vue 3 (Options API) · **Diperbarui:** November 2026 · **Penyusun:** Aldian Umbu

> 📚 Panduan ini pasangan dari [penjelasanVue3.md](penjelasanVue3.md). Baca konsepnya di sana, lalu praktikkan di sini.

---

## 🏷️ Cara Membaca Komentar pada Kode

Setiap komentar kode diberi tanda yang menunjuk ke **kriteria penilaian**:

| Tanda | Kriteria | Isi |
|---|---|---|
| `[1.1]` | Arsitektur dan struktur proyek | Folder, penamaan file, komponen, template |
| `[1.2]` | Data binding, directive, array, filter | Mustache, `v-text`, `v-html`, `v-bind`, `v-model`, `v-for`, filter teks |
| `[1.3]` | Conditional | `v-if`, `v-else-if`, `v-else`, `v-show` |
| `[1.4]` | Computed dan methods | Properti turunan dan aksi |
| `[1.5]` | Watchers | Pemantau perubahan data (minimal 2) |
| `[1.6]` | Formulir, validasi, event mouse dan keyboard | Input, pesan error, klik, hover, Enter, Esc |

Contoh: komentar `// [1.4] computed` berarti baris itu adalah bukti untuk kriteria 1.4.

---

## 📋 Daftar Isi

- [0. Gambaran Proyek](#0-gambaran-proyek)
- [Langkah 1: Persiapan dan Instalasi](#langkah-1-persiapan-dan-instalasi)
- [Langkah 2: Folder dan CSS](#langkah-2-folder-dan-css)
- [Langkah 3: Filter Teks dan main.js](#langkah-3-filter-teks-dan-mainjs)
- [Langkah 4: Kerangka App.vue](#langkah-4-kerangka-appvue)
- [Langkah 5: Komponen TaskStats](#langkah-5-komponen-taskstats)
- [Langkah 6: Komponen TaskItem dan TaskList](#langkah-6-komponen-taskitem-dan-tasklist)
- [Langkah 7: Centang dan Hapus](#langkah-7-centang-dan-hapus)
- [Langkah 8: Komponen TaskFilter](#langkah-8-komponen-taskfilter)
- [Langkah 9: Komponen TaskAlert](#langkah-9-komponen-taskalert)
- [Langkah 10: Komponen TaskActions](#langkah-10-komponen-taskactions)
- [Langkah 11: Komponen TaskForm](#langkah-11-komponen-taskform)
- [Langkah 12: Mouse dan Keyboard di TaskItem](#langkah-12-mouse-dan-keyboard-di-taskitem)
- [Langkah 13: Watchers dan Penyimpanan Data](#langkah-13-watchers-dan-penyimpanan-data)
- [Langkah 14: Pengujian Akhir](#langkah-14-pengujian-akhir)
- [15. Memahami Computed, Methods, dan Watch](#15-memahami-computed-methods-dan-watch)
- [16. Memahami Tanda : dan @](#16-memahami-tanda--dan-)
- [17. Peta Konsep dan Kriteria Penilaian](#17-peta-konsep-dan-kriteria-penilaian)
- [18. Kesalahan yang Sering Terjadi](#18-kesalahan-yang-sering-terjadi)
- [19. Latihan Pengembangan](#19-latihan-pengembangan)

---

## 0. Gambaran Proyek

### Fitur aplikasi

- Menambah tugas baru (dengan validasi) lewat **klik** atau tombol **Enter**
- Mencentang tugas selesai, menghapus tugas, dan **mengedit** tugas
- Aksi massal: **tandai semua selesai** dan **hapus yang selesai**
- Filter **Semua / Aktif / Selesai**
- Teks tugas **diformat otomatis** (huruf pertama kapital, teks panjang dipotong, tanggal berbahasa Indonesia)
- Pesan otomatis sesuai jumlah tugas, dan statistik
- Data **tersimpan** walau halaman di-refresh
- Judul tab browser menunjukkan jumlah tugas aktif

### Struktur folder akhir

```
todo-vue-demo/
├─ index.html
├─ package.json
└─ src/
   ├─ main.js                  ← titik masuk aplikasi
   ├─ App.vue                  ← komponen induk
   ├─ assets/
   │  └─ main.css              ← gaya global
   ├─ utils/
   │  └─ filters.js            ← fungsi pemformat teks ("filter")
   └─ components/
      ├─ TaskForm.vue          ← formulir tambah tugas
      ├─ TaskFilter.vue        ← tombol filter
      ├─ TaskAlert.vue         ← pesan kondisional
      ├─ TaskActions.vue       ← aksi massal
      ├─ TaskList.vue          ← daftar + tampilan kosong
      ├─ TaskItem.vue          ← satu baris tugas
      └─ TaskStats.vue         ← statistik
```

### Alur data: props turun, event naik

```
                       App.vue
        (data: tasks, filter | computed | methods | watch)
          │ props ↓                              ↑ event
 ┌────────┼───────────┬───────────┬───────────┬──┴────────┐
 TaskForm TaskFilter  TaskAlert  TaskActions  TaskList  TaskStats
                                                 │
                                              TaskItem
```

`App.vue` adalah **satu-satunya** pemegang data (`tasks` dan `filter`). Komponen anak hanya menampilkan data dan melapor lewat event.

### Urutan pembuatan file

Kita membangun dari yang **paling sedikit bergantung** pada file lain.

| Urutan | File | Langkah | Alasan |
|---|---|---|---|
| 1 | `assets/main.css` | 2 | Gaya dibutuhkan semua komponen |
| 2 | `utils/filters.js` | 3 | Dipakai komponen lain, tidak bergantung pada apa pun |
| 3 | `main.js` | 3 | Mendaftarkan filter dan memuat CSS |
| 4 | `App.vue` (kerangka) | 4 | Wadah dan data |
| 5 | `TaskStats.vue` | 5 | Paling sederhana: hanya props |
| 6 | `TaskItem.vue` (versi dasar), `TaskList.vue` | 6 | Menampilkan daftar |
| 7 | Methods centang dan hapus di `App.vue` | 7 | Menghidupkan tombol |
| 8 | `TaskFilter.vue` | 8 | Butuh daftar yang sudah tampil |
| 9 | `TaskAlert.vue` | 9 | Memakai computed dari Langkah 5 |
| 10 | `TaskActions.vue` | 10 | Aksi massal pada array |
| 11 | `TaskForm.vue` | 11 | Komponen paling kompleks |
| 12 | `TaskItem.vue` (versi final) | 12 | Menambah mouse, keyboard, dan edit |
| 13 | Watchers di `App.vue` | 13 | "Penyempurna" setelah semua fitur jalan |

### Aturan membaca langkah

- **Template `App.vue`** ditulis **lengkap** di setiap langkah (isinya pendek).
- **Script `App.vue`** hanya ditulis bagian yang **ditambahkan**. Kode lengkap ada di [Langkah 13](#langkah-13-watchers-dan-penyimpanan-data) untuk dicocokkan.
- Komponen anak ditulis **lengkap** sehingga bisa langsung disalin.
- **Jalankan dan uji** setiap langkah sebelum lanjut. Jangan lanjut kalau Console (F12) masih menampilkan error.

> 💡 Biarkan `npm run dev` berjalan terus. Setiap kali Anda menyimpan file (Ctrl + S), browser memperbarui tampilan otomatis.

---

## Langkah 1: Persiapan dan Instalasi

**Tujuan:** punya proyek Vue yang bisa dijalankan.

### 1.1 Pasang peralatan

1. **Node.js (LTS)** dari [nodejs.org](https://nodejs.org)
2. **VS Code** dengan ekstensi **Vue - Official** (nonaktifkan Vetur jika ada)

Cek di terminal:

```bash
node -v
npm -v
```

Kalau muncul nomor versi, berhasil.

### 1.2 Buat proyek dengan Vite

```bash
npm create vite@latest todo-vue-demo -- --template vue
cd todo-vue-demo
npm install
npm run dev
```

| Perintah | Fungsi |
|---|---|
| `npm create vite@latest ...` | Membuat folder proyek dari templat Vue |
| `cd todo-vue-demo` | Masuk ke folder proyek |
| `npm install` | Mengunduh paket yang dibutuhkan |
| `npm run dev` | Menjalankan server pengembangan |

Buka alamat yang tampil di terminal (biasanya `http://localhost:5173`).

> ⚠️ Versi Vite yang baru bisa menanyakan beberapa opsi tambahan. Pilih opsi standar atau `No`.
> ⚠️ Pastikan sudah `cd todo-vue-demo` sebelum `npm install`. Salah folder adalah kesalahan paling umum.

### 1.3 Buka di VS Code

Buka **terminal baru** (biarkan terminal `npm run dev` tetap jalan), lalu:

```bash
code todo-vue-demo
```

Atau lewat menu **File → Open Folder**.

### 1.4 Hapus file bawaan template

Hapus tiga file ini karena tidak dipakai:

- `src/components/HelloWorld.vue`
- `src/style.css`
- `src/assets/vue.svg`

> ⚠️ Setelah penghapusan ini, halaman bisa menjadi putih dan Console menampilkan error `Failed to resolve import`. Itu wajar, karena `main.js` dan `App.vue` bawaan masih memanggil file yang sudah dihapus. Kita perbaiki di Langkah 3 dan 4.

### ✅ Hasil yang diharapkan
Halaman bawaan Vite tampil di browser sebelum file dihapus.

---

## Langkah 2: Folder dan CSS

**Tujuan:** menyiapkan folder dan gaya tampilan.
**Kriteria:** `[1.1]` struktur folder.

### 2.1 Buat folder `utils`

Di dalam `src/`, buat folder baru bernama `utils`. Di VS Code: klik kanan pada folder `src` → **New Folder** → ketik `utils`. Atau lewat terminal:

```bash
mkdir src/utils
```

Folder `components` dan `assets` sudah ada dari templat.

### 2.2 Buat `src/assets/main.css`

Isi file ini dengan kode berikut. Ini gaya untuk **seluruh** aplikasi. Nama class di sini akan dipakai di langkah-langkah berikutnya.

```css
/* ===== Warna yang dipakai di seluruh aplikasi ===== */
:root {
  --primary: #667eea;
  --primary-dark: #5568d3;
  --danger: #e53e3e;
  --success: #2f855a;
  --warning: #b7791f;
}
* { box-sizing: border-box; }

body {
  margin: 0;
  min-height: 100vh;
  padding: 40px 16px;
  font-family: Arial, sans-serif;
  background: linear-gradient(135deg, #ead666 0%, #a24b68 100%);
}
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 30px;
  background: #fff;
  color: #222;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}
h1 { text-align: center; margin-top: 0; }
label { display: block; font-weight: bold; margin-bottom: 6px; }

/* ===== Tombol umum ===== */
button {
  padding: 10px 16px; font-size: 15px; cursor: pointer;
  background: var(--primary); color: #fff; border: 0; border-radius: 5px;
}
button:hover { background: var(--primary-dark); }
button:disabled { opacity: 0.5; cursor: not-allowed; pointer-events: none; }

/* ===== Formulir (TaskForm) ===== */
.input-row { display: flex; gap: 8px; }
input[type="text"] {
  flex: 1; padding: 10px; font-size: 16px;
  border: 2px solid #ddd; border-radius: 5px;
}
input[type="text"].invalid { border-color: var(--danger); }
.counter { color: #777; }
.counter.warn { color: var(--warning); font-weight: bold; }
.hint { display: block; color: #888; margin-top: 4px; font-size: 12px; }
.error { color: var(--danger); margin: 6px 0 0; }

/* ===== Filter (TaskFilter) ===== */
.filter-buttons { display: flex; gap: 8px; margin: 16px 0; }
.filter-buttons button { background: #e2e8f0; color: #333; }
.filter-buttons button:hover { background: #cbd5e0; }
.filter-buttons button.active { background: var(--primary); color: #fff; }
.filter-buttons button.active:hover { background: var(--primary-dark); }

/* ===== Pesan (TaskAlert) ===== */
.alert { padding: 10px 14px; border-radius: 6px; margin-bottom: 16px; }
.alert-info { background: #ebf8ff; color: #2c5282; }
.alert-success { background: #f0fff4; color: var(--success); }
.alert-warning { background: #fffaf0; color: var(--warning); }

/* ===== Aksi massal (TaskActions) ===== */
.actions { display: flex; gap: 8px; margin-bottom: 12px; flex-wrap: wrap; }
.btn-secondary { background: #e2e8f0; color: #333; }
.btn-secondary:hover { background: #cbd5e0; }
.btn-outline-danger {
  background: #fff; color: var(--danger); border: 1px solid var(--danger);
}
.btn-outline-danger:hover { background: var(--danger); color: #fff; }

/* ===== Daftar dan baris tugas (TaskList, TaskItem) ===== */
ul { list-style: none; padding: 0; margin: 0; }
.task-item {
  display: flex; align-items: center; gap: 10px;
  padding: 10px; border-bottom: 1px solid #eee;
}
.task-item.hovered { background: #f7fafc; }
.task-body { flex: 1; display: flex; flex-direction: column; }
.task-text { flex: 1; margin: 0; font-weight: normal; cursor: pointer; }
.task-body .task-text { flex: none; }
.task-item.completed .task-text { text-decoration: line-through; color: #999; }
.task-date { color: #888; font-size: 12px; }
.done-mark { color: var(--success); font-size: 13px; }
input[type="text"].edit-input {
  flex: 1; padding: 6px 8px; font-size: 15px;
  border: 2px solid var(--primary); border-radius: 5px;
}
.edit-btn { background: #e2e8f0; color: #333; padding: 6px 12px; font-size: 13px; }
.edit-btn:hover { background: #cbd5e0; }
.delete-btn { background: var(--danger); padding: 6px 12px; font-size: 13px; }
.delete-btn:hover { background: #c53030; }
.empty-state { text-align: center; color: #777; padding: 20px 0; }

/* ===== Statistik (TaskStats) ===== */
.stats {
  margin-top: 16px; padding-top: 12px;
  border-top: 2px solid #eee; text-align: center;
}
```

> 💡 CSS bukan fokus pelajaran Vue, jadi cukup disalin. Warna bisa diubah di bagian `:root`.

### ✅ Hasil yang diharapkan
Belum ada perubahan tampilan. File CSS baru dipakai setelah dimuat di `main.js` (Langkah 3).

---

## Langkah 3: Filter Teks dan main.js

**Tujuan:** membuat fungsi pemformat teks dan mendaftarkannya.
**Kriteria:** `[1.2]` filter sebagai formatting teks, `[1.1]` titik masuk aplikasi.

### Apa itu "filter" dan kenapa berbeda di Vue 3?

Di Vue 2, ada sintaks khusus untuk memformat teks:

```
{{ teks | capitalize }}        ← Vue 2 (sudah dihapus di Vue 3)
```

Di Vue 3 sintaks pipe `|` **tidak ada lagi**. Penggantinya: tulis fungsi pemformat biasa, daftarkan sebagai **properti global**, lalu panggil seperti fungsi:

```
{{ $filters.capitalize(teks) }}   ← Vue 3
```

Tujuannya sama (merapikan tampilan teks), hanya cara menulisnya yang berbeda.

### 3.1 Buat `src/utils/filters.js`

```js
// [1.1] File ini ada di folder utils/ karena berisi fungsi bantu murni
//       (bukan komponen). Memisahkannya membuat kode lebih rapi dan bisa dipakai ulang.
// [1.2] Kumpulan "filter": fungsi pemformat teks untuk ditampilkan di template.

// [1.2] Filter 1: huruf pertama menjadi kapital.
//       "belajar vue" -> "Belajar vue"
export function capitalize(value) {
  if (!value) return '' // jika kosong, kembalikan teks kosong (hindari error)
  const text = String(value)
  return text.charAt(0).toUpperCase() + text.slice(1)
}

// [1.2] Filter 2: seluruh teks menjadi huruf kapital.
//       "to-do list" -> "TO-DO LIST"
export function uppercase(value) {
  if (!value) return ''
  return String(value).toUpperCase()
}

// [1.2] Filter 3: memotong teks panjang lalu menambah "...".
//       max = 30 adalah nilai bawaan jika tidak diberi angka.
export function truncate(value, max = 30) {
  if (!value) return ''
  const text = String(value)
  return text.length > max ? text.slice(0, max) + '...' : text
}

// [1.2] Filter 4: mengubah tanggal ISO menjadi format Indonesia.
//       "2026-11-07T01:30:00.000Z" -> "7 November 2026"
export function formatDate(value) {
  if (!value) return ''
  return new Date(value).toLocaleDateString('id-ID', {
    day: 'numeric',
    month: 'long',
    year: 'numeric'
  })
}

// Semua filter dikumpulkan dalam satu objek agar mudah didaftarkan di main.js
export default { capitalize, uppercase, truncate, formatDate }
```

| Bagian | Penjelasan |
|---|---|
| Setiap filter | Fungsi biasa: menerima nilai, mengembalikan teks yang sudah rapi |
| `if (!value) return ''` | Mencegah error saat nilainya kosong |
| `max = 30` | **Nilai bawaan** parameter |
| `'id-ID'` | Nama bulan tampil dalam bahasa Indonesia |

### 3.2 Isi `src/main.js`

Hapus seluruh isi bawaan, ganti dengan:

```js
// [1.1] Titik masuk aplikasi: file pertama yang dijalankan Vue.
import { createApp } from 'vue'
import App from './App.vue'              // [1.1] komponen induk
import filters from './utils/filters'    // [1.2] fungsi pemformat teks
import './assets/main.css'               // [1.1] gaya global

// Buat aplikasi dari komponen App (disimpan dulu di variabel "app")
const app = createApp(App)

// [1.2] Daftarkan filter sebagai properti global bernama $filters.
//       Efeknya: di template komponen MANA PUN boleh menulis
//       {{ $filters.capitalize(teks) }} tanpa mengimpor ulang.
app.config.globalProperties.$filters = filters

// Pasang aplikasi ke elemen <div id="app"> di index.html
app.mount('#app')
```

> 💡 Bedanya dengan `createApp(App).mount('#app')`: kita pecah menjadi dua langkah, karena `$filters` harus didaftarkan **sebelum** aplikasi dipasang.

### ✅ Hasil yang diharapkan
Halaman masih kosong atau menampilkan error, karena `App.vue` bawaan belum diganti. Lanjut ke Langkah 4.

---

## Langkah 4: Kerangka App.vue

**Tujuan:** membuat wadah aplikasi dan data utama.
**Kriteria:** `[1.1]` komponen induk, `[1.2]` mustache dan filter, array data.

Hapus seluruh isi `src/App.vue`, ganti dengan:

```vue
<template>
  <!-- [1.1] Template: bagian tampilan (HTML) dari komponen ini -->
  <main class="container">
    <!-- [1.2] Mustache + filter: judul diubah menjadi KAPITAL oleh $filters.uppercase -->
    <h1>{{ $filters.uppercase(appTitle) }}</h1>
  </main>
</template>

<script>
export default {
  name: 'App', // [1.1] nama komponen

  // [1.2] data() HARUS berupa fungsi di Vue 3, lalu me-return sebuah objek
  data() {
    const now = new Date().toISOString() // waktu sekarang dalam format standar (ISO)
    return {
      appTitle: 'To-Do List Saya',

      // [1.2] Array berisi objek. Setiap tugas punya: id (unik), text, completed, createdAt.
      //       Teks sengaja huruf kecil agar efek filter capitalize terlihat.
      tasks: [
        { id: 1, text: 'belajar vue.js', completed: false, createdAt: now },
        { id: 2, text: 'membuat to-do list', completed: false, createdAt: now },
        { id: 3, text: 'mengerjakan tugas', completed: false, createdAt: now }
      ],

      // Filter yang sedang dipilih: 'all' | 'active' | 'completed'
      filter: 'all'
    }
  }
}
</script>
```

> 💡 `tasks` dan `filter` disimpan di `App.vue` (induk) karena banyak komponen membutuhkannya. Prinsipnya **single source of truth**: satu sumber data.

### ✅ Hasil yang diharapkan
Kartu putih di tengah, latar gradien, dan judul **TO-DO LIST SAYA** (kapital). Console bersih dari error.

---

## Langkah 5: Komponen TaskStats

**Tujuan:** komponen paling sederhana: hanya menerima data dan menampilkannya.
**Kriteria:** `[1.1]` komponen dan props, `[1.2]` `v-text`, `[1.4]` computed.

### 5.1 Buat `src/components/TaskStats.vue`

```vue
<template>
  <div class="stats">
    <strong>Total:</strong>
    <!-- [1.2] v-text: mengisi isi <span> dengan nilai props "total".
               Hasilnya sama dengan {{ total }}, tetapi mengganti seluruh isi elemen. -->
    <span v-text="total"></span> |
    <strong>Aktif:</strong> <span v-text="active"></span> |
    <strong>Selesai:</strong> <span v-text="completed"></span>
  </div>
</template>

<script>
export default {
  name: 'TaskStats', // [1.1] nama file = nama komponen, PascalCase dan dua kata

  // [1.1] props: "pintu masuk" data dari induk (App.vue). Hanya dibaca, tidak diubah.
  props: {
    total: { type: Number, required: true },
    active: { type: Number, required: true },
    completed: { type: Number, required: true }
  }
}
</script>
```

### 5.2 Hubungkan di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>{{ $filters.uppercase(appTitle) }}</h1>

    <!-- [1.1] Memakai komponen anak. Tanda ":" (v-bind) mengirim NILAI computed,
               bukan teks biasa. Ini contoh props turun. -->
    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script (tambahkan tiga bagian bertanda `← BARU`):**

```js
import TaskStats from './components/TaskStats.vue'   // ← BARU [1.1] langkah 1: impor

export default {
  name: 'App',
  components: { TaskStats },                         // ← BARU [1.1] langkah 2: daftarkan

  data() { /* ...tetap seperti sebelumnya... */ },

  // ← BARU [1.4] computed: nilai turunan dari data "tasks".
  //   Dihitung otomatis dan disimpan (cache); hanya dihitung ulang jika tasks berubah.
  computed: {
    totalTasks() {
      return this.tasks.length // jumlah seluruh tugas
    },
    activeTasks() {
      return this.tasks.filter(t => !t.completed).length // yang belum selesai
    },
    completedTasks() {
      return this.tasks.filter(t => t.completed).length // yang sudah selesai
    }
  }
}
```

**Tiga langkah memakai komponen** (hafalkan, akan dipakai terus):

1. **Impor:** `import TaskStats from './components/TaskStats.vue'`
2. **Daftarkan:** `components: { TaskStats }`
3. **Pasang di template:** `<TaskStats ... />`

> ⚠️ **Kenapa `:total="totalTasks"` pakai titik dua?** Tanpa `:`, Vue menganggapnya teks `"totalTasks"`. Dengan `:`, Vue mengambil **nilai** computed `totalTasks`.

### ✅ Hasil yang diharapkan

```
TO-DO LIST SAYA
Total: 3 | Aktif: 3 | Selesai: 0
```

### 🧪 Percobaan
Ubah `completed: false` pada satu tugas di `data()` menjadi `true`, simpan. Angkanya langsung menjadi `Aktif: 2 | Selesai: 1`. Itulah **reaktivitas**. Kembalikan ke `false` setelahnya.

---

## Langkah 6: Komponen TaskItem dan TaskList

**Tujuan:** menampilkan daftar tugas yang sudah diformat.
**Kriteria:** `[1.2]` mustache, filter, `v-bind`, `v-for`; `[1.3]` `v-show` dan `v-if`.

Kita buat `TaskItem` (satu baris) dalam **versi dasar** dulu. Interaksi mouse dan keyboard menyusul di Langkah 12.

### 6.1 Buat `src/components/TaskItem.vue` (versi dasar)

```vue
<template>
  <!-- [1.2] v-bind pada class: class "completed" dipasang HANYA jika tugas selesai
             (di CSS, class ini memberi coretan pada teks). -->
  <li class="task-item" :class="{ completed: task.completed }">
    <!-- [1.2] v-bind: id unik, status centang diambil dari data.
         [1.6] Event "change" dipicu klik mouse atau tombol spasi; lalu melapor ke induk. -->
    <input
      :id="'task-' + task.id"
      type="checkbox"
      :checked="task.completed"
      @change="$emit('toggle', task.id)"
    />

    <div class="task-body">
      <!-- [1.2] Mustache + FILTER BERANTAI: huruf pertama dikapitalkan,
                 lalu hasilnya dipotong di 35 karakter.
                 :title (v-bind) menampilkan teks lengkap saat kursor diam di atasnya.
                 :for menghubungkan label dengan checkbox (klik teks = mencentang). -->
      <label :for="'task-' + task.id" class="task-text" :title="task.text">
        {{ number }}. {{ $filters.truncate($filters.capitalize(task.text), 35) }}
      </label>

      <!-- [1.3] v-if: tanggal hanya tampil jika data createdAt ada.
           [1.2] Filter formatDate mengubah tanggal ISO menjadi format Indonesia. -->
      <small v-if="task.createdAt" class="task-date">
        Dibuat: {{ $filters.formatDate(task.createdAt) }}
      </small>
    </div>

    <!-- [1.3] v-show: tanda ✓ selalu ada di halaman, tetapi hanya TERLIHAT
               jika tugas selesai (disembunyikan dengan CSS display:none). -->
    <span v-show="task.completed" class="done-mark">✓ Selesai</span>

    <!-- [1.6] Event mouse: klik tombol melapor ke induk, membawa id tugas -->
    <button type="button" class="delete-btn" @click="$emit('delete', task.id)">
      Hapus
    </button>
  </li>
</template>

<script>
export default {
  name: 'TaskItem', // [1.1]

  // [1.1] Props dari induk: satu tugas (objek) dan nomor urutnya
  props: {
    task: { type: Object, required: true },
    number: { type: Number, required: true }
  },

  // [1.1] Daftar event yang boleh dikirim komponen ini ke induk (events up)
  emits: ['toggle', 'delete']
}
</script>
```

| Kode | Fungsi |
|---|---|
| `$filters.truncate($filters.capitalize(...), 35)` | Dua filter berantai (seperti `teks \| capitalize \| truncate` di Vue 2) |
| `$emit('toggle', task.id)` | Komponen **melapor** ke induk sambil membawa `id`. Ia tidak mengubah data sendiri |
| `v-if` vs `v-show` | `v-if` membuat/menghapus elemen; `v-show` hanya menyembunyikan |

### 6.2 Buat `src/components/TaskList.vue`

```vue
<template>
  <!-- [1.3] v-if / v-else: jika ada tugas tampilkan daftar, jika tidak tampilkan pesan kosong -->
  <ul v-if="tasks.length > 0">
    <!-- [1.2] v-for: membuat SATU <TaskItem> untuk SETIAP tugas di array "tasks".
               (task, index): "index" dimulai dari 0, sehingga nomor tampil = index + 1.
               :key unik (id) membantu Vue melacak item mana yang berubah atau terhapus. -->
    <TaskItem
      v-for="(task, index) in tasks"
      :key="task.id"
      :task="task"
      :number="index + 1"
      @toggle="$emit('toggle', $event)"
      @delete="$emit('delete', $event)"
      @edit="$emit('edit', $event)"
    />
  </ul>

  <div v-else class="empty-state">
    <!-- [1.3] Rantai v-if / v-else-if / v-else: pesan kosong menyesuaikan filter aktif -->
    <p v-if="filter === 'all'">Belum ada tugas. Tambahkan tugas pertama Anda!</p>
    <p v-else-if="filter === 'active'">Tidak ada tugas aktif.</p>
    <p v-else>Tidak ada tugas yang selesai.</p>
  </div>
</template>

<script>
import TaskItem from './TaskItem.vue' // [1.1] impor komponen anak

export default {
  name: 'TaskList',
  components: { TaskItem }, // [1.1] daftarkan komponen anak

  props: {
    tasks: { type: Array, required: true },  // [1.2] array yang akan di-loop
    filter: { type: String, required: true } // dipakai untuk memilih pesan kosong
  },

  // [1.6] TaskList hanya PERANTARA: event dari TaskItem diteruskan ke App.
  //       ($event = data yang dibawa event, misalnya id tugas.)
  //       Event "edit" dipakai nanti di Langkah 12.
  emits: ['toggle', 'delete', 'edit']
}
</script>
```

> 💡 `TaskList` tidak tahu cara menghapus atau mencentang. Ia hanya menyampaikan pesan ke atas. Tiap komponen dibuat sekecil mungkin tanggung jawabnya.

### 6.3 Hubungkan di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>{{ $filters.uppercase(appTitle) }}</h1>

    <!-- [1.1] filter="all" sementara berupa teks biasa; diganti data asli di Langkah 8 -->
    <TaskList :tasks="tasks" filter="all" />

    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script:**

```js
import TaskList from './components/TaskList.vue'     // ← BARU
import TaskStats from './components/TaskStats.vue'

// ...
components: { TaskList, TaskStats },                 // ← BARU (tambah TaskList)
```

### ✅ Hasil yang diharapkan

Tiga tugas tampil bernomor, huruf pertama kapital (`1. Belajar vue.js`), dengan tanggal berformat Indonesia di bawahnya, plus checkbox dan tombol **Hapus**. **Tombol dan checkbox belum berfungsi**, karena `App` belum mendengarkan event-nya (Langkah 7).

### 🧪 Percobaan
- Arahkan kursor ke teks tugas: tooltip menampilkan teks asli (huruf kecil).
- Ubah satu `text` di `data()` menjadi kalimat 50 huruf: tampil terpotong dengan `...`.

---

## Langkah 7: Centang dan Hapus

**Tujuan:** membuat checkbox dan tombol Hapus bekerja.
**Kriteria:** `[1.4]` methods, `[1.6]` event.

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>{{ $filters.uppercase(appTitle) }}</h1>

    <!-- [1.6] "@" adalah singkatan v-on: MENDENGARKAN event dari komponen anak.
               "Kalau ada event toggle, jalankan method toggleTask." -->
    <TaskList
      :tasks="tasks"
      filter="all"
      @toggle="toggleTask"
      @delete="deleteTask"
    />

    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script (tambahkan `methods` setelah `computed`):**

```js
  // ← BARU [1.4] methods: aksi yang dijalankan saat dipanggil (event).
  //   Method BOLEH mengubah data; computed tidak.
  methods: {
    // Membalik status selesai/belum pada tugas yang id-nya cocok
    toggleTask(id) {
      const task = this.tasks.find(t => t.id === id) // [1.2] find: cari satu elemen array
      if (task) task.completed = !task.completed
    },
    // Membuat array baru TANPA tugas yang dihapus
    deleteTask(id) {
      this.tasks = this.tasks.filter(t => t.id !== id) // [1.2] filter: saring array
    }
  }
```

> 💡 Di dalam `<script>`, data diakses lewat **`this`** (`this.tasks`). Di `<template>`, cukup `tasks` tanpa `this`.

### ✅ Hasil yang diharapkan

- Centang tugas: teks **tercoret**, muncul **✓ Selesai**, angka statistik berubah.
- Klik **Hapus**: tugas hilang, **Total** berkurang.
- Hapus semua tugas: muncul *"Belum ada tugas. Tambahkan tugas pertama Anda!"*

### 🧪 Percobaan
Perhatikan bahwa kita **hanya mengubah data**. Kita tidak pernah menyentuh tampilan, tetapi tampilan, statistik, dan coretan berubah sendiri. Refresh halaman mengembalikan 3 tugas awal (penyimpanan permanen dibuat di Langkah 13).

---

## Langkah 8: Komponen TaskFilter

**Tujuan:** menyaring tugas berdasarkan status.
**Kriteria:** `[1.2]` `v-for` dari array dan `v-model` pada komponen, `[1.4]` computed `filteredTasks`.

### 8.1 Buat `src/components/TaskFilter.vue`

```vue
<template>
  <div class="filter-buttons">
    <!-- [1.2] v-for dari array "options": tombol dibuat otomatis, TIDAK ditulis satu per satu.
               Menambah filter baru cukup menambah satu baris di array "options".
               [1.2] :class (v-bind): tombol yang sedang dipilih diberi class "active".
               [1.6] @click (event mouse): kirim nilai filter baru ke induk. -->
    <button
      v-for="option in options"
      :key="option.value"
      type="button"
      :class="{ active: modelValue === option.value }"
      @click="$emit('update:modelValue', option.value)"
    >
      {{ option.label }}
    </button>
  </div>
</template>

<script>
export default {
  name: 'TaskFilter',

  // [1.2] Pasangan WAJIB agar komponen ini bisa dipakai dengan v-model:
  //       props bernama "modelValue" + event bernama "update:modelValue".
  props: {
    modelValue: { type: String, required: true }
  },
  emits: ['update:modelValue'],

  data() {
    return {
      // [1.2] Array pilihan filter yang di-loop dengan v-for
      options: [
        { value: 'all', label: 'Semua' },
        { value: 'active', label: 'Aktif' },
        { value: 'completed', label: 'Selesai' }
      ]
    }
  }
}
</script>
```

> 💡 **Rahasia `v-model` pada komponen.** `<TaskFilter v-model="filter" />` adalah singkatan dari
> `<TaskFilter :modelValue="filter" @update:modelValue="filter = $event" />`.

### 8.2 Hubungkan di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>{{ $filters.uppercase(appTitle) }}</h1>

    <!-- [1.2] v-model pada komponen: "filter" di App ikut berubah saat tombol diklik -->
    <TaskFilter v-model="filter" />

    <!-- Perhatikan dua perubahan: sekarang memakai filteredTasks dan :filter="filter" -->
    <TaskList
      :tasks="filteredTasks"
      :filter="filter"
      @toggle="toggleTask"
      @delete="deleteTask"
    />

    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script:**

```js
import TaskFilter from './components/TaskFilter.vue'   // ← BARU

// ...
components: { TaskFilter, TaskList, TaskStats },       // ← BARU (tambah TaskFilter)

// di dalam computed, tambahkan:
    // ← BARU [1.4] computed: daftar tugas yang sudah disaring sesuai pilihan filter.
    //   Dihitung ulang otomatis bila "tasks" ATAU "filter" berubah.
    filteredTasks() {
      if (this.filter === 'active') return this.tasks.filter(t => !t.completed)
      if (this.filter === 'completed') return this.tasks.filter(t => t.completed)
      return this.tasks
    }
```

| `filter` | `filteredTasks` berisi |
|---|---|
| `'all'` | Semua tugas |
| `'active'` | Tugas yang belum selesai |
| `'completed'` | Tugas yang sudah selesai |

> ⚠️ Menyaring dengan `v-if` di dalam `v-for` pada elemen yang sama **tidak dianjurkan**. Menyaring lewat computed lebih bersih dan efisien.

### ✅ Hasil yang diharapkan

- Tiga tombol **Semua / Aktif / Selesai**; **Semua** berwarna ungu.
- Centang satu tugas, klik **Selesai**: hanya tugas itu yang tampil, **bernomor ulang dari 1**.
- Pilih **Selesai** saat belum ada yang selesai: *"Tidak ada tugas yang selesai."*

---

## Langkah 9: Komponen TaskAlert

**Tujuan:** pesan otomatis sesuai jumlah tugas.
**Kriteria:** `[1.3]` rantai `v-if / v-else-if / v-else`, `[1.2]` `v-html`, `[1.4]` computed.

### 9.1 Buat `src/components/TaskAlert.vue`

```vue
<template>
  <!-- [1.3] Rantai v-if / v-else-if / v-else dibaca dari ATAS ke BAWAH.
             Hanya SATU blok yang tampil. Letakkan kondisi paling khusus di atas. -->
  <div v-if="total === 0" class="alert alert-info">
    Belum ada tugas, yuk mulai tambahkan.
  </div>
  <div v-else-if="active === 0" class="alert alert-success">
    🎉 Hebat! Semua tugas sudah selesai.
  </div>

  <!-- [1.2] v-html: isi pesan dirender sebagai HTML, sehingga <strong> benar-benar menebalkan.
             {{ }} akan menampilkan tag-nya sebagai tulisan mentah. -->
  <div v-else-if="active > 5" class="alert alert-warning" v-html="busyMessage"></div>
  <div v-else class="alert alert-info" v-html="normalMessage"></div>
</template>

<script>
export default {
  name: 'TaskAlert',
  props: {
    total: { type: Number, required: true },
    active: { type: Number, required: true }
  },

  // [1.4] computed: menyusun teks pesan dari props.
  //       AMAN memakai v-html karena isinya hanya teks dari kode kita sendiri
  //       ditambah sebuah ANGKA. JANGAN PERNAH v-html untuk teks yang diketik pengguna (risiko XSS).
  computed: {
    busyMessage() {
      return `⚠️ Wah, ada <strong>${this.active}</strong> tugas aktif. Jangan lupa diselesaikan!`
    },
    normalMessage() {
      return `📌 Ada <strong>${this.active}</strong> tugas yang masih aktif.`
    }
  }
}
</script>
```

### 9.2 Pasang di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>{{ $filters.uppercase(appTitle) }}</h1>

    <TaskFilter v-model="filter" />

    <!-- [1.1] Dua props: jumlah total dan jumlah aktif -->
    <TaskAlert :total="totalTasks" :active="activeTasks" />

    <TaskList
      :tasks="filteredTasks"
      :filter="filter"
      @toggle="toggleTask"
      @delete="deleteTask"
    />

    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script:**

```js
import TaskAlert from './components/TaskAlert.vue'   // ← BARU

components: { TaskFilter, TaskAlert, TaskList, TaskStats },   // ← BARU (tambah TaskAlert)
```

### ✅ Hasil yang diharapkan

| Kondisi | Pesan |
|---|---|
| 3 tugas aktif | 📌 Ada **3** tugas yang masih aktif. |
| Semua dicentang | 🎉 Hebat! Semua tugas sudah selesai. |
| Semua dihapus | Belum ada tugas, yuk mulai tambahkan. |

> 💡 Pesan peringatan "lebih dari 5 tugas aktif" baru bisa diuji setelah Langkah 11 (ada tombol tambah tugas).

---

## Langkah 10: Komponen TaskActions

**Tujuan:** aksi massal yang mengolah seluruh array sekaligus.
**Kriteria:** `[1.2]` pengolahan array, `[1.6]` event mouse, `[1.4]` methods.

### 10.1 Buat `src/components/TaskActions.vue`

```vue
<template>
  <div class="actions">
    <!-- [1.6] Event mouse: klik melapor ke induk (App) untuk menandai semua selesai.
         [1.2] :disabled (v-bind): tombol nonaktif bila tidak ada tugas aktif. -->
    <button
      type="button"
      class="btn-secondary"
      :disabled="activeCount === 0"
      @click="$emit('mark-all')"
    >
      ✓ Tandai semua selesai
    </button>

    <!-- [1.6] Klik: hapus semua tugas yang selesai.
         [1.2] Mustache: angka di label ikut berubah mengikuti data. -->
    <button
      type="button"
      class="btn-outline-danger"
      :disabled="completedCount === 0"
      @click="$emit('clear-completed')"
    >
      🗑 Hapus yang selesai ({{ completedCount }})
    </button>
  </div>
</template>

<script>
export default {
  name: 'TaskActions',
  props: {
    activeCount: { type: Number, required: true },
    completedCount: { type: Number, required: true }
  },
  emits: ['mark-all', 'clear-completed']
}
</script>
```

### 10.2 Hubungkan di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>{{ $filters.uppercase(appTitle) }}</h1>

    <TaskFilter v-model="filter" />
    <TaskAlert :total="totalTasks" :active="activeTasks" />

    <!-- [1.1] Di template ditulis kebab-case (:active-count), di script camelCase (activeCount).
               Vue menghubungkan keduanya otomatis. -->
    <TaskActions
      :active-count="activeTasks"
      :completed-count="completedTasks"
      @mark-all="markAllDone"
      @clear-completed="clearCompleted"
    />

    <TaskList
      :tasks="filteredTasks"
      :filter="filter"
      @toggle="toggleTask"
      @delete="deleteTask"
    />

    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script:**

```js
import TaskActions from './components/TaskActions.vue'   // ← BARU

components: { TaskFilter, TaskAlert, TaskActions, TaskList, TaskStats },   // ← BARU

// di dalam methods, tambahkan:
    // ← BARU [1.4][1.2] forEach: mengubah SETIAP elemen array
    markAllDone() {
      this.tasks.forEach(t => {
        t.completed = true
      })
    },
    // ← BARU [1.4][1.2] filter: buat array baru berisi HANYA tugas yang belum selesai
    clearCompleted() {
      this.tasks = this.tasks.filter(t => !t.completed)
    }
```

### ✅ Hasil yang diharapkan

- **Tandai semua selesai**: semua tercentang dan tombol menjadi nonaktif.
- **Hapus yang selesai (N)**: tugas yang selesai hilang; tombol nonaktif jika N = 0.

---

## Langkah 11: Komponen TaskForm

**Tujuan:** menambah tugas baru dengan validasi, lewat klik **atau** keyboard.
**Kriteria:** `[1.6]` formulir, validasi, event mouse dan keyboard; `[1.5]` watcher; `[1.2]` `v-model`.

Ini komponen paling kompleks, jadi dibuat setelah semua dasar dikuasai.

### 11.1 Buat `src/components/TaskForm.vue`

```vue
<template>
  <!-- [1.6] @submit.prevent: mencegah halaman reload bila form terkirim.
             Pengiriman sebenarnya ditangani event Enter dan klik di bawah. -->
  <form class="task-form" novalidate @submit.prevent>
    <!-- [1.6] <label for> terhubung ke input ber-id sama: klik label memfokuskan input -->
    <label for="new-task">Tugas baru</label>

    <div class="input-row">
      <!-- [1.2] v-model: isi input terhubung DUA ARAH dengan data "newTask".
           [1.6] maxlength membatasi 50 karakter; placeholder memberi contoh isian.
           [1.2] :class: input bergaris merah bila ada pesan error.
           [1.6] Event KEYBOARD: Enter = tambah tugas, Esc = kosongkan isian. -->
      <input
        id="new-task"
        ref="taskInput"
        v-model="newTask"
        type="text"
        maxlength="50"
        placeholder="Contoh: Belajar Vue.js"
        :class="{ invalid: errorMessage }"
        @keyup.enter="submitTask"
        @keydown.esc="resetForm"
      />

      <!-- [1.6] Event MOUSE: klik tombol juga menambah tugas -->
      <button type="button" @click="submitTask">Tambah</button>
    </div>

    <!-- [1.2] Mustache: penghitung karakter diperbarui otomatis.
               :class: berubah warna saat mendekati batas (40 dari 50). -->
    <small class="counter" :class="{ warn: newTask.length >= 40 }">
      {{ newTask.length }}/50 karakter
    </small>
    <small class="hint">Enter = tambah · Esc = kosongkan</small>

    <!-- [1.3] v-if: pesan error HANYA tampil bila ada isinya -->
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
  </form>
</template>

<script>
export default {
  name: 'TaskForm',

  // [1.1] Props: daftar tugas yang sudah ada (untuk memeriksa duplikat).
  //       default: () => [] artinya "pakai array kosong jika induk tidak mengirim apa-apa".
  props: {
    existingTasks: { type: Array, default: () => [] }
  },
  emits: ['add-task'],

  data() {
    return {
      newTask: '',      // isi input
      errorMessage: ''  // pesan kesalahan ('' = tidak ada error)
    }
  },

  // [1.5] WATCHER 3: memantau data "newTask".
  //       Begitu pengguna mulai mengetik, pesan error otomatis dihapus.
  watch: {
    newTask() {
      if (this.errorMessage) this.errorMessage = ''
    }
  },

  methods: {
    // [1.6] Validasi sederhana. Mengembalikan pesan error, atau '' bila isian valid.
    //       Aturan diperiksa berurutan; yang pertama dilanggar langsung dikembalikan.
    validate(text) {
      if (text === '') return 'Tugas tidak boleh kosong.'
      if (text.length < 3) return 'Tugas minimal 3 karakter.'

      // [1.2] some: apakah ADA elemen array yang cocok? (duplikat tanpa peduli huruf besar/kecil)
      const duplicate = this.existingTasks.some(
        t => t.text.toLowerCase() === text.toLowerCase()
      )
      if (duplicate) return 'Tugas ini sudah ada di daftar.'

      return ''
    },

    // [1.4] Method: dipanggil oleh Enter (keyboard) DAN klik tombol (mouse)
    submitTask() {
      const text = this.newTask.trim() // buang spasi di awal dan akhir
      const error = this.validate(text)

      if (error) {
        this.errorMessage = error            // tampilkan pesan, jangan simpan
      } else {
        this.$emit('add-task', text)         // kirim teks bersih ke induk (events up)
        this.newTask = ''                    // kosongkan input
      }

      // [1.6] Kembalikan kursor ke kolom input agar pengguna bisa langsung mengetik lagi
      this.$refs.taskInput.focus()
    },

    // [1.6] Dipanggil tombol Esc: kosongkan isian dan pesan error
    resetForm() {
      this.newTask = ''
      this.errorMessage = ''
    }
  }
}
</script>
```

| Bagian | Fungsi |
|---|---|
| `trim()` | Tanpa ini, isian `"   "` (hanya spasi) lolos sebagai teks |
| `ref="taskInput"` + `this.$refs.taskInput` | Mengambil elemen input agar bisa diberi `.focus()` |
| `watch: newTask` | Pesan error hilang begitu pengguna memperbaiki isiannya |

### 11.2 Pasang di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>{{ $filters.uppercase(appTitle) }}</h1>

    <!-- [1.1] Props "existing-tasks" untuk cek duplikat; event "add-task" ditangkap addTask -->
    <TaskForm :existing-tasks="tasks" @add-task="addTask" />

    <TaskFilter v-model="filter" />
    <TaskAlert :total="totalTasks" :active="activeTasks" />
    <TaskActions
      :active-count="activeTasks"
      :completed-count="completedTasks"
      @mark-all="markAllDone"
      @clear-completed="clearCompleted"
    />
    <TaskList
      :tasks="filteredTasks"
      :filter="filter"
      @toggle="toggleTask"
      @delete="deleteTask"
    />
    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script:**

```js
import TaskForm from './components/TaskForm.vue'   // ← BARU

components: { TaskForm, TaskFilter, TaskAlert, TaskActions, TaskList, TaskStats },   // ← BARU

// di dalam methods, tambahkan:
    // ← BARU [1.4][1.2] push: menambah elemen baru di akhir array.
    //   Date.now() = angka milidetik saat ini, dipakai sebagai id unik.
    //   createdAt disimpan format ISO; ditampilkan rapi oleh filter formatDate.
    addTask(text) {
      this.tasks.push({
        id: Date.now(),
        text,
        completed: false,
        createdAt: new Date().toISOString()
      })
    }
```

### ✅ Hasil yang diharapkan
Aplikasi bisa dipakai untuk menambah, mencentang, menghapus, dan memfilter.

### 🧪 Uji validasi

| Isian | Hasil yang benar |
|---|---|
| (kosong), klik **Tambah** | Error: *tidak boleh kosong* |
| `"   "` (hanya spasi) | Error: *tidak boleh kosong* |
| `"ab"` | Error: *minimal 3 karakter* |
| `"BELAJAR VUE.JS"` (padahal sudah ada) | Error: *sudah ada di daftar* |
| `"tugas baru saya"` | Berhasil; tampil `Tugas baru saya`; kursor kembali ke input |
| Tekan **Enter** | Sama seperti klik **Tambah** |
| Tekan **Esc** | Isian dan pesan error terhapus |
| Ketik 40 huruf atau lebih | Penghitung berubah warna |
| Setelah error, mulai mengetik | Pesan error hilang |
| Tambah sampai 6 tugas aktif | Pesan `TaskAlert` berubah menjadi peringatan ⚠️ |

---

## Langkah 12: Mouse dan Keyboard di TaskItem

**Tujuan:** menambah interaksi pada baris tugas: sorot saat kursor lewat, dan mode edit.
**Kriteria:** `[1.6]` event mouse (`mouseenter`, `mouseleave`, `click`) dan keyboard (`enter`, `esc`), `[1.3]` conditional.

### 12.1 Ganti seluruh isi `src/components/TaskItem.vue`

```vue
<template>
  <!-- [1.6] Event MOUSE: kursor masuk/keluar baris mengubah data "isHovered".
       [1.2] :class: baris disorot ("hovered") saat kursor di atasnya, dan dicoret bila selesai. -->
  <li
    class="task-item"
    :class="{ completed: task.completed, hovered: isHovered }"
    @mouseenter="isHovered = true"
    @mouseleave="isHovered = false"
  >
    <input
      :id="'task-' + task.id"
      type="checkbox"
      :checked="task.completed"
      @change="$emit('toggle', task.id)"
    />

    <!-- [1.3] v-if / v-else: pindah antara MODE NORMAL dan MODE EDIT -->
    <!-- Mode normal -->
    <div v-if="!isEditing" class="task-body">
      <label :for="'task-' + task.id" class="task-text" :title="task.text">
        {{ number }}. {{ $filters.truncate($filters.capitalize(task.text), 35) }}
      </label>

      <!-- [1.3] v-if: tanggal hanya tampil bila data createdAt ada.
           [1.2] Mustache + ekspresi: teks berganti sesuai status hover. -->
      <small v-if="task.createdAt" class="task-date">
        {{ isHovered ? 'Klik teks untuk mencentang' : 'Dibuat: ' + $filters.formatDate(task.createdAt) }}
      </small>
    </div>

    <!-- Mode edit.
         [1.2] v-model.trim: isi input terhubung ke "editText", spasi pinggir dibuang otomatis.
         [1.6] Keyboard: Enter = simpan, Esc = batal.
         [1.6] @blur: klik di luar kolom juga menyimpan. -->
    <input
      v-else
      ref="editInput"
      v-model.trim="editText"
      type="text"
      maxlength="50"
      class="edit-input"
      aria-label="Ubah teks tugas"
      @keyup.enter="saveEdit"
      @keyup.esc="cancelEdit"
      @blur="saveEdit"
    />

    <!-- [1.3] v-show: tanda ✓ hanya terlihat bila tugas selesai dan tidak sedang diedit -->
    <span v-show="task.completed && !isEditing" class="done-mark">✓ Selesai</span>

    <!-- [1.6] Event MOUSE: klik "Edit" masuk mode edit (tombol ini hanya ada di mode normal) -->
    <button v-if="!isEditing" type="button" class="edit-btn" @click="startEdit">
      Edit
    </button>
    <button type="button" class="delete-btn" @click="$emit('delete', task.id)">
      Hapus
    </button>
  </li>
</template>

<script>
export default {
  name: 'TaskItem',

  props: {
    task: { type: Object, required: true },
    number: { type: Number, required: true }
  },

  // [1.1] Sekarang ada event ketiga: "edit"
  emits: ['toggle', 'delete', 'edit'],

  // [1.2] Data LOKAL komponen (hanya milik baris ini): status hover, mode edit, teks sementara
  data() {
    return {
      isHovered: false,
      isEditing: false,
      editText: ''
    }
  },

  methods: {
    // [1.6] Masuk mode edit: salin teks saat ini ke input, lalu fokuskan
    startEdit() {
      this.editText = this.task.text
      this.isEditing = true
      // Input baru muncul setelah Vue selesai memperbarui tampilan,
      // jadi fokus diberikan SESUDAHNYA lewat $nextTick.
      this.$nextTick(() => this.$refs.editInput.focus())
    },

    // [1.6] Simpan hasil edit (dipanggil oleh Enter dan blur)
    saveEdit() {
      // Penjaga: saat Enter ditekan, input hilang lalu memicu blur.
      // Tanpa baris ini, saveEdit bisa berjalan dua kali.
      if (!this.isEditing) return

      this.isEditing = false

      // [1.6] Validasi sederhana: minimal 3 karakter dan berbeda dari teks lama.
      //       Jika tidak valid, perubahan dibuang (teks kembali semula).
      if (this.editText.length >= 3 && this.editText !== this.task.text) {
        this.$emit('edit', { id: this.task.id, text: this.editText })
      }
    },

    // [1.6] Dipanggil tombol Esc: keluar dari mode edit tanpa menyimpan
    cancelEdit() {
      this.isEditing = false
    }
  }
}
</script>
```

> 💡 Edit yang tidak valid (kosong atau kurang dari 3 karakter) dikembalikan ke teks semula **tanpa pesan**. Ini sengaja dibuat sederhana. Latihan: tampilkan pesan error juga di mode edit.

### 12.2 Tambahkan `editTask` dan pasang `@edit` di `App.vue`

**Template:** pada `<TaskList>`, tambahkan satu baris:

```vue
    <TaskList
      :tasks="filteredTasks"
      :filter="filter"
      @toggle="toggleTask"
      @delete="deleteTask"
      @edit="editTask"
    />
```

**Script:** di dalam `methods`, tambahkan:

```js
    // ← BARU [1.4][1.6] Menerima OBJEK { id, text } dari TaskItem.
    //   Tanda { } pada parameter mengambil id dan text sekaligus (destructuring).
    editTask({ id, text }) {
      const task = this.tasks.find(t => t.id === id)
      if (task) task.text = text
    }
```

### ✅ Hasil yang diharapkan

| Aksi | Hasil yang benar |
|---|---|
| Arahkan kursor ke baris | Baris menyorot; info berganti menjadi "Klik teks untuk mencentang" |
| Keluarkan kursor | Info kembali menjadi tanggal |
| Klik **Edit** | Muncul kolom edit yang langsung terfokus |
| **Enter** di kolom edit | Perubahan tersimpan |
| **Esc** di kolom edit | Batal; teks kembali semula |
| Edit lalu klik di luar kolom | Tersimpan |
| Edit menjadi 2 huruf | Dikembalikan ke teks semula |

---

## Langkah 13: Watchers dan Penyimpanan Data

**Tujuan:** data tidak hilang saat refresh, dan judul tab informatif.
**Kriteria:** `[1.5]` watcher dengan `deep` dan `immediate`.

Ini langkah terakhir. Cocokkan **seluruh** isi `App.vue` Anda dengan kode final berikut.

### Kode lengkap `src/App.vue` (final)

```vue
<template>
  <!-- [1.1] Template induk: menyusun semua komponen anak -->
  <main class="container">
    <!-- [1.2] Mustache + filter uppercase -->
    <h1>{{ $filters.uppercase(appTitle) }}</h1>

    <!-- [1.1][1.6] Props turun (:existing-tasks), event naik (@add-task) -->
    <TaskForm :existing-tasks="tasks" @add-task="addTask" />

    <!-- [1.2] v-model pada komponen: dua arah dengan data "filter" -->
    <TaskFilter v-model="filter" />

    <!-- [1.3] Pesan kondisional berdasarkan jumlah tugas -->
    <TaskAlert :total="totalTasks" :active="activeTasks" />

    <!-- [1.6] Event mouse (klik) dari komponen anak diterima di sini -->
    <TaskActions
      :active-count="activeTasks"
      :completed-count="completedTasks"
      @mark-all="markAllDone"
      @clear-completed="clearCompleted"
    />

    <!-- [1.2] Daftar yang sudah disaring (computed) -->
    <TaskList
      :tasks="filteredTasks"
      :filter="filter"
      @toggle="toggleTask"
      @delete="deleteTask"
      @edit="editTask"
    />

    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>

<script>
// [1.1] Impor semua komponen anak
import TaskForm from './components/TaskForm.vue'
import TaskFilter from './components/TaskFilter.vue'
import TaskAlert from './components/TaskAlert.vue'
import TaskActions from './components/TaskActions.vue'
import TaskList from './components/TaskList.vue'
import TaskStats from './components/TaskStats.vue'

// "Nama laci" tempat data disimpan di browser (localStorage)
const STORAGE_KEY = 'todo-demo-tasks'

// Ambil data tersimpan; jika belum ada, pakai 3 tugas awal.
// try/catch mencegah aplikasi rusak bila penyimpanan bermasalah.
function loadTasks() {
  try {
    const saved = localStorage.getItem(STORAGE_KEY)
    if (saved) return JSON.parse(saved)
  } catch (e) {
    console.warn('Gagal membaca localStorage', e)
  }
  const now = new Date().toISOString()
  return [
    { id: 1, text: 'belajar vue.js', completed: false, createdAt: now },
    { id: 2, text: 'membuat to-do list', completed: false, createdAt: now },
    { id: 3, text: 'mengerjakan tugas', completed: false, createdAt: now }
  ]
}

export default {
  name: 'App', // [1.1]

  // [1.1] Daftarkan komponen anak agar bisa dipakai di template
  components: {
    TaskForm, TaskFilter, TaskAlert, TaskActions, TaskList, TaskStats
  },

  // [1.2] Data utama (single source of truth)
  data() {
    return {
      appTitle: 'To-Do List Saya',
      tasks: loadTasks(), // [1.2] array objek; dibaca dari localStorage
      filter: 'all'       // 'all' | 'active' | 'completed'
    }
  },

  // [1.4] COMPUTED: nilai turunan dari data, dihitung otomatis dan di-cache
  computed: {
    totalTasks() {
      return this.tasks.length
    },
    activeTasks() {
      return this.tasks.filter(t => !t.completed).length
    },
    completedTasks() {
      return this.tasks.filter(t => t.completed).length
    },
    filteredTasks() {
      if (this.filter === 'active') return this.tasks.filter(t => !t.completed)
      if (this.filter === 'completed') return this.tasks.filter(t => t.completed)
      return this.tasks
    }
  },

  // [1.4] METHODS: aksi yang dijalankan karena ada event; boleh mengubah data
  methods: {
    addTask(text) {
      this.tasks.push({
        id: Date.now(),
        text,
        completed: false,
        createdAt: new Date().toISOString()
      })
    },
    toggleTask(id) {
      const task = this.tasks.find(t => t.id === id)
      if (task) task.completed = !task.completed
    },
    deleteTask(id) {
      this.tasks = this.tasks.filter(t => t.id !== id)
    },
    editTask({ id, text }) {
      const task = this.tasks.find(t => t.id === id)
      if (task) task.text = text
    },
    markAllDone() {
      this.tasks.forEach(t => {
        t.completed = true
      })
    },
    clearCompleted() {
      this.tasks = this.tasks.filter(t => !t.completed)
    }
  },

  // [1.5] WATCHERS: bereaksi otomatis saat data tertentu berubah.
  //       Total watcher di proyek ini = 3 (2 di sini + 1 di TaskForm.vue).
  watch: {
    // [1.5] WATCHER 1: simpan ke localStorage setiap "tasks" berubah.
    //       deep: true WAJIB agar perubahan DI DALAM objek (misalnya completed) ikut terdeteksi.
    tasks: {
      handler(newTasks) {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(newTasks))
      },
      deep: true
    },

    // [1.5] WATCHER 2: judul tab browser mengikuti jumlah tugas aktif.
    //       immediate: true membuat watcher langsung jalan SEKALI saat aplikasi dimuat.
    activeTasks: {
      handler(count) {
        document.title = count > 0 ? `(${count}) To-Do List` : 'To-Do List'
      },
      immediate: true
    }
  }
}
</script>
```

### ✅ Hasil yang diharapkan

- Tambah atau centang tugas, lalu **refresh**. Data tetap ada.
- Judul tab browser menampilkan jumlah tugas aktif, misalnya `(3) To-Do List`.

### 🧪 Percobaan

1. Buka **F12 → Application → Local Storage → localhost**. Ada key `todo-demo-tasks` berisi data JSON.
2. Centang tugas: isi Local Storage ikut berubah.
3. **Eksperimen `deep`:** hapus sementara `deep: true`, centang tugas, lalu refresh. Status centang **tidak tersimpan**. Kembalikan setelah mencoba.
4. **Reset data:** di tab Console ketik `localStorage.clear()` lalu refresh.

> ⚠️ Kalau Anda pernah menjalankan versi aplikasi sebelumnya, data lama di browser **belum punya `createdAt`**. Jalankan `localStorage.clear()` agar tanggal tampil.

---

## Langkah 14: Pengujian Akhir

Jalankan daftar periksa ini sebelum mengumpulkan atau mendemokan.

### Fungsi aplikasi

- [ ] Tambah tugas dengan klik **Tambah** dan dengan tombol **Enter**
- [ ] Validasi: kosong, spasi saja, kurang dari 3 huruf, duplikat
- [ ] **Esc** mengosongkan input; pesan error hilang saat mulai mengetik
- [ ] Centang tugas: teks tercoret, muncul ✓ Selesai
- [ ] Hapus tugas dan edit tugas (Enter simpan, Esc batal)
- [ ] **Tandai semua selesai** dan **Hapus yang selesai** bekerja; tombol nonaktif saat tidak relevan
- [ ] Filter **Semua / Aktif / Selesai** bekerja, nomor urut dan pesan kosong menyesuaikan
- [ ] Pesan `TaskAlert` berubah di 4 kondisi (kosong, semua selesai, aktif lebih dari 5, normal)
- [ ] Statistik selalu cocok dengan daftar
- [ ] Refresh halaman: data tetap ada
- [ ] Judul tab menunjukkan jumlah tugas aktif
- [ ] Console (F12) bersih dari error dan peringatan merah

### Filter teks (kriteria 1.2)

- [ ] Judul tampil kapital
- [ ] Teks tugas selalu diawali huruf kapital
- [ ] Teks lebih dari 35 karakter terpotong `...`, tooltip menampilkan teks lengkap
- [ ] Tanggal tampil berbahasa Indonesia (misalnya "7 November 2026")

### Kualitas kode

- [ ] Nama file komponen **PascalCase** dan terdiri dari dua kata
- [ ] Setiap komponen punya **satu tanggung jawab**
- [ ] Komponen anak tidak mengubah props langsung
- [ ] Semua `v-for` memakai `:key` unik
- [ ] Semua event terdaftar di `emits`
- [ ] Indentasi rapi (VS Code: **Shift + Alt + F**)

---

## 15. Memahami Computed, Methods, dan Watch

Tiga hal ini sering membingungkan. Pakai satu analogi: **warung makan**.

| Di warung | Di `App.vue` | Perannya |
|---|---|---|
| **Stok bahan di gudang** | `data()` → `tasks`, `filter` | Barang asli yang disimpan |
| **Papan "sisa porsi hari ini"** | `computed` | Angka yang **dihitung otomatis dari stok**, hanya untuk dilihat |
| **Pelayan** | `methods` | **Bekerja** ketika dipanggil: menambah, mencoret, membuang |
| **Satpam / CCTV** | `watch` | **Mengawasi** stok; saat stok berubah, ia melakukan sesuatu di luar |

```
Papan sisa porsi  →  MELAPORKAN   (tidak mengubah stok)
Pelayan           →  MENGERJAKAN  (mengubah stok, kalau disuruh)
Satpam            →  MEREAKSI     (bergerak sendiri saat stok berubah)
```

### Computed = papan sisa porsi
Contoh: `totalTasks`, `activeTasks`, `completedTasks`, `filteredTasks`. Ciri: selalu ada **`return`**, dipanggil **tanpa kurung** (`{{ activeTasks }}`), **tidak mengubah data**, dan hanya dihitung ulang jika data yang dipakainya berubah.

### Methods = pelayan
Contoh: `addTask`, `toggleTask`, `deleteTask`, `editTask`, `markAllDone`, `clearCompleted`. Ciri: dijalankan karena ada **kejadian** (klik, Enter), **boleh mengubah data**, dan bekerja ulang setiap dipanggil.

### Watch = satpam
- Satpam 1 mengawasi `tasks`: saat berubah, **menyimpan** ke `localStorage`.
- Satpam 2 mengawasi `activeTasks`: saat berubah, **mengganti judul tab**.
- Satpam 3 (di `TaskForm`) mengawasi `newTask`: saat pengguna mengetik, **menghapus pesan error**.

| Opsi | Analogi |
|---|---|
| `deep: true` | Satpam **memeriksa isi tiap kotak**, bukan hanya jumlah kotaknya |
| `immediate: true` | Satpam **langsung memeriksa sekali saat warung buka** |

### Cara memilih

```
Hasilnya hanya untuk DITAMPILKAN / dihitung dari data lain?
        │
        ├── Ya  ──►  COMPUTED
        │
        └── Tidak
              │
              Dijalankan karena PENGGUNA melakukan sesuatu (klik, Enter)?
              │
              ├── Ya  ──►  METHOD
              │
              └── Tidak, harus OTOMATIS saat data berubah
                          (menyimpan, ubah judul, panggil API)
                          ──►  WATCH
```

| | Computed | Method | Watch |
|---|---|---|---|
| Analogi | Papan sisa porsi | Pelayan | Satpam |
| Kapan jalan? | Otomatis, jika data yang dipakai berubah | Saat **dipanggil** | Otomatis, jika data yang diawasi berubah |
| Menyimpan hasil (cache)? | ✅ Ya | ❌ Tidak | — |
| Boleh mengubah data? | ❌ Jangan | ✅ Boleh | ✅ Boleh (hati-hati) |
| Perlu `return`? | ✅ Ya | Tergantung | ❌ Tidak |

### Satu kejadian, semua peran bekerja

**Pengguna mencentang "Belajar vue.js":**

```
1. Pengguna mencentang checkbox di TaskItem
        ↓
2. EMIT: TaskItem melapor "toggle" membawa id tugas
        ↓
3. METHOD toggleTask dipanggil → tasks[0].completed menjadi true   ← DATA berubah
        ↓
4. Sebagai REAKSI atas perubahan data:
   • COMPUTED menghitung ulang (activeTasks 3 → 2, completedTasks 0 → 1)
   • WATCH bereaksi (simpan ke localStorage, judul tab menjadi "(2) To-Do List")
        ↓
5. TAMPILAN diperbarui oleh Vue
```

> 💡 Method **selalu bergerak lebih dulu**, karena ia yang mengubah data. Computed dan watch bereaksi sesudahnya. Urutan **di antara** computed dan watch tidak perlu dihafalkan.

### Pertanyaan yang sering muncul

**"Kenapa `activeTasks` tidak dibuat method?"** Bisa, tetapi method menghitung ulang **setiap dipanggil**, sedangkan computed hanya jika `tasks` berubah. `activeTasks` dipakai di banyak tempat, jadi computed lebih hemat.

**"Kenapa menyimpan ke `localStorage` tidak di computed?"** Menyimpan adalah **aksi**, bukan **hitungan**. Pekerjaan seperti itu milik watch.

**"Kenapa menyimpan tidak ditulis di `addTask`, `toggleTask`, dan lainnya?"** Harus ditulis berkali-kali dan mudah terlupa saat ada method baru. Dengan watcher, **apa pun** yang mengubah `tasks` otomatis tersimpan.

### 🧪 Melihat ketiganya bekerja

Tempel baris `console.log` ini **sementara**, lalu buka Console (F12) dan centang satu tugas:

```js
computed: {
  activeTasks() {
    console.log('🧮 computed activeTasks dihitung')
    return this.tasks.filter(t => !t.completed).length
  }
},
methods: {
  toggleTask(id) {
    console.log('🧑‍🍳 method toggleTask dipanggil')
    const task = this.tasks.find(t => t.id === id)
    if (task) task.completed = !task.completed
  }
},
watch: {
  tasks: {
    handler(newTasks) {
      console.log('👮 watch tasks: menyimpan...')
      localStorage.setItem(STORAGE_KEY, JSON.stringify(newTasks))
    },
    deep: true
  }
}
```

Pesan method muncul **paling pertama**. Pesan computed dan watch muncul sesudahnya, dan urutan di antara keduanya bisa berbeda. Itu normal. **Hapus semua `console.log`** setelah percobaan.

### Kuis cepat: computed, method, atau watch?

1. Menghitung jumlah tugas yang selesai.
2. Menghapus tugas saat tombol **Hapus** diklik.
3. Menyimpan data ke `localStorage` setiap daftar tugas berubah.
4. Menyaring tugas sesuai tombol filter.
5. Mengubah judul tab saat jumlah tugas aktif berubah.
6. Menambah tugas baru setelah Enter ditekan.

<details>
<summary>Lihat jawaban</summary>

1. **Computed** (`completedTasks`)
2. **Method** (`deleteTask`)
3. **Watch** (`tasks`)
4. **Computed** (`filteredTasks`)
5. **Watch** (`activeTasks`)
6. **Method** (`submitTask` lalu `addTask`)
</details>

> **Ringkasan:** Method itu pelayan, bergerak kalau disuruh. Computed itu papan hitungan, otomatis menghitung dan hanya untuk dilihat. Watch itu satpam, otomatis bereaksi kalau ada yang berubah.

---

## 16. Memahami Tanda `:` dan `@`

Dua singkatan ini sering tertukar.

| Singkatan | Kepanjangan | Fungsi | Cara mengingat |
|---|---|---|---|
| `:` | `v-bind:` | **Mengirim data** ke atribut atau props | "ambil nilai ini" |
| `@` | `v-on:` | **Mendengarkan event** | "dengarkan kabar ini" (seperti telinga) |

```vue
<TaskStats :total="totalTasks" />          <!-- ":" kirim NILAI totalTasks -->
<TaskList @toggle="toggleTask" />          <!-- "@" dengarkan event toggle, jalankan toggleTask -->
```

`@toggle="toggleTask"` dibaca: **"Kalau ada event `toggle`, panggil method `toggleTask`."**

### Siapa yang mengirim event?

```
TaskItem.vue   →  $emit('toggle', task.id)                    (anak berteriak)
      ↓
TaskList.vue   →  @toggle="$emit('toggle', $event)"           (perantara meneruskan)
      ↓
App.vue        →  @toggle="toggleTask"                        (induk mendengar)
      ↓
methods: toggleTask(id) { ... }                               (method bekerja)
```

Nama event di pengirim (`$emit('toggle')`) dan di pendengar (`@toggle`) **harus sama persis**. Salah ketik (misalnya `@toggel`) tidak menimbulkan error, hanya tidak terjadi apa-apa.

### Dari mana `id` sampai ke method?

`TaskItem` mengirim `$emit('toggle', task.id)`. Vue **otomatis mengoper** nilai itu ke method yang terpasang di `@toggle`. Karena itu cukup ditulis tanpa kurung:

```vue
@toggle="toggleTask"        ← benar
@toggle="toggleTask()"      ← salah: id tidak ikut terkirim (menjadi undefined)
```

### Event bawaan browser juga memakai `@`

| Penulisan | Jenis | Kapan terjadi |
|---|---|---|
| `@click` | Mouse | Elemen diklik |
| `@mouseenter` / `@mouseleave` | Mouse | Kursor masuk / keluar elemen |
| `@keyup.enter` | Keyboard | Tombol Enter dilepas |
| `@keyup.esc` / `@keydown.esc` | Keyboard | Tombol Esc |
| `@blur` | Fokus | Kolom kehilangan fokus |
| `@submit.prevent` | Form | Form dikirim, tanpa reload halaman |
| `@toggle`, `@add-task`, dll. | **Buatan sendiri** | Komponen anak memanggil `$emit(...)` |

> 💡 Bagian `.enter`, `.esc`, `.prevent` disebut **modifier**: pengubah perilaku event.

---

## 17. Peta Konsep dan Kriteria Penilaian

### Peta konsep ke file

| Konsep | Ditemukan di |
|---|---|
| Struktur & komponen | Folder `components/`, `utils/`, `main.js`, `App.vue` |
| Template & SFC | Semua file `.vue` (bagian `<template>` dan `<script>`) |
| Mustache | `TaskItem.vue`, `TaskForm.vue`, `App.vue` |
| `v-text` | `TaskStats.vue` |
| `v-html` | `TaskAlert.vue` |
| **Filter (formatting teks)** | `utils/filters.js` → dipakai di `TaskItem.vue` (`capitalize`, `truncate`, `formatDate`) dan `App.vue` (`uppercase`) |
| `v-bind` (`:class`, `:for`, `:checked`, `:title`, `:disabled`) | `TaskItem.vue`, `TaskFilter.vue`, `TaskActions.vue`, `TaskForm.vue` |
| `v-model` (+ `.trim`) | `TaskForm.vue`, `TaskItem.vue` (edit), `App.vue` + `TaskFilter.vue` |
| `v-for` + `:key` (+ `index`) | `TaskList.vue` (dengan nomor urut), `TaskFilter.vue` |
| Pengolahan array | `App.vue`: `push`, `filter`, `find`, `forEach`; `TaskForm.vue`: `some` |
| `v-if / v-else-if / v-else` | `TaskAlert.vue`, `TaskList.vue`, `TaskItem.vue` (mode edit), `TaskForm.vue` (pesan error) |
| `v-show` | `TaskItem.vue` |
| Computed | `App.vue`, `TaskAlert.vue` |
| Methods | `App.vue`, `TaskForm.vue`, `TaskItem.vue` |
| Watchers | `App.vue` (2), `TaskForm.vue` (1) |
| Props & `$emit` | Semua komponen anak |
| **Event mouse** | `@click` (`TaskForm`, `TaskItem`, `TaskActions`, `TaskFilter`), `@mouseenter` / `@mouseleave` (`TaskItem`) |
| **Event keyboard** | `@keyup.enter`, `@keydown.esc` (`TaskForm`); `@keyup.enter`, `@keyup.esc` (`TaskItem`) |
| Formulir & validasi | `TaskForm.vue` |

### Peta ke rubrik penilaian

| Kriteria | Bukti di proyek | Dibuat di |
|---|---|---|
| **1.1** Arsitektur dan struktur | Folder `components/`, `utils/`, `assets/`; 7 komponen PascalCase dua kata; template SFC; props turun, event naik | Langkah 2 sampai 12 |
| **1.2** Data binding, directive, array, filter | Mustache, `v-text`, `v-html`, `v-bind`, `v-model`, `v-for` dengan index, 4 filter (ada yang berantai), pengolahan array (`push`, `filter`, `find`, `forEach`, `some`) | Langkah 3, 5, 6, 8, 9, 11 |
| **1.3** Conditional | `v-if / v-else-if / v-else` (`TaskAlert`, `TaskList`, `TaskItem`, pesan error); `v-show` (`TaskItem`) | Langkah 6, 9, 11, 12 |
| **1.4** Computed dan methods | 4 computed di `App.vue`, 2 di `TaskAlert`; method di `App`, `TaskForm`, `TaskItem` | Langkah 5, 7, 8, 10, 11 |
| **1.5** Watchers (minimal 2) | 3 watcher: simpan `localStorage`, judul tab, hapus error saat mengetik | Langkah 11, 13 |
| **1.6** Formulir, validasi, event mouse dan keyboard | `<label>`, `maxlength`, penghitung karakter, validasi kosong / minimal 3 / duplikat, pesan error, `trim()`; mouse (`click`, `mouseenter`, `mouseleave`); keyboard (`enter`, `esc`) | Langkah 11, 12 |

---

## 18. Kesalahan yang Sering Terjadi

| Gejala | Penyebab umum | Solusi |
|---|---|---|
| Halaman putih, error `Failed to resolve import './components/HelloWorld.vue'` | `App.vue` bawaan masih memanggil file yang sudah dihapus | Ganti seluruh isi `App.vue` (Langkah 4) |
| Error `Failed to resolve import './utils/filters'` | Folder `utils` atau nama file salah ketik | Pastikan `src/utils/filters.js` ada, huruf kecil semua |
| `Cannot read properties of undefined (reading 'capitalize')` | `$filters` belum didaftarkan di `main.js` | Periksa baris `app.config.globalProperties.$filters = filters` |
| Teks `{{ $filters... }}` tampil mentah | Aplikasi gagal dimuat, atau salah tempat penulisan | Lihat error di Console |
| `Failed to resolve component: TaskItem` | Lupa impor atau daftar di `components` | Lakukan 3 langkah memakai komponen |
| Peringatan *"Component name should always be multi-word"* | Nama komponen satu kata | Pakai nama dua kata, misalnya `TaskItem` |
| Peringatan *"Extraneous non-emits event listeners"* | Event belum didaftarkan di `emits` | Tambahkan ke `emits: [...]` |
| Angka statistik `NaN`, atau props error | Lupa titik dua: `total="totalTasks"` | Tulis `:total="totalTasks"` |
| Tombol **Hapus** atau checkbox tidak bekerja | `@toggle` / `@delete` belum dipasang, atau `TaskList` tidak meneruskan event | Periksa pasangan `@toggle` di `App.vue` dan `TaskList.vue` |
| Tombol **Edit** tidak menyimpan perubahan | `@edit="editTask"` belum dipasang di `App.vue`, atau method `editTask` belum ada | Lengkapi Langkah 12.2 |
| Mode edit langsung menyimpan dua kali atau tidak berfokus | Penjaga `if (!this.isEditing) return` atau `$nextTick` terhapus | Samakan dengan kode Langkah 12 |
| Filter tidak mengubah daftar | `TaskList` masih menerima `:tasks="tasks"` | Ganti ke `:tasks="filteredTasks"` |
| Tombol filter tidak berubah warna | Props bukan `modelValue`, atau event bukan `update:modelValue` | Samakan ejaan persis |
| Data tidak tersimpan setelah refresh | `deep: true` hilang, atau `STORAGE_KEY` berbeda | Periksa watcher `tasks` |
| Tanggal tidak tampil pada tugas lama | Data lama di Local Storage belum punya `createdAt` | `localStorage.clear()` lalu refresh |
| Error `Unexpected token` saat membaca data | Isi Local Storage rusak | `localStorage.clear()` lalu refresh |
| Tampilan polos tanpa warna | `import './assets/main.css'` hilang di `main.js` | Periksa Langkah 3.2 |
| Tombol nonaktif tidak bisa diklik padahal seharusnya bisa | Nilai `activeCount` / `completedCount` salah | Periksa props yang dikirim di `App.vue` |
| Error tanda kurung atau tag saat menyalin | Komentar penanda `← BARU` ikut tersalin | Hapus penanda `← BARU`; komentar `[1.x]` boleh dibiarkan |

> 💡 **Kebiasaan baik:** selalu buka **Console (F12)** saat membangun. Pesan error Vue biasanya menyebut nama komponen dan penyebabnya.

---

## 19. Latihan Pengembangan

Kerjakan setelah aplikasi utama selesai. Urut dari termudah.

| Level | Tugas | Konsep yang dilatih |
|---|---|---|
| ⭐ | Tampilkan pesan error juga di **mode edit** (kurang dari 3 huruf) | `v-if`, data baru, validasi |
| ⭐ | Ganti klik tombol **Edit** dengan **klik dua kali** pada teks (`@dblclick`) | Event mouse baru |
| ⭐⭐ | Buat filter baru `waktuRelatif` ("2 hari lalu") dan pakai di `TaskItem` | Filter, `Date` |
| ⭐⭐ | Tambahkan **prioritas** (Rendah/Sedang/Tinggi) lewat `<select>` | `v-model` pada `select`, `:class` |
| ⭐⭐ | Pintasan keyboard: `Ctrl + Enter` untuk menambah tugas (`@keyup.ctrl.enter`) | Modifier keyboard |
| ⭐⭐⭐ | **Urutkan** tugas berdasarkan prioritas atau tanggal | Computed bertingkat, `sort` |
| ⭐⭐⭐ | Tambahkan **kolom pencarian** tugas | `v-model`, computed, watcher |
| 🏆 | Ubah satu komponen ke **Composition API** (`<script setup>`) | Perbandingan gaya penulisan |

> ✍️ **Tugas refleksi:** tuliskan dengan kalimat sendiri, *kenapa `activeTasks` dibuat computed dan bukan method?* dan *kenapa filter di Vue 3 ditulis sebagai fungsi `$filters.xxx()`?*

---

**Diperbarui:** November 2026 · **Versi:** 2.0 (Vue 3, dengan filter, event mouse dan keyboard) · **Lisensi:** untuk keperluan pembelajaran
