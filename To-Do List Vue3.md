# 🛠️ Membangun Aplikasi To-Do List dengan Vue.js 3

> Panduan **langkah demi langkah** membuat aplikasi To-Do List dari nol.
> Setiap langkah menghasilkan sesuatu yang bisa **dijalankan dan diuji** sebelum lanjut ke langkah berikutnya.

**Versi:** Vue 3 (Options API) · **Diperbarui:** Oktober 2026 · **Penyusun:** Aldian Umbu

> 📚 Panduan ini adalah pasangan dari [penjelasanVue3.md](penjelasanVue3.md). Baca materi konsepnya dulu, lalu praktikkan di sini. Kalau ada istilah yang belum dikenal, lihat Glosarium di materi tersebut.

---

## 📋 Daftar Isi

0. [Gambaran Proyek](#0-gambaran-proyek)
1. [Langkah 1: Persiapan dan Instalasi](#langkah-1-persiapan-dan-instalasi)
2. [Langkah 2: Kerangka Proyek](#langkah-2-kerangka-proyek)
3. [Langkah 3: Komponen TaskStats](#langkah-3-komponen-taskstats)
4. [Langkah 4: Komponen TaskItem dan TaskList](#langkah-4-komponen-taskitem-dan-tasklist)
5. [Langkah 5: Menghidupkan Centang dan Hapus](#langkah-5-menghidupkan-centang-dan-hapus)
6. [Langkah 6: Komponen TaskFilter](#langkah-6-komponen-taskfilter)
7. [Langkah 7: Komponen TaskAlert](#langkah-7-komponen-taskalert)
8. [Langkah 8: Komponen TaskForm](#langkah-8-komponen-taskform)
9. [Langkah 9: Watchers dan Penyimpanan Data](#langkah-9-watchers-dan-penyimpanan-data)
10. [Memahami Computed, Methods, dan Watch](#memahami-computed-methods-dan-watch-di-appvue)
11. [Langkah 10: Pengujian Akhir](#langkah-10-pengujian-akhir)
12. [Peta Konsep dan Kriteria Penilaian](#11-peta-konsep-dan-kriteria-penilaian)
13. [Kesalahan yang Sering Terjadi](#12-kesalahan-yang-sering-terjadi)
14. [Latihan Pengembangan](#13-latihan-pengembangan)

---

## 0. Gambaran Proyek

### Fitur aplikasi

- Menambah tugas baru (dengan validasi)
- Mencentang tugas yang selesai dan menghapus tugas
- Memfilter: **Semua / Aktif / Selesai**
- Pesan otomatis sesuai jumlah tugas
- Statistik jumlah tugas
- Data **tersimpan** walau halaman di-refresh
- Judul tab browser menunjukkan jumlah tugas aktif

### Struktur folder akhir

```
todo-vue-demo/
├─ index.html
├─ package.json
└─ src/
   ├─ main.js
   ├─ App.vue
   ├─ assets/
   │  └─ main.css
   └─ components/
      ├─ TaskForm.vue
      ├─ TaskFilter.vue
      ├─ TaskAlert.vue
      ├─ TaskList.vue
      ├─ TaskItem.vue
      └─ TaskStats.vue
```

### Alur data (props turun, event naik)

```
                    App.vue
      (data: tasks, filter | computed | methods | watch)
         │ props ↓                           ↑ event
 ┌───────┼────────────┬────────────┬─────────┴───────┐
 TaskForm  TaskFilter   TaskAlert    TaskList         TaskStats
                                        │
                                     TaskItem
```

`App.vue` adalah **satu-satunya** pemegang data (`tasks` dan `filter`). Komponen anak hanya menampilkan data dan melapor lewat event.

### Kenapa urutannya begini?

Kita membangun dari komponen yang **paling sedikit bergantung** pada yang lain.

| Langkah | Yang dibuat | Alasan urutan |
|---|---|---|
| 1 | Instalasi | Pondasi |
| 2 | Kerangka (`App.vue`, CSS, `main.js`) | Data dan wadah harus ada dulu |
| 3 | `TaskStats` | Paling sederhana: hanya menerima props |
| 4 | `TaskItem` + `TaskList` | Menampilkan data; `TaskList` butuh `TaskItem` |
| 5 | Method centang dan hapus | Menghidupkan event dari `TaskItem` |
| 6 | `TaskFilter` + `filteredTasks` | Butuh daftar yang sudah tampil |
| 7 | `TaskAlert` | Butuh computed jumlah tugas aktif (dari Langkah 3) |
| 8 | `TaskForm` | Paling kompleks (validasi, watcher, emit) |
| 9 | Watchers + localStorage | "Penyempurna" setelah semua fitur jalan |
| 10 | Pengujian | Memastikan semuanya benar |

### Cara membaca panduan ini

- **Template `App.vue`** ditulis **lengkap** di setiap langkah (isinya pendek).
- **Script `App.vue`** hanya ditulis bagian yang **ditambahkan**. Kode lengkap `App.vue` ada di [Langkah 9](#langkah-9-watchers-dan-penyimpanan-data) untuk dicocokkan.
- Tanda `← BARU` menunjukkan baris yang baru ditambahkan.
- Setelah setiap langkah, **jalankan dan uji** dulu. Jangan lanjut kalau masih ada error.

> 💡 Biarkan `npm run dev` berjalan terus selama Anda mengetik. Browser akan otomatis memperbarui tampilan setiap kali file disimpan (Ctrl + S).

---

## Langkah 1: Persiapan dan Instalasi

**Tujuan:** punya proyek Vue yang bisa dijalankan.

### 1.1 Pasang peralatan

1. **Node.js (LTS)** dari [nodejs.org](https://nodejs.org)
2. **VS Code** dengan ekstensi **Vue - Official**

Cek di terminal:

```bash
node -v
npm -v
```

### 1.2 Buat proyek

```bash
npm create vite@latest todo-vue-demo -- --template vue
cd todo-vue-demo
npm install
npm run dev
```

Buka alamat yang tampil di terminal (biasanya `http://localhost:5173`). Anda akan melihat halaman bawaan Vite.

> ⚠️ Kalau muncul pertanyaan tambahan, pilih opsi standar atau `No`.
> ⚠️ Pastikan sudah `cd todo-vue-demo` sebelum `npm install`.

### 1.3 Buka di VS Code

Di terminal **baru** (biarkan terminal `npm run dev` tetap jalan):

```bash
code todo-vue-demo
```

Atau lewat menu **File → Open Folder**.

### ✅ Hasil yang diharapkan
Halaman bawaan Vite (logo Vite dan Vue, tombol counter) tampil di browser.

---

## Langkah 2: Kerangka Proyek

**Tujuan:** membersihkan file bawaan dan menyiapkan wadah aplikasi.
**Konsep:** struktur folder, `main.js`, anatomi file `.vue`, `data()`.

### 2.1 Hapus file bawaan

Hapus tiga file ini:
- `src/components/HelloWorld.vue`
- `src/style.css`
- `src/assets/vue.svg`

### 2.2 Buat file CSS global

Buat file `src/assets/main.css`. Isinya gaya tampilan untuk **seluruh** aplikasi (nama class di sini akan dipakai di langkah-langkah berikutnya).

```css
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

/* Form */
.input-row { display: flex; gap: 8px; }
input[type="text"] {
  flex: 1; padding: 10px; font-size: 16px;
  border: 2px solid #ddd; border-radius: 5px;
}
input[type="text"].invalid { border-color: var(--danger); }
button {
  padding: 10px 16px; font-size: 15px; cursor: pointer;
  background: var(--primary); color: #fff; border: 0; border-radius: 5px;
}
button:hover { background: var(--primary-dark); }
.counter { color: #777; }
.counter.warn { color: var(--warning); font-weight: bold; }
.error { color: var(--danger); margin: 6px 0 0; }

/* Filter */
.filter-buttons { display: flex; gap: 8px; margin: 16px 0; }
.filter-buttons button { background: #e2e8f0; color: #333; }
.filter-buttons button:hover { background: #cbd5e0; }
.filter-buttons button.active { background: var(--primary); color: #fff; }
.filter-buttons button.active:hover { background: var(--primary-dark); }

/* Alert */
.alert { padding: 10px 14px; border-radius: 6px; margin-bottom: 16px; }
.alert-info { background: #ebf8ff; color: #2c5282; }
.alert-success { background: #f0fff4; color: var(--success); }
.alert-warning { background: #fffaf0; color: var(--warning); }

/* List */
ul { list-style: none; padding: 0; margin: 0; }
.task-item {
  display: flex; align-items: center; gap: 10px;
  padding: 10px; border-bottom: 1px solid #eee;
}
.task-text { flex: 1; margin: 0; font-weight: normal; cursor: pointer; }
.task-item.completed .task-text { text-decoration: line-through; color: #999; }
.done-mark { color: var(--success); font-size: 13px; }
.delete-btn { background: var(--danger); padding: 6px 12px; font-size: 13px; }
.delete-btn:hover { background: #c53030; }
.empty-state { text-align: center; color: #777; padding: 20px 0; }

/* Stats */
.stats {
  margin-top: 16px; padding-top: 12px;
  border-top: 2px solid #eee; text-align: center;
}
```

> 💡 CSS bukan fokus pelajaran Vue, jadi cukup disalin. Anda bisa mengubah warna di bagian `:root` sesuai selera.

### 2.3 Ganti `src/main.js`

Hapus seluruh isinya, ganti dengan:

```js
import { createApp } from 'vue'
import App from './App.vue'
import './assets/main.css'

createApp(App).mount('#app')
```

| Baris | Artinya |
|---|---|
| `createApp(App)` | Buat aplikasi Vue dari komponen `App` |
| `.mount('#app')` | Tempelkan ke elemen `#app` di `index.html` |
| `import './assets/main.css'` | Muat CSS global (menggantikan `style.css` yang dihapus) |

### 2.4 Ganti `src/App.vue`

Hapus seluruh isinya, ganti dengan:

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>
  </main>
</template>

<script>
export default {
  name: 'App',

  data() {
    return {
      tasks: [
        { id: 1, text: 'Belajar Vue.js', completed: false },
        { id: 2, text: 'Membuat To-Do List', completed: false },
        { id: 3, text: 'Mengerjakan Tugas', completed: false }
      ],
      filter: 'all' // nilai: 'all' | 'active' | 'completed'
    }
  }
}
</script>
```

### Penjelasan

| Bagian | Fungsi |
|---|---|
| `tasks` | Array berisi semua tugas. Tiap tugas punya `id` (unik), `text`, dan `completed` (selesai atau belum) |
| `filter` | Menyimpan filter yang sedang dipilih |
| `data()` berupa **fungsi** | Aturan Vue 3: `data` harus fungsi yang me-`return` objek |

> 💡 Data `tasks` dan `filter` kita taruh di `App.vue` (induk) karena akan dibutuhkan banyak komponen. Inilah prinsip *single source of truth*: satu sumber data.

### ✅ Hasil yang diharapkan
Kartu putih di tengah dengan latar gradien dan judul **To-Do List Saya**. Tidak ada error di Console (F12).

---

## Langkah 3: Komponen TaskStats

**Tujuan:** komponen paling sederhana, hanya menerima data dan menampilkannya.
**Konsep:** membuat komponen, **props**, **`v-text`**, **computed**.

### 3.1 Buat `src/components/TaskStats.vue`

```vue
<template>
  <div class="stats">
    <strong>Total:</strong> <span v-text="total"></span> |
    <strong>Aktif:</strong> <span v-text="active"></span> |
    <strong>Selesai:</strong> <span v-text="completed"></span>
  </div>
</template>

<script>
export default {
  name: 'TaskStats',
  props: {
    total: { type: Number, required: true },
    active: { type: Number, required: true },
    completed: { type: Number, required: true }
  }
}
</script>
```

| Bagian | Fungsi |
|---|---|
| `props: { total, active, completed }` | "Pintu masuk" data dari induk. Wajib bertipe angka |
| `v-text="total"` | Mengisi isi `<span>` dengan nilai `total` (hasilnya sama dengan `{{ total }}`) |

### 3.2 Hubungkan di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>

    <TaskStats                          <!-- ← BARU -->
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

> ⚠️ Komentar HTML `<!-- -->` di dalam tag di atas hanya penanda untuk materi ini. **Jangan ketik** komentar itu di dalam tag di kode Anda. Cukup tulis tag `<TaskStats ... />`-nya saja.

Versi bersih yang boleh langsung disalin:

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>

    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script** (tambahkan `import`, `components`, dan `computed`):

```vue
<script>
import TaskStats from './components/TaskStats.vue'        // ← BARU

export default {
  name: 'App',
  components: { TaskStats },                              // ← BARU

  data() {
    return {
      tasks: [ /* ...tetap seperti sebelumnya... */ ],
      filter: 'all'
    }
  },

  computed: {                                             // ← BARU
    totalTasks() {
      return this.tasks.length
    },
    activeTasks() {
      return this.tasks.filter(t => !t.completed).length
    },
    completedTasks() {
      return this.tasks.filter(t => t.completed).length
    }
  }
}
</script>
```

### Penjelasan

**Tiga langkah memakai komponen** (hafalkan, akan dipakai terus):

1. **Impor:** `import TaskStats from './components/TaskStats.vue'`
2. **Daftarkan:** `components: { TaskStats }`
3. **Pasang di template:** `<TaskStats ... />`

**Kenapa `:total="totalTasks"` pakai titik dua?** Tanpa `:`, Vue menganggapnya teks `"totalTasks"`. Dengan `:`, Vue mengambil **nilai** dari computed `totalTasks`.

**Kenapa `totalTasks` dll. dibuat computed, bukan data?** Nilainya **turunan** dari `tasks`. Kalau `tasks` berubah, hasilnya ikut diperbarui otomatis. Kita tidak perlu menghitung manual.

| Computed | Cara kerja |
|---|---|
| `totalTasks` | Panjang array `tasks` |
| `activeTasks` | `filter` hanya yang `completed` bernilai `false`, lalu hitung jumlahnya |
| `completedTasks` | `filter` yang `completed` bernilai `true`, lalu hitung jumlahnya |

### ✅ Hasil yang diharapkan

```
To-Do List Saya
Total: 3 | Aktif: 3 | Selesai: 0
```

### 🧪 Percobaan
Ubah `completed: false` pada satu tugas di `data()` menjadi `true`, simpan. Angkanya langsung menjadi `Aktif: 2 | Selesai: 1`. Itulah reaktivitas. Kembalikan ke `false` setelah mencoba.

---

## Langkah 4: Komponen TaskItem dan TaskList

**Tujuan:** menampilkan daftar tugas.
**Konsep:** **mustache**, **`v-bind`**, **`v-show`**, **`v-for` + `:key`**, **`v-if / v-else-if / v-else`**, **`$emit`**.

Kita buat dua komponen: `TaskItem` (satu baris tugas) lalu `TaskList` (kumpulan baris).

### 4.1 Buat `src/components/TaskItem.vue`

```vue
<template>
  <li class="task-item" :class="{ completed: task.completed }">
    <input
      :id="'task-' + task.id"
      type="checkbox"
      :checked="task.completed"
      @change="$emit('toggle', task.id)"
    />
    <label :for="'task-' + task.id" class="task-text">{{ task.text }}</label>

    <span v-show="task.completed" class="done-mark">✓ Selesai</span>

    <button type="button" class="delete-btn" @click="$emit('delete', task.id)">
      Hapus
    </button>
  </li>
</template>

<script>
export default {
  name: 'TaskItem',
  props: {
    task: { type: Object, required: true }
  },
  emits: ['toggle', 'delete']
}
</script>
```

### Penjelasan baris demi baris

| Kode | Konsep | Fungsi |
|---|---|---|
| `{{ task.text }}` | Mustache | Menampilkan teks tugas |
| `:class="{ completed: task.completed }"` | `v-bind` | Class `completed` dipasang **hanya jika** tugas selesai (di CSS, class ini mencoret teks) |
| `:id="'task-' + task.id"` dan `:for="..."` | `v-bind` | ID unik per tugas; `<label for>` menghubungkan teks dengan checkbox, jadi klik teks ikut mencentang |
| `:checked="task.completed"` | `v-bind` | Status centang diambil dari data |
| `@change="$emit('toggle', task.id)"` | Event | Saat dicentang, **lapor ke induk** sambil membawa `id` tugas |
| `v-show="task.completed"` | `v-show` | Tanda ✓ hanya terlihat jika selesai (elemen tetap ada di DOM, hanya disembunyikan) |
| `@click="$emit('delete', task.id)"` | Event | Saat tombol Hapus diklik, lapor ke induk |
| `props: { task }` | Props | Satu tugas (objek) dari induk |
| `emits: ['toggle', 'delete']` | Emits | Daftar event yang boleh dikirim komponen ini |

> 💡 `TaskItem` **tidak mengubah data sendiri**. Ia hanya melapor, "Tugas nomor 2 dicentang." Yang memutuskan dan mengubah data adalah induk. Inilah **events up**.

### 4.2 Buat `src/components/TaskList.vue`

```vue
<template>
  <ul v-if="tasks.length > 0">
    <TaskItem
      v-for="task in tasks"
      :key="task.id"
      :task="task"
      @toggle="$emit('toggle', $event)"
      @delete="$emit('delete', $event)"
    />
  </ul>

  <div v-else class="empty-state">
    <p v-if="filter === 'all'">Belum ada tugas. Tambahkan tugas pertama Anda!</p>
    <p v-else-if="filter === 'active'">Tidak ada tugas aktif.</p>
    <p v-else>Tidak ada tugas yang selesai.</p>
  </div>
</template>

<script>
import TaskItem from './TaskItem.vue'

export default {
  name: 'TaskList',
  components: { TaskItem },
  props: {
    tasks: { type: Array, required: true },
    filter: { type: String, required: true }
  },
  emits: ['toggle', 'delete']
}
</script>
```

### Penjelasan

| Kode | Konsep | Fungsi |
|---|---|---|
| `v-for="task in tasks"` | List rendering | Buat satu `<TaskItem>` untuk **setiap** tugas |
| `:key="task.id"` | Key unik | Tanda pengenal tiap tugas, supaya Vue tahu mana yang berubah atau terhapus |
| `:task="task"` | Props | Kirim satu tugas ke `TaskItem` |
| `v-if="tasks.length > 0"` ... `v-else` | Conditional | Kalau ada tugas tampilkan daftar, kalau tidak tampilkan pesan kosong |
| `<p v-if> / v-else-if / v-else` | Conditional bersarang | Pesan kosong menyesuaikan filter yang sedang aktif |
| `@toggle="$emit('toggle', $event)"` | Meneruskan event | `TaskList` hanya **perantara**: event dari `TaskItem` diteruskan ke `App`. `$event` adalah data yang dibawa (id tugas) |

> 💡 `TaskList` tidak tahu cara menghapus atau mencentang. Ia hanya menyampaikan pesan ke atas. Tanggung jawab tiap komponen dibuat sekecil mungkin.

### 4.3 Hubungkan di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>

    <TaskList :tasks="tasks" filter="all" />

    <TaskStats
      :total="totalTasks"
      :active="activeTasks"
      :completed="completedTasks"
    />
  </main>
</template>
```

**Script** (tambahkan impor dan daftarkan):

```js
import TaskList from './components/TaskList.vue'          // ← BARU
import TaskStats from './components/TaskStats.vue'

// ...
components: { TaskList, TaskStats },                      // ← BARU (tambah TaskList)
```

> 💡 Sementara ini kita kirim `filter="all"` (teks biasa, tanpa titik dua). Di Langkah 6 akan kita ganti dengan data `filter` yang sesungguhnya.

### ✅ Hasil yang diharapkan

Tiga tugas tampil, masing-masing dengan checkbox dan tombol **Hapus**. Di bawahnya ada statistik. **Tombol dan checkbox belum berfungsi**, karena `App` belum mendengarkan event-nya (Langkah 5).

### 🧪 Percobaan
Centang sebuah tugas lalu buka **Console (F12)**. Tidak ada error dan tidak ada yang berubah. Itu wajar, event dikirim tetapi belum ada yang menerima.

---

## Langkah 5: Menghidupkan Centang dan Hapus

**Tujuan:** membuat tombol dan checkbox bekerja.
**Konsep:** **methods**, menerima event dari anak, reaktivitas.

### 5.1 Tambahkan methods di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>

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

**Script** (tambahkan `methods` setelah `computed`):

```js
methods: {                                               // ← BARU
  toggleTask(id) {
    const task = this.tasks.find(t => t.id === id)
    if (task) task.completed = !task.completed
  },
  deleteTask(id) {
    this.tasks = this.tasks.filter(t => t.id !== id)
  }
}
```

### Penjelasan

| Kode | Fungsi |
|---|---|
| `@toggle="toggleTask"` | "Kalau ada event `toggle` dari `TaskList`, jalankan method `toggleTask`" |
| `this.tasks.find(t => t.id === id)` | Cari tugas yang `id`-nya cocok |
| `task.completed = !task.completed` | Balik statusnya (`false` → `true`, `true` → `false`) |
| `this.tasks.filter(t => t.id !== id)` | Buat array baru **tanpa** tugas yang dihapus |

> 💡 Di dalam `<script>`, data diakses lewat **`this`** (`this.tasks`). Di dalam `<template>`, cukup ditulis `tasks` tanpa `this`.

### ✅ Hasil yang diharapkan

- Centang tugas: teks **tercoret**, muncul **✓ Selesai**, angka statistik berubah.
- Klik **Hapus**: tugas hilang, angka **Total** berkurang.
- Hapus semua tugas: muncul pesan *"Belum ada tugas. Tambahkan tugas pertama Anda!"*

### 🧪 Percobaan
Perhatikan bahwa kita **hanya mengubah data** (`completed`, `tasks`). Kita tidak menyentuh tampilan sama sekali. Tampilan, statistik, dan coretan berubah sendiri.
Refresh halaman untuk mengembalikan 3 tugas awal (penyimpanan permanen baru dibuat di Langkah 9).

---

## Langkah 6: Komponen TaskFilter

**Tujuan:** menyaring tugas berdasarkan status.
**Konsep:** **`v-model` pada komponen**, **`v-for`** dari array pilihan, **computed `filteredTasks`**.

### 6.1 Buat `src/components/TaskFilter.vue`

```vue
<template>
  <div class="filter-buttons">
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
  props: {
    modelValue: { type: String, required: true }
  },
  emits: ['update:modelValue'],
  data() {
    return {
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

### Penjelasan

| Kode | Fungsi |
|---|---|
| `options` | Array pilihan filter. Tombolnya dibuat dengan `v-for`, **tidak ditulis satu per satu**. Mau tambah filter? Cukup tambah satu baris di array |
| `:class="{ active: modelValue === option.value }"` | Tombol yang sedang dipilih diberi class `active` (warna berbeda) |
| `@click="$emit('update:modelValue', option.value)"` | Saat tombol diklik, kirim nilai filter baru ke induk |
| `props: modelValue` + `emits: update:modelValue` | **Pasangan wajib** agar komponen ini bisa dipakai dengan `v-model` |

> 💡 **Rahasia `v-model` pada komponen:** `<TaskFilter v-model="filter" />` sebenarnya singkatan dari:
> `<TaskFilter :modelValue="filter" @update:modelValue="filter = $event" />`.

### 6.2 Tambahkan `filteredTasks` di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>

    <TaskFilter v-model="filter" />

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
import TaskFilter from './components/TaskFilter.vue'      // ← BARU

// ...
components: { TaskFilter, TaskList, TaskStats },          // ← BARU (tambah TaskFilter)

// di dalam computed, tambahkan:
filteredTasks() {                                         // ← BARU
  if (this.filter === 'active') return this.tasks.filter(t => !t.completed)
  if (this.filter === 'completed') return this.tasks.filter(t => t.completed)
  return this.tasks
}
```

> 💡 Perhatikan dua perubahan di `TaskList`: `:tasks="filteredTasks"` (bukan `tasks`) dan `:filter="filter"` (dengan titik dua, bukan teks `"all"`).

### Penjelasan

`filteredTasks` adalah **computed** karena ia menghasilkan nilai turunan dari `tasks` **dan** `filter`. Setiap salah satunya berubah, hasilnya otomatis diperbarui.

| `filter` | `filteredTasks` berisi |
|---|---|
| `'all'` | Semua tugas |
| `'active'` | Tugas yang belum selesai |
| `'completed'` | Tugas yang sudah selesai |

> ⚠️ Kenapa tidak memakai `v-if` di dalam `v-for` untuk menyaring? Menggabungkan keduanya pada satu elemen **tidak dianjurkan**. Menyaring lewat computed lebih bersih dan efisien.

### ✅ Hasil yang diharapkan

- Tiga tombol **Semua / Aktif / Selesai**, dengan **Semua** berwarna ungu (aktif).
- Centang satu tugas, klik **Selesai**: hanya tugas itu yang tampil.
- Klik **Aktif**: tugas selainnya tampil.
- Pilih **Selesai** saat belum ada yang selesai: muncul *"Tidak ada tugas yang selesai."*

---

## Langkah 7: Komponen TaskAlert

**Tujuan:** pesan otomatis sesuai jumlah tugas.
**Konsep:** **`v-if / v-else-if / v-else`** berantai, **`v-html`**, computed di komponen.

### 7.1 Buat `src/components/TaskAlert.vue`

```vue
<template>
  <div v-if="total === 0" class="alert alert-info">
    Belum ada tugas, yuk mulai tambahkan.
  </div>
  <div v-else-if="active === 0" class="alert alert-success">
    🎉 Hebat! Semua tugas sudah selesai.
  </div>
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

### Penjelasan

Rantai `v-if` dibaca dari atas ke bawah. **Hanya satu** blok yang tampil:

| Urutan | Kondisi | Pesan |
|---|---|---|
| 1 | `total === 0` | Belum ada tugas |
| 2 | `active === 0` | Semua selesai 🎉 |
| 3 | `active > 5` | Peringatan: tugas aktif banyak |
| 4 | selain itu (`v-else`) | Info jumlah tugas aktif |

> 💡 **Urutan kondisi penting.** Kalau "semua selesai" diletakkan di bawah "tugas aktif banyak", pesan yang muncul bisa tidak sesuai. Letakkan kondisi **paling khusus** di atas.

**Kenapa `v-html`?** Pesan berisi tag `<strong>` untuk menebalkan angka. `{{ }}` akan menampilkan tag itu sebagai tulisan mentah, sedangkan `v-html` merendernya sebagai HTML.

> ⚠️ **Aman?** Ya, karena isi `busyMessage` dan `normalMessage` hanya berasal dari **kode kita sendiri** plus sebuah **angka**. Tidak ada teks dari pengguna. **Jangan pernah** memakai `v-html` untuk teks yang diketik pengguna (risiko XSS, lihat Bab 4 di `penjelasanVue3.md`).

### 7.2 Pasang di `App.vue`

**Template (lengkap):**

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>

    <TaskFilter v-model="filter" />

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
import TaskAlert from './components/TaskAlert.vue'        // ← BARU

// ...
components: { TaskFilter, TaskAlert, TaskList, TaskStats }, // ← BARU (tambah TaskAlert)
```

### ✅ Hasil yang diharapkan

| Kondisi | Pesan |
|---|---|
| 3 tugas aktif | 📌 Ada **3** tugas yang masih aktif. |
| Semua dicentang | 🎉 Hebat! Semua tugas sudah selesai. |
| Semua dihapus | Belum ada tugas, yuk mulai tambahkan. |

### 🧪 Percobaan
Uji pesan "tugas aktif banyak" nanti di Langkah 8, setelah ada tombol untuk menambah tugas. Tambahkan sampai 6 tugas aktif, lalu pesan berubah menjadi peringatan ⚠️.

---

## Langkah 8: Komponen TaskForm

**Tujuan:** menambah tugas baru **dengan validasi**.
**Konsep:** **`v-model`**, **formulir dan validasi**, **watcher**, **`@submit.prevent`**, `emit` dengan data.

Ini komponen paling kompleks, jadi kita buat terakhir setelah semua dasar dikuasai.

### 8.1 Buat `src/components/TaskForm.vue`

```vue
<template>
  <form class="task-form" novalidate @submit.prevent="submitTask">
    <label for="new-task">Tugas baru</label>

    <div class="input-row">
      <input
        id="new-task"
        v-model="newTask"
        type="text"
        maxlength="50"
        placeholder="Contoh: Belajar Vue.js"
        :class="{ invalid: errorMessage }"
      />
      <button type="submit">Tambah</button>
    </div>

    <small class="counter" :class="{ warn: newTask.length >= 40 }">
      {{ newTask.length }}/50 karakter
    </small>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
  </form>
</template>

<script>
export default {
  name: 'TaskForm',
  props: {
    existingTasks: { type: Array, default: () => [] }
  },
  emits: ['add-task'],

  data() {
    return {
      newTask: '',
      errorMessage: ''
    }
  },

  watch: {
    // Pesan error hilang begitu pengguna mulai mengetik lagi
    newTask() {
      if (this.errorMessage) this.errorMessage = ''
    }
  },

  methods: {
    // Mengembalikan pesan error, atau '' jika isiannya valid
    validate(text) {
      if (text === '') return 'Tugas tidak boleh kosong.'
      if (text.length < 3) return 'Tugas minimal 3 karakter.'
      const duplicate = this.existingTasks.some(
        t => t.text.toLowerCase() === text.toLowerCase()
      )
      if (duplicate) return 'Tugas ini sudah ada di daftar.'
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

### Penjelasan: bagian template

| Kode | Fungsi |
|---|---|
| `@submit.prevent="submitTask"` | Saat form dikirim (klik **Tambah** atau tekan **Enter**), jalankan `submitTask`. `.prevent` mencegah halaman reload |
| `novalidate` | Mematikan validasi bawaan browser agar kita memakai pesan error buatan sendiri |
| `<label for="new-task">` + `id="new-task"` | Label terhubung ke input; klik label akan memfokuskan input |
| `v-model="newTask"` | Isi input terhubung dua arah dengan data `newTask` |
| `maxlength="50"` | Browser membatasi maksimal 50 karakter |
| `:class="{ invalid: errorMessage }"` | Jika ada pesan error, input diberi garis tepi merah |
| `{{ newTask.length }}/50` | Penghitung karakter yang diperbarui otomatis |
| `:class="{ warn: newTask.length >= 40 }"` | Penghitung berubah warna saat mendekati batas |
| `v-if="errorMessage"` | Pesan error hanya tampil jika ada isinya |

### Penjelasan: bagian script

**`validate(text)`** memeriksa tiga aturan **berurutan**. Begitu satu aturan dilanggar, langsung mengembalikan pesannya:

| Urutan | Aturan | Pesan |
|---|---|---|
| 1 | Kosong | Tugas tidak boleh kosong. |
| 2 | Kurang dari 3 karakter | Tugas minimal 3 karakter. |
| 3 | Sudah ada (tidak peduli besar/kecil huruf) | Tugas ini sudah ada di daftar. |
| — | Lolos semua | `''` (kosong = valid) |

**`submitTask()`** alurnya:

```
Ambil isi input → trim() (buang spasi pinggir)
        ↓
   validate(text)
        ↓
 ada error? ── ya ──► tampilkan pesan, berhenti (return)
        │
       tidak
        ↓
 $emit('add-task', text)  → kirim ke induk
        ↓
 kosongkan input
```

**`trim()`** penting. Tanpa itu, pengguna yang mengetik `"   "` (hanya spasi) akan lolos sebagai teks tidak kosong.

**Watcher `newTask`:** begitu pengguna mengetik, pesan error otomatis dihapus. Ini membuat formulir terasa ramah. Kesalahan tidak "menempel" setelah diperbaiki.

**Props `existingTasks`:** dipakai untuk memeriksa duplikat. `default: () => []` artinya: kalau induk tidak mengirim apa-apa, pakai array kosong.

### 8.2 Tambahkan `addTask` dan pasang di `App.vue`

**Template (lengkap, sudah final):**

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>

    <TaskForm :existing-tasks="tasks" @add-task="addTask" />
    <TaskFilter v-model="filter" />
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
import TaskForm from './components/TaskForm.vue'          // ← BARU

// ...
components: { TaskForm, TaskFilter, TaskAlert, TaskList, TaskStats }, // ← BARU

// di dalam methods, tambahkan:
addTask(text) {                                           // ← BARU
  this.tasks.push({ id: Date.now(), text, completed: false })
}
```

| Kode | Fungsi |
|---|---|
| `:existing-tasks="tasks"` | Kirim daftar tugas ke form (untuk cek duplikat). Di template ditulis `kebab-case` (`existing-tasks`), di script `camelCase` (`existingTasks`). Vue menghubungkan keduanya |
| `@add-task="addTask"` | Saat form mengirim event `add-task`, jalankan `addTask` |
| `Date.now()` | Angka milidetik saat ini, dipakai sebagai `id` unik |
| `this.tasks.push({...})` | Menambah tugas baru di akhir array |

### ✅ Hasil yang diharapkan

Aplikasi **utuh** sekarang bisa dipakai: tambah, centang, hapus, filter.

### 🧪 Uji validasi

Coba satu per satu dan perhatikan hasilnya:

| Isian | Hasil yang benar |
|---|---|
| (kosong), klik Tambah | Error: *tidak boleh kosong* |
| `"   "` (hanya spasi) | Error: *tidak boleh kosong* |
| `"ab"` | Error: *minimal 3 karakter* |
| `"belajar vue.js"` (padahal sudah ada "Belajar Vue.js") | Error: *sudah ada* |
| `"Tugas baru saya"` | Berhasil ditambahkan, input kosong lagi |
| Ketik 40+ huruf | Penghitung berubah warna |
| Tekan **Enter** | Sama seperti klik Tambah |
| Setelah error, mulai mengetik | Pesan error hilang |

---

## Langkah 9: Watchers dan Penyimpanan Data

**Tujuan:** data tidak hilang saat refresh, dan judul tab informatif.
**Konsep:** **watcher** dengan `deep` dan `immediate`, `localStorage`.

Ini langkah terakhir untuk `App.vue`. Di sini kita lengkapi dengan **dua watcher** dan ganti data awal agar dibaca dari penyimpanan browser.

### Kode lengkap `src/App.vue` (final)

Cocokkan seluruh isi `App.vue` Anda dengan kode ini:

```vue
<template>
  <main class="container">
    <h1>To-Do List Saya</h1>

    <TaskForm :existing-tasks="tasks" @add-task="addTask" />
    <TaskFilter v-model="filter" />
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

<script>
import TaskForm from './components/TaskForm.vue'
import TaskFilter from './components/TaskFilter.vue'
import TaskAlert from './components/TaskAlert.vue'
import TaskList from './components/TaskList.vue'
import TaskStats from './components/TaskStats.vue'

const STORAGE_KEY = 'todo-demo-tasks'

// Ambil data tersimpan; kalau belum ada, pakai data awal
function loadTasks() {
  try {
    const saved = localStorage.getItem(STORAGE_KEY)
    if (saved) return JSON.parse(saved)
  } catch (e) {
    console.warn('Gagal membaca localStorage', e)
  }
  return [
    { id: 1, text: 'Belajar Vue.js', completed: false },
    { id: 2, text: 'Membuat To-Do List', completed: false },
    { id: 3, text: 'Mengerjakan Tugas', completed: false }
  ]
}

export default {
  name: 'App',
  components: { TaskForm, TaskFilter, TaskAlert, TaskList, TaskStats },

  data() {
    return {
      tasks: loadTasks(),
      filter: 'all' // 'all' | 'active' | 'completed'
    }
  },

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

  methods: {
    addTask(text) {
      this.tasks.push({ id: Date.now(), text, completed: false })
    },
    toggleTask(id) {
      const task = this.tasks.find(t => t.id === id)
      if (task) task.completed = !task.completed
    },
    deleteTask(id) {
      this.tasks = this.tasks.filter(t => t.id !== id)
    }
  },

  watch: {
    // Watcher 1: simpan ke localStorage setiap tasks berubah
    tasks: {
      handler(newTasks) {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(newTasks))
      },
      deep: true
    },
    // Watcher 2: judul tab browser mengikuti jumlah tugas aktif
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

### Penjelasan

| Bagian baru | Fungsi |
|---|---|
| `STORAGE_KEY` | "Nama laci" tempat data disimpan di browser |
| `loadTasks()` | Saat aplikasi dimuat: kalau ada data tersimpan, pakai itu; kalau tidak, pakai 3 tugas awal. `try/catch` mencegah aplikasi rusak kalau penyimpanan bermasalah |
| `tasks: loadTasks()` | Menggantikan array tetap di `data()` |
| **Watcher 1** `tasks` | Setiap `tasks` berubah, simpan ke `localStorage` (diubah jadi teks dengan `JSON.stringify`) |
| `deep: true` | **Wajib** di sini. Tanpa itu, mencentang tugas (mengubah `completed` di dalam objek) tidak terdeteksi |
| **Watcher 2** `activeTasks` | Setiap jumlah tugas aktif berubah, ubah judul tab, misalnya `(2) To-Do List` |
| `immediate: true` | Watcher dijalankan **sekali saat aplikasi dimuat**, jadi judul tab langsung benar tanpa menunggu perubahan |

> 💡 Total watcher dalam proyek: **3** (2 di `App.vue` + 1 di `TaskForm.vue`). Rubrik meminta minimal 2, dan semuanya punya efek yang **terlihat nyata**.

### ✅ Hasil yang diharapkan

- Tambah atau centang tugas, lalu **refresh halaman**. Data tetap ada.
- Judul tab browser menampilkan jumlah tugas aktif, misalnya `(3) To-Do List`.

### 🧪 Percobaan

1. Buka **F12 → Application → Local Storage → localhost**. Anda akan melihat key `todo-demo-tasks` berisi data JSON.
2. Centang tugas. Nilainya di Local Storage ikut berubah.
3. **Eksperimen:** hapus sementara `deep: true`, simpan, lalu centang tugas dan refresh. Status centang **tidak tersimpan**. Kembalikan `deep: true` setelah mencoba.
4. **Mereset data:** di tab Console, ketik `localStorage.clear()` lalu refresh, dan data kembali ke 3 tugas awal.

---
## Langkah 10: Memahami Computed, Methods, dan Watch di App.vue
---

Sekarang `App.vue` Anda sudah berisi lima "peran": `data`, `computed`, `methods`, `watch`, dan komunikasi dengan komponen anak. Bagian ini menjelaskan **siapa mengerjakan apa**, memakai satu analogi: **warung makan**.

### Analogi: Warung Makan

| Di warung | Di `App.vue` | Perannya |
|---|---|---|
| **Stok bahan di gudang** | `data()` → `tasks`, `filter` | Barang asli yang disimpan |
| **Papan "sisa porsi hari ini"** | `computed` | Angka yang **dihitung otomatis dari stok**, hanya untuk dilihat |
| **Pelayan** | `methods` | **Bekerja** ketika dipanggil: menambah, mencoret, membuang |
| **Satpam / CCTV** | `watch` | **Mengawasi** stok. Begitu stok berubah, ia melakukan sesuatu di luar |
| **Titipan pemilik ke pelayan** | `props` (dari induk ke anak) | Data yang diberikan induk |
| **Pelayan melapor ke pemilik** | `$emit` (dari anak ke induk) | Kabar bahwa sesuatu terjadi |

Tiga peran utamanya berbeda tugas:

```
Papan sisa porsi  →  MELAPORKAN   (tidak mengubah stok)
Pelayan           →  MENGERJAKAN  (mengubah stok, kalau disuruh)
Satpam            →  MEREAKSI     (bergerak sendiri saat stok berubah)
```

---

### 1. Data = Stok di Gudang

```js
data() {
  return {
    tasks: loadTasks(),   // daftar tugas
    filter: 'all'         // filter yang dipilih
  }
}
```

Hanya ada **dua** data di `App.vue`. Semua yang lain (jumlah, daftar tersaring, pesan) **diturunkan** dari dua data ini.

> 💡 **Aturan hemat data:** jangan simpan sesuatu di `data()` kalau nilainya bisa dihitung dari data lain. Contohnya, `totalTasks` tidak disimpan sebagai data, karena sudah bisa dihitung dari `tasks.length`.

---

### 2. Computed = Papan "Sisa Porsi"

Bayangkan papan di dinding warung bertuliskan **"Sisa porsi: 7"**.

- Pemilik **tidak menulis angka itu sendiri** setiap ada pembeli. Angkanya dihitung otomatis dari stok.
- Papan itu **tidak boleh dipakai untuk mengambil atau membuang makanan**. Ia hanya menampilkan hasil hitungan.
- Kalau stok tidak berubah, papan **tidak menghitung ulang**. Ia mengingat hasil terakhir (*caching*).

Di `App.vue`:

```js
computed: {
  totalTasks()     { return this.tasks.length },
  activeTasks()    { return this.tasks.filter(t => !t.completed).length },
  completedTasks() { return this.tasks.filter(t => t.completed).length },
  filteredTasks()  {
    if (this.filter === 'active')    return this.tasks.filter(t => !t.completed)
    if (this.filter === 'completed') return this.tasks.filter(t => t.completed)
    return this.tasks
  }
}
```

| Papan warung | Computed di To-Do | Dipakai oleh |
|---|---|---|
| "Total menu" | `totalTasks` | `TaskStats`, `TaskAlert` |
| "Sisa porsi" | `activeTasks` | `TaskStats`, `TaskAlert`, judul tab |
| "Sudah terjual" | `completedTasks` | `TaskStats` |
| "Menu yang ditampilkan sesuai pilihan pelanggan" | `filteredTasks` | `TaskList` |

**Ciri computed:**
- Selalu punya **`return`**.
- Dipanggil **tanpa kurung** di template: `{{ activeTasks }}`.
- **Tidak mengubah data.** Hanya menghitung dari data lain.
- Otomatis menghitung ulang **hanya jika data yang dipakainya berubah**.

---

### 3. Methods = Pelayan

Pelayan **tidak bekerja sendiri**. Ia bergerak **hanya ketika dipanggil** ("Mas, tolong catat pesanan ini!"). Setiap dipanggil, ia bekerja dari awal.

Di `App.vue`:

```js
methods: {
  addTask(text) {
    this.tasks.push({ id: Date.now(), text, completed: false })
  },
  toggleTask(id) {
    const task = this.tasks.find(t => t.id === id)
    if (task) task.completed = !task.completed
  },
  deleteTask(id) {
    this.tasks = this.tasks.filter(t => t.id !== id)
  }
}
```

| Panggilan ke pelayan | Method | Dipanggil dari |
|---|---|---|
| "Tolong catat pesanan baru" | `addTask` | Event `add-task` dari `TaskForm` |
| "Tolong coret pesanan nomor 2" | `toggleTask` | Event `toggle` dari `TaskItem` (lewat `TaskList`) |
| "Tolong buang pesanan nomor 3" | `deleteTask` | Event `delete` dari `TaskItem` (lewat `TaskList`) |

**Siapa yang "memanggil" pelayan?** Pengguna, lewat event: klik tombol, centang checkbox, tekan Enter. Di kode:

```vue
<TaskList @toggle="toggleTask" @delete="deleteTask" />
```

Artinya: *"Kalau ada kabar `toggle`, panggil pelayan `toggleTask`."*

**Ciri method:**
- Dijalankan karena ada **kejadian** (klik, submit, dll.).
- **Boleh mengubah data** (`this.tasks...`).
- Tidak menyimpan hasil. Setiap dipanggil, ia bekerja lagi.

---

### 4. Watch = Satpam / CCTV

Satpam duduk diam mengawasi gudang. **Begitu stok berubah**, ia langsung bereaksi, walau tidak ada yang memanggilnya.

Reaksinya biasanya urusan **di luar hitungan stok**: mencatat ke buku besar, mengganti papan nama toko, membunyikan alarm.

Di `App.vue` ada dua satpam:

```js
watch: {
  tasks: {                       // Satpam 1: mengawasi daftar tugas
    handler(newTasks) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(newTasks))
    },
    deep: true
  },
  activeTasks: {                 // Satpam 2: mengawasi jumlah tugas aktif
    handler(count) {
      document.title = count > 0 ? `(${count}) To-Do List` : 'To-Do List'
    },
    immediate: true
  }
}
```

| Satpam | Mengawasi | Saat berubah, ia... |
|---|---|---|
| Satpam 1 | `tasks` | **Mencatat ke buku besar** (menyimpan ke `localStorage`) |
| Satpam 2 | `activeTasks` | **Mengganti papan nama** (judul tab menjadi `(2) To-Do List`) |

Dua opsi yang sering membingungkan:

| Opsi | Analogi |
|---|---|
| `deep: true` | Satpam **memeriksa isi tiap kotak**, bukan hanya jumlah kotaknya. Tanpa ini, saat satu tugas dicentang (isi kotak berubah, jumlah kotak tetap), satpam tidak sadar |
| `immediate: true` | Satpam **langsung memeriksa sekali saat warung buka**, tidak menunggu ada perubahan dulu |

**Ciri watch:**
- Berjalan **otomatis** saat data yang diawasi berubah.
- Untuk **efek samping**: menyimpan, mengubah judul tab, memanggil server.
- Nama watcher **harus sama persis** dengan nama data yang diawasi.
- Tidak menghasilkan nilai (tidak ada `return` untuk ditampilkan).

---

### 5. Props dan Emit = Komunikasi di Warung

`App.vue` adalah **pemilik warung**. Komponen anak adalah **karyawan**.

| | Props | Emit |
|---|---|---|
| Arah | Induk → anak | Anak → induk |
| Analogi | Pemilik **menitipkan** data ke karyawan | Karyawan **melapor** ke pemilik |
| Contoh | `:total="totalTasks"` ke `TaskStats` | `$emit('toggle', task.id)` dari `TaskItem` |

Karyawan tidak boleh mengubah stok sendiri. Ia hanya **melapor**, lalu pemilik (lewat method) yang memutuskan dan mengubah data. Inilah aturan **props turun, event naik**.

---

### Satu Kejadian, Semua Peran Bekerja

Ikuti satu kejadian: **pengguna mencentang "Belajar Vue.js"**.

```
1. Pengguna mencentang checkbox di TaskItem
        ↓
2. EMIT: TaskItem melapor "toggle" membawa id tugas
        ↓
3. METHOD toggleTask dipanggil (pelayan mencoret pesanan)
   → tasks[0].completed berubah menjadi true     ← DATA berubah
        ↓
4. Dua hal terjadi sebagai REAKSI atas perubahan data:
   • COMPUTED menghitung ulang → activeTasks: 3 menjadi 2, completedTasks: 0 menjadi 1
   • WATCH bereaksi            → simpan ke localStorage, judul tab menjadi "(2) To-Do List"
        ↓
5. TAMPILAN diperbarui oleh Vue (props baru mengalir ke komponen anak)
```

Pembagian tugasnya:

- **Method** yang **mengubah data**.
- **Computed** yang **menghitung** hasil dari data.
- **Watch** yang **bereaksi** terhadap perubahan.

> 💡 Method selalu bergerak **lebih dulu**, karena ia yang mengubah data. Computed dan watch hanya **bereaksi** setelahnya. Urutan *di antara* computed dan watch tidak perlu dihafalkan.

---

### Cara Memilih: Tiga Pertanyaan

```
Apakah hasilnya hanya untuk DITAMPILKAN / dihitung dari data lain?
        │
        ├── Ya  ──►  COMPUTED
        │
        └── Tidak
              │
              Apakah dijalankan karena PENGGUNA melakukan sesuatu (klik, submit)?
              │
              ├── Ya  ──►  METHOD
              │
              └── Tidak, harus jalan OTOMATIS saat data berubah
                          (menyimpan, ubah judul, panggil API)
                          ──►  WATCH
```

### Tabel Ringkas

| | Computed | Method | Watch |
|---|---|---|---|
| Analogi | Papan sisa porsi | Pelayan | Satpam |
| Kapan jalan? | Otomatis, jika data yang dipakai berubah | Saat **dipanggil** | Otomatis, jika data yang diawasi berubah |
| Tugas | Menghitung nilai | Mengerjakan aksi | Bereaksi dengan efek samping |
| Menyimpan hasil (cache)? | ✅ Ya | ❌ Tidak | — |
| Boleh mengubah data? | ❌ Jangan | ✅ Boleh | ✅ Boleh (hati-hati) |
| Perlu `return`? | ✅ Ya | Tergantung | ❌ Tidak |
| Dipanggil di template | `{{ activeTasks }}` (tanpa kurung) | `@click="deleteTask"` atau `{{ fn() }}` | Tidak dipanggil, jalan sendiri |
| Contoh di To-Do | `activeTasks`, `filteredTasks` | `addTask`, `toggleTask`, `deleteTask` | Simpan ke `localStorage`, judul tab |

---

### Pertanyaan yang Sering Muncul

**1. "Kenapa `activeTasks` tidak dibuat method saja?"**
Bisa, hasilnya sama. Tetapi method **menghitung ulang setiap dipanggil**, sedangkan computed **hanya menghitung ulang kalau `tasks` berubah**. `activeTasks` dipakai di beberapa tempat (`TaskAlert`, `TaskStats`, judul tab). Dengan computed, dihitung sekali lalu dipakai bersama.
*Analogi:* papan sisa porsi dilihat 20 pelanggan. Lebih hemat kalau dihitung sekali daripada 20 kali.

**2. "Kenapa menyimpan ke `localStorage` tidak ditaruh di computed?"**
Karena menyimpan adalah **aksi**, bukan **hitungan**. Papan sisa porsi hanya boleh menampilkan angka, bukan menulis di buku besar. Pekerjaan seperti itu milik satpam.

**3. "Kenapa menyimpan tidak di dalam `addTask`, `toggleTask`, dan `deleteTask` saja?"**
Bisa, tetapi kode simpan harus ditulis **tiga kali**. Kalau nanti ada method keempat (misalnya "hapus semua yang selesai"), mudah lupa menambahkannya. Dengan satpam, **apa pun yang mengubah `tasks`** otomatis tersimpan.

**4. "Kenapa `activeTasks` diawasi watcher, padahal sudah computed?"**
Computed hanya **menghitung**. Mengubah `document.title` adalah efek di luar komponen, jadi butuh watcher untuk **bereaksi** pada hasil hitungan itu.

**5. "Apa bedanya computed dan watch? Keduanya otomatis."**
Computed **menghasilkan nilai** (ada `return`) yang bisa ditampilkan. Watch **melakukan tindakan** tanpa menghasilkan nilai. Kalau pertanyaannya "berapa?", pakai computed. Kalau pertanyaannya "lalu lakukan apa?", pakai watch.

**6. "Kenapa `filteredTasks` computed, bukan method?"**
Hasilnya adalah **nilai turunan** dari `tasks` dan `filter`, hanya untuk ditampilkan, dan tidak mengubah apa pun. Itu ciri khas computed.

---

### 🧪 Percobaan: Melihat Ketiganya Bekerja

Tempel baris `console.log` ini **sementara** di `App.vue`, lalu buka **Console (F12)**.

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

Centang satu tugas, lalu baca log di Console. Ketiga pesan akan muncul:

```
🧑‍🍳 method toggleTask dipanggil     ← selalu paling pertama
🧮 computed activeTasks dihitung   ← urutan dua baris ini
👮 watch tasks: menyimpan...       ← bisa saling bertukar
```

**Yang perlu diperhatikan:**
- Method **selalu** muncul lebih dulu, karena ia yang mengubah data.
- Computed dan watch muncul **sesudahnya**. Urutan di antara keduanya bisa berbeda tergantung cara Vue menjadwalkan pekerjaan. Itu normal dan bukan kesalahan.

**Eksperimen susulan:**

1. **Membuktikan caching.** Tulis `{{ activeTasks }}` dua kali di template. Log computed tetap muncul **satu kali**. Lalu ubah menjadi method dan panggil dua kali, log muncul **dua kali**.
2. **Membuktikan `deep`.** Hapus `deep: true`, lalu centang tugas. Log satpam **tidak muncul**, dan data tidak tersimpan. Kembalikan `deep: true`.
3. **Membuktikan `immediate`.** Hapus `immediate: true` pada watcher `activeTasks`, lalu refresh. Judul tab tidak langsung terisi jumlah tugas sampai ada perubahan. Kembalikan setelah mencoba.

> ⚠️ **Hapus semua `console.log`** setelah percobaan selesai.

---

### Kesalahan Umum

| Kesalahan | Kenapa keliru | Perbaikan |
|---|---|---|
| Menulis `{{ activeTasks() }}` (pakai kurung) padahal computed | Computed dipakai seperti **data**, bukan fungsi | `{{ activeTasks }}` |
| Lupa `return` di computed | Papan sisa porsi kosong, tidak menampilkan apa pun | Tambahkan `return` |
| Mengubah data di dalam computed | Papan tidak boleh mengambil makanan dari gudang | Pindahkan ke method |
| Memakai watch untuk hal yang bisa dihitung | Terlalu rumit dan rawan bug | Pakai computed |
| Nama watcher beda dengan nama data | Satpam menjaga gudang yang salah | Samakan dengan nama data persis |
| Lupa `deep: true` pada array/objek | Satpam hanya menghitung jumlah kotak, bukan isinya | Tambahkan `deep: true` |
| Menyimpan nilai turunan di `data()` | Harus diperbarui manual dan mudah tidak sinkron | Jadikan computed |
| Memanggil method di template tanpa perlu, misalnya `{{ hitung() }}` untuk nilai tampilan | Dijalankan ulang setiap tampilan diperbarui | Jadikan computed |

---

### Kuis Cepat

Sebutkan: **computed, method, atau watch?**

1. Menghitung jumlah tugas yang sudah selesai.
2. Menghapus tugas saat tombol **Hapus** diklik.
3. Menyimpan data ke `localStorage` setiap daftar tugas berubah.
4. Menyaring tugas sesuai tombol filter yang dipilih.
5. Mengubah judul tab saat jumlah tugas aktif berubah.
6. Menambah tugas baru setelah form dikirim.
7. Menampilkan pesan *"Tugas minimal 3 karakter"* berdasarkan isi input.

<details>
<summary>Lihat jawaban</summary>

1. **Computed** (`completedTasks`): menghitung nilai turunan.
2. **Method** (`deleteTask`): aksi karena klik pengguna.
3. **Watch** (`tasks`): efek samping otomatis saat data berubah.
4. **Computed** (`filteredTasks`): nilai turunan dari `tasks` dan `filter`.
5. **Watch** (`activeTasks`): efek di luar komponen.
6. **Method** (`addTask`): aksi yang mengubah data.
7. **Method** (`validate`) yang dipanggil saat submit. Pesannya disimpan di data `errorMessage` dan ditampilkan dengan `v-if`.
</details>

---

### Ringkasan dalam Tiga Kalimat

> **Method itu pelayan:** bergerak kalau disuruh, dan boleh mengubah stok.
> **Computed itu papan hitungan:** otomatis menghitung dari stok, hanya untuk dilihat.
> **Watch itu satpam:** otomatis bereaksi kalau stok berubah, untuk urusan di luar hitungan.

---

## Langkah 11: Pengujian Akhir

Jalankan daftar periksa ini sebelum mengumpulkan atau mendemokan.

### Fungsi aplikasi

- [ ] Tambah tugas dengan klik **Tambah**
- [ ] Tambah tugas dengan tekan **Enter**
- [ ] Validasi: kosong, spasi saja, kurang dari 3 huruf, duplikat
- [ ] Pesan error hilang saat mulai mengetik
- [ ] Centang tugas: teks tercoret, muncul ✓ Selesai
- [ ] Hapus tugas
- [ ] Filter **Semua / Aktif / Selesai** bekerja dan pesan kosong menyesuaikan
- [ ] Pesan `TaskAlert` berubah di 4 kondisi (kosong, semua selesai, aktif lebih dari 5, normal)
- [ ] Statistik selalu cocok dengan daftar
- [ ] Refresh halaman: data tetap ada
- [ ] Judul tab menunjukkan jumlah tugas aktif
- [ ] Tidak ada pesan error atau peringatan merah di Console (F12)

### Kualitas kode

- [ ] Nama file komponen **PascalCase** dan terdiri dari dua kata atau lebih
- [ ] Setiap komponen punya **satu tanggung jawab**
- [ ] Tidak ada komponen anak yang mengubah props langsung
- [ ] Semua `v-for` memakai `:key` unik (`task.id`, `option.value`)
- [ ] Semua event terdaftar di `emits`
- [ ] Indentasi rapi (di VS Code: **Shift + Alt + F** untuk format otomatis)

---

## 11. Peta Konsep dan Kriteria Penilaian

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

### Peta ke rubrik penilaian

| Kriteria | Bukti di proyek | Dibuat di |
|---|---|---|
| **1.1** Arsitektur dan struktur proyek | Folder `components/` dan `assets/`; 6 komponen berpenamaan PascalCase; satu tanggung jawab per komponen; props turun, event naik | Langkah 2 sampai 8 |
| **1.2** Data binding, directive, list rendering | Mustache (`TaskItem`, `TaskForm`); `v-text` (`TaskStats`); `v-html` (`TaskAlert`); `v-bind` (`:class`, `:id`, `:for`, `:checked`); `v-model` (`TaskForm`, `TaskFilter`); `v-for` + `:key` (`TaskList`, `TaskFilter`) | Langkah 3, 4, 6, 7, 8 |
| **1.3** Conditional | `v-if / v-else-if / v-else` (`TaskAlert`, `TaskList`, pesan error); `v-show` (`TaskItem`) | Langkah 4, 7, 8 |
| **1.4** Computed dan methods | Computed: `totalTasks`, `activeTasks`, `completedTasks`, `filteredTasks`. Methods: `addTask`, `toggleTask`, `deleteTask`, `validate`, `submitTask` | Langkah 3, 5, 6, 8 |
| **1.5** Watchers (minimal 2) | 3 watcher: simpan `localStorage`, judul tab, hapus error saat mengetik | Langkah 8, 9 |
| **1.6** Formulir dan validasi | `<label>`, `maxlength`, penghitung karakter, validasi kosong / minimal 3 / duplikat, pesan error terlihat, `trim()`, kirim dengan Enter | Langkah 8 |

---

## 12. Kesalahan yang Sering Terjadi

| Gejala | Penyebab umum | Solusi |
|---|---|---|
| Halaman putih, Console menampilkan `Failed to resolve import './components/HelloWorld.vue'` | `App.vue` bawaan masih mengimpor file yang sudah dihapus | Ganti seluruh isi `App.vue` (Langkah 2.4) |
| `Failed to resolve component: TaskItem` | Lupa mengimpor atau mendaftarkan di `components` | Lakukan 3 langkah memakai komponen |
| Peringatan *"Component name should always be multi-word"* | Nama komponen satu kata | Pakai nama dua kata seperti `TaskItem` |
| Peringatan *"Extraneous non-emits event listeners"* | Event belum didaftarkan di `emits` | Tambahkan ke `emits: [...]` |
| Angka statistik tampil `NaN` atau props error | Lupa titik dua: `total="totalTasks"` | Tulis `:total="totalTasks"` |
| Tombol **Hapus** atau checkbox tidak bekerja | `@toggle` / `@delete` belum dipasang di `TaskList`, atau `TaskList` lupa meneruskan event | Periksa `@toggle="toggleTask"` di `App.vue` dan `@toggle="$emit('toggle', $event)"` di `TaskList.vue` |
| Filter tidak mengubah daftar | `TaskList` masih menerima `:tasks="tasks"` | Ganti ke `:tasks="filteredTasks"` |
| Tombol filter tidak berubah warna | Props bernama selain `modelValue`, atau event bukan `update:modelValue` | Samakan ejaan persis |
| Data tidak tersimpan setelah refresh | `deep: true` terhapus, atau `STORAGE_KEY` berbeda | Periksa watcher `tasks` |
| Tugas lama muncul lagi / tidak sesuai harapan | Data lama tersimpan di Local Storage | Jalankan `localStorage.clear()` di Console, lalu refresh |
| Error `Unexpected token` saat membaca data | Isi Local Storage rusak | `localStorage.clear()` lalu refresh |
| Tampilan polos tanpa warna | `import './assets/main.css'` hilang di `main.js` | Periksa Langkah 2.3 |
| Error kurung atau tag saat menyalin | Komentar `<!-- ← BARU -->` ikut tersalin | Hapus komentar penanda dari kode Anda |

> 💡 **Kebiasaan baik:** selalu buka **Console (F12)** saat membangun. Pesan error Vue umumnya menyebut nama komponen dan penyebabnya.

---

## 13. Latihan Pengembangan

Kerjakan setelah aplikasi utama selesai. Urut dari termudah.

| Level | Tugas | Konsep yang dilatih |
|---|---|---|
| ⭐ | Tambahkan tombol **"Hapus semua yang selesai"** | Method baru, `filter()` array, event |
| ⭐ | Tampilkan **tanggal dibuat** pada tiap tugas | Menambah properti data, mustache |
| ⭐⭐ | Tambahkan **edit tugas** (klik dua kali untuk mengubah teks) | `v-if` di dalam `TaskItem`, `v-model`, event baru |
| ⭐⭐ | Tambahkan **prioritas** (Rendah/Sedang/Tinggi) lewat `<select>` | `v-model` pada `select`, `:class` dinamis |
| ⭐⭐ | Tombol **"Tandai semua selesai"** | Method, loop pada array |
| ⭐⭐⭐ | **Urutkan** tugas berdasarkan prioritas | Computed bertingkat |
| ⭐⭐⭐ | Tambahkan **kolom pencarian** tugas | `v-model`, computed, watcher |
| 🏆 | Ubah salah satu komponen ke **Composition API** (`<script setup>`) | Perbandingan gaya penulisan |

> ✍️ **Tugas refleksi:** tuliskan dengan kalimat sendiri, *kenapa `activeTasks` dibuat computed dan bukan method?* Gunakan hasil percobaan di Bab 8 `penjelasanVue3.md` sebagai bukti.

---

**Diperbarui:** Oktober 2026 · **Versi:** 1.0 (Vue 3) · **Lisensi:** untuk keperluan pembelajaran
