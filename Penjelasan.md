# 📚 Dokumentasi Vue.js - Penjelasan Lengkap Poin 1-8

> Materi lengkap pembelajaran Vue.js Framework untuk pemahaman mendalam tentang konsep dasar hingga lanjutan

---

## 📋 Daftar Isi

1. [Mustaches & Text Directives](#1-mustaches--text-directives)
2. [Conditional Rendering](#2-conditional-rendering)
3. [Data Binding](#3-data-binding)
4. [Computed Properties & Methods](#4-computed-properties--methods)
5. [Watchers](#5-watchers)
6. [List Rendering](#6-list-rendering)
7. [Filters](#7-filters)
8. [Custom Components](#8-custom-components)
9. [Hubungan Data & Reactivity](#-hubungan-data--reactivity)

---

## 1. Mustaches & Text Directives

### 📝 HTML Template

```html
<!-- Mustaches (Kurung Kurawal) -->
<p><strong>Mustaches:</strong> {{ studentName }}</p>

<!-- v-text Directive -->
<p><strong>v-text:</strong> <span v-text="studentNIM"></span></p>

<!-- v-html Directive -->
<p><strong>v-html:</strong> <span v-html="formattedText"></span></p>
```

### 🔧 Script Data

```javascript
data: {
    studentName: 'John Doe',
    studentNIM: '2024001',
    formattedText: '<strong style="color: #667eea;">Text dengan HTML</strong>',
}
```

### 🔗 Hubungan & Penjelasan

- **Mustaches (`{{ }}`)**: Menampilkan data sebagai teks biasa (escaped)
- **v-text**: Mengganti seluruh konten elemen dengan teks
- **v-html**: Menginterpretasikan konten sebagai HTML (⚠️ hati-hati terhadap XSS)
- **Reactivity**: Ketika data berubah, tampilan otomatis diperbarui

---

## 2. Conditional Rendering

### 📝 HTML Template

```html
<!-- Conditional Rendering dengan v-if -->
<div v-if="score >= 80" class="alert alert-success">
    <strong>Luar Biasa!</strong> Nilai A - {{ score }}
</div>
<div v-else-if="score >= 70" class="alert alert-info">
    <strong>Bagus!</strong> Nilai B - {{ score }}
</div>
<div v-else class="alert alert-warning">
    <strong>Perlu Peningkatan!</strong> Nilai D/E - {{ score }}
</div>

<!-- Conditional Display dengan v-show -->
<div v-show="score >= 60" class="card">
    <p>✓ Mahasiswa dinyatakan LULUS</p>
</div>
```

### 🔧 Script Data

```javascript
data: {
    score: 75,
}
```

### 🔗 Hubungan & Penjelasan

- **v-if/v-else-if/v-else**: Menambah/menghapus elemen dari DOM
- **v-show**: Hanya mengubah CSS display property
- **Perbedaan**: 
  - `v-if` lebih berat (manipulasi DOM)
  - `v-show` lebih ringan (hanya CSS)

| Directive | Render Cost | Toggle Cost | Use Case |
|-----------|-------------|-------------|----------|
| v-if | Tinggi | Rendah | Kondisi jarang berubah |
| v-show | Rendah | Tinggi | Toggle sering terjadi |

---

## 3. Data Binding

### 📝 HTML Template

```html
<!-- Two-way Binding dengan v-model -->
<input type="text" v-model="bindingName" placeholder="Ketik nama...">
<p>Hasil: {{ bindingName }}</p>

<!-- One-way Binding dengan v-bind -->
<select v-model="selectedColor">
    <option value="blue">Biru</option>
    <option value="green">Hijau</option>
    <option value="red">Merah</option>
</select>

<div class="card" :style="{ borderLeftColor: selectedColor }">
    <p>Box ini berubah warna border sesuai pilihan: <strong>{{ selectedColor }}</strong></p>
</div>
```

### 🔧 Script Data

```javascript
data: {
    bindingName: '',
    selectedColor: 'blue',
    productPrice: 100000,
}
```

### 🔗 Hubungan & Penjelasan

- **v-model**: Two-way binding (data ↔ UI)
- **v-bind / `:`**: One-way binding (data → UI)
- **Shorthand**: `:` sama dengan `v-bind:`

#### Data Flow Comparison

```
One-Way Binding (v-bind):
┌──────────┐
│   Data   │ ────────────> │    UI    │
└──────────┘               └──────────┘

Two-Way Binding (v-model):
┌──────────┐
│   Data   │ <───────────> │    UI    │
└──────────┘               └──────────┘
```

---

## 4. Computed Properties & Methods

### 📝 HTML Template

```html
<input type="number" v-model.number="length" placeholder="Panjang">
<input type="number" v-model.number="width" placeholder="Lebar">

<p><strong>Luas (Computed):</strong> {{ area }} m²</p>
<p><strong>Keliling (Computed):</strong> {{ perimeter }} m</p>

<button class="btn" @click="calculateTotal">Hitung dengan Method</button>
<p v-if="methodResult"><strong>Result:</strong> {{ methodResult }}</p>
```

### 🔧 Script Implementation

```javascript
data: {
    length: 10,
    width: 5,
    methodResult: '',
},

computed: {
    area() {
        return this.length * this.width;
    },
    perimeter() {
        return 2 * (this.length + this.width);
    }
},

methods: {
    calculateTotal() {
        const total = this.area + this.perimeter;
        this.methodResult = `Total Luas + Keliling = ${total}`;
    }
}
```

### 🔗 Hubungan & Penjelasan

#### Computed Properties:
- ✅ Cached berdasarkan dependencies
- ✅ Otomatis di-update ketika data berubah
- ✅ Hanya dihitung ketika diperlukan

#### Methods:
- ✅ Dieksekusi setiap kali dipanggil
- ✅ Dapat menerima parameter
- ✅ Digunakan untuk event handlers

#### Comparison Table

| Feature | Computed | Methods |
|---------|----------|---------|
| Caching | Ya | Tidak |
| Parameters | Tidak | Ya |
| Usage | `{{ computed }}` | `{{ method() }}` |
| Re-evaluation | Saat dependency berubah | Setiap render |
| Best for | Derived data | Event handling |

---

## 5. Watchers

### 📝 HTML Template

```html
<input type="text" v-model="watchedText" placeholder="Ketik untuk melihat watcher...">

<div v-if="watchMessage" class="alert alert-info">
    {{ watchMessage }}
</div>

<div class="card">
    <p><strong>Jumlah karakter:</strong> {{ watchedText.length }}</p>
    <p><strong>Perubahan terdeteksi:</strong> {{ changeCount }} kali</p>
</div>
```

### 🔧 Script Implementation

```javascript
data: {
    watchedText: '',
    watchMessage: '',
    changeCount: 0,
},

watch: {
    watchedText(newValue, oldValue) {
        this.changeCount++;
        this.watchMessage = `Text berubah dari "${oldValue}" menjadi "${newValue}"`;
        
        setTimeout(() => {
            this.watchMessage = '';
        }, 3000);
    },
    score(newScore) {
        console.log('Nilai berubah menjadi:', newScore);
    }
}
```

### 🔗 Hubungan & Penjelasan

**Watchers**: Memantau perubahan data spesifik

#### Use Cases:
- 🔍 Performing asynchronous operations
- ✔️ Data validation
- ⚡ Side effects ketika data berubah
- 📡 API calls based on data changes

#### Parameters: 
- `newValue`: Nilai baru setelah perubahan
- `oldValue`: Nilai sebelum perubahan

---

## 6. List Rendering

### 📝 HTML Template

```html
<!-- Looping Array dengan Index -->
<div v-for="(student, index) in students" :key="index" class="student-card">
    <h3>{{ index + 1 }}. {{ student.name }}</h3>
    <p>NIM: {{ student.nim }}</p>
    <p>Jurusan: {{ student.major }}</p>
</div>

<!-- Looping Object Properties -->
<tr v-for="(value, key) in campusInfo" :key="key">
    <td><strong>{{ key }}</strong></td>
    <td>{{ value }}</td>
</tr>
```

### 🔧 Script Data

```javascript
data: {
    students: [
        { 
            name: 'Ahmad Rizki', 
            nim: '2024001', 
            major: 'Teknik Informatika', 
            gpa: 3.8 
        },
        { 
            name: 'Dewi Lestari', 
            nim: '2024002', 
            major: 'Sistem Informasi', 
            gpa: 3.9 
        },
        // ... data lainnya
    ],
    campusInfo: {
        nama: 'Universitas Teknologi Indonesia',
        alamat: 'Jl. Pendidikan No. 123',
        akreditasi: 'A',
        jumlahMahasiswa: 5000,
        tahunBerdiri: 1990
    }
}
```

### 🔗 Hubungan & Penjelasan

- **v-for**: Mengiterasi array atau object
- **:key**: ⚠️ Wajib untuk performa dan tracking elemen
- **Syntax**: 
  - Array: `(item, index) in array`
  - Object: `(value, key) in object`

#### Array vs Object Iteration

```javascript
// Array Iteration
v-for="(item, index) in items"
// item = element value
// index = 0, 1, 2, ...

// Object Iteration
v-for="(value, key, index) in object"
// value = property value
// key = property name
// index = 0, 1, 2, ...
```

---

## 7. Filters

### 📝 HTML Template

```html
<p><strong>Teks Normal:</strong> {{ filterText }}</p>
<p><strong>UPPERCASE Filter:</strong> {{ filterText | uppercase }}</p>
<p><strong>Capitalize Filter:</strong> {{ filterText | capitalize }}</p>
<p><strong>Tanggal:</strong> {{ currentDate | formatDate }}</p>
<p><strong>Harga:</strong> {{ 150000 | currency }}</p>
```

### 🔧 Script Implementation

```javascript
// Global Filters
Vue.filter('uppercase', function(value) {
    if (!value) return '';
    return value.toString().toUpperCase();
});

Vue.filter('capitalize', function(value) {
    if (!value) return '';
    return value.charAt(0).toUpperCase() + value.slice(1).toLowerCase();
});

Vue.filter('formatDate', function(value) {
    const date = new Date(value);
    const options = { year: 'numeric', month: 'long', day: 'numeric' };
    return date.toLocaleDateString('id-ID', options);
});

Vue.filter('currency', function(value) {
    return 'Rp ' + value.toLocaleString('id-ID');
});

// Data
data: {
    filterText: 'demo vue.js framework',
    currentDate: new Date()
}
```

### 🔗 Hubungan & Penjelasan

**Filters**: Memformat data untuk ditampilkan

#### Key Points:
- 🔗 **Chaining**: Bisa digabung `{{ data | filter1 | filter2 }}`
- 🌍 **Global vs Local**: Bisa didefinisikan secara global atau local dalam component
- 📝 **Syntax**: `{{ value | filterName }}`

#### Filter Types

| Type | Definition Location | Usage |
|------|-------------------|-------|
| Global | `Vue.filter('name', fn)` | Available everywhere |
| Local | Inside component | Only in that component |

#### Example Chaining:

```javascript
{{ text | lowercase | capitalize }}
// "HELLO WORLD" → "hello world" → "Hello world"
```

---

## 8. Custom Components

### 📝 HTML Template

```html
<student-info 
    name="Budi Santoso"
    nim="123456789"
    major="Teknik Informatika"
    :gpa="3.8">
</student-info>

<counter-component></counter-component>
```

### 🔧 Script Implementation

```javascript
// Component Student Info
Vue.component('student-info', {
    props: ['name', 'nim', 'major', 'gpa'],
    template: `
        <div class="card" style="margin: 15px 0;">
            <h3>{{ name }}</h3>
            <p><strong>NIM:</strong> {{ nim }}</p>
            <p><strong>Jurusan:</strong> {{ major }}</p>
            <p><strong>IPK:</strong> {{ gpa }}</p>
            <span class="badge" :class="gpa >= 3.5 ? 'badge-success' : 'badge-danger'">
                {{ status }}
            </span>
        </div>
    `,
    computed: {
        status() {
            return this.gpa >= 3.5 ? 'Cumlaude' : 'Reguler';
        }
    }
});

// Component Counter
Vue.component('counter-component', {
    data() {
        return {
            count: 0
        }
    },
    template: `
        <div class="card">
            <h3>Counter Component</h3>
            <p style="font-size: 24px; font-weight: bold;">Count: {{ count }}</p>
            <button class="btn" @click="count++">Tambah</button>
            <button class="btn btn-danger" @click="count--">Kurang</button>
            <button class="btn" @click="count = 0">Reset</button>
        </div>
    `
});
```

### 🔗 Hubungan & Penjelasan

- **Props**: Data yang diterima dari parent component
- **Local State**: Data lokal dalam component (`count`)
- **Reusability**: Bisa digunakan berkali-kali dengan data berbeda
- **Encapsulation**: Masing-masing component memiliki state sendiri

#### Component Communication

```
Parent Component
    ↓ (props)
Child Component
    ↑ (events)
Parent Component
```

#### Props Types

```javascript
props: {
    // Simple
    name: String,
    
    // With validation
    gpa: {
        type: Number,
        required: true,
        validator: (value) => value >= 0 && value <= 4
    }
}
```

---

## 🔄 Hubungan Data & Reactivity

### Data Flow Diagram

```
┌─────────────────────────────────────────┐
│        Data (Vue Instance)              │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│    Computed Properties (Derived Data)   │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│  Template Binding ({{ }}, v-text, ...)  │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│   User Interaction (v-model, @click)    │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│      Methods (Event Handlers)           │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│   Data Update (Reactivity Trigger)      │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│      Watchers (Side Effects)            │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│   UI Update (Automatic Re-render)       │
└─────────────────────────────────────────┘
```

### Reactivity System

1. **Data Changes** → Vue mendeteksi perubahan
2. **Dependency Tracking** → Computed properties dan watchers di-notify
3. **Virtual DOM Update** → Vue menghitung perubahan minimal
4. **DOM Patching** → Browser DOM diperbarui secara efisien

### Key Concepts

| Concept | Description |
|---------|-------------|
| **Single Source of Truth** | Data disimpan di Vue instance |
| **One-Way Data Flow** | Parent → Child melalui props |
| **Two-Way Binding** | Hanya untuk form inputs dengan v-model |
| **Component Communication** | Props down, events up |

### Vue Reactivity Flow

```javascript
// 1. Data Definition
data: {
    message: 'Hello'
}

// 2. Getter/Setter Created (Internally by Vue)
Object.defineProperty(data, 'message', {
    get() { /* track dependency */ },
    set(newValue) { /* trigger update */ }
})

// 3. When data changes
this.message = 'World'  // Setter triggered

// 4. Update queue
// - Re-evaluate computed properties
// - Trigger watchers
// - Update Virtual DOM
// - Patch Real DOM
```

---

## 🎯 Kesimpulan

Setiap poin dalam Vue.js saling terhubung membentuk ecosystem yang kohesif:

1. ✅ **Data** sebagai sumber kebenaran
2. ✅ **Directives** sebagai penghubung data dan template
3. ✅ **Components** untuk modularitas dan reusability
4. ✅ **Reactivity System** yang otomatis menyinkronkan data dan UI

### Best Practices Summary

| Aspect | Recommendation |
|--------|----------------|
| **Data Binding** | Gunakan v-model untuk forms, v-bind untuk lainnya |
| **Conditionals** | v-if untuk lazy loading, v-show untuk frequent toggles |
| **Loops** | Selalu gunakan :key dengan unique identifier |
| **Computed vs Methods** | Computed untuk derived data, Methods untuk actions |
| **Watchers** | Gunakan untuk async operations atau side effects |
| **Components** | Keep them small, focused, and reusable |

---

## 📚 Resources

- [Vue.js Official Documentation](https://vuejs.org/)
- [Vue.js API Reference](https://vuejs.org/api/)
- [Vue.js Style Guide](https://vuejs.org/style-guide/)

---

## 📝 License

This documentation is created for educational purposes.

---

**Last Updated**: November 2025  
**Version**: 1.0  
**Author**: [Your Name]
