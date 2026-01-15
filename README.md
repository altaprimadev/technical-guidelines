# 🛠 Technical Guidelines
> version 1.0

Dokumen ini adalah standar resmi untuk pengembangan aplikasi di lingkungan kerja. Tujuannya adalah memastikan codebase konsisten, berperforma tinggi, dan mudah di-maintain.

## Type Safety

Penerapan TypeSafe bukan sebagai beban, tapi sebagai asuransi keamanan kode. Fokus utama-nya adalah Codebase Visibility; setiap bagian kode harus transparan dan dapat diprediksi nilainya. Dengan sistem tipe yang jelas, diharapkan menekan faktor "Fear of the Unknown"—rasa takut saat melakukan refactoring atau perubahan karena tidak tahu efek samping tersembunyinya.

### Strictness

1. No any Policy: Penggunaan any dilarang keras. Jika tipe data benar-benar tidak diketahui, gunakan unknown dan lakukan type narrowing.

2. Strict Mode: File `tsconfig.json` wajib mengaktifkan `"strict": true`.

3. Explicit Return Types: Fungsi yang kompleks atau bagian dari API publik wajib mencantumkan tipe return secara eksplisit.

### Type vs Interface

1. Interface: Gunakan untuk mendefinisikan struktur objek atau kontrak kelas (mendukung declaration merging).

2. Type: Gunakan untuk unions, intersections, primitives, tuples, dan utility types.

### Enums vs Const Objects

Hindari enum karena masalah overhead pada hasil kompilasi. Gunakan as const atau string literal types untuk performa dan tree-shaking yang lebih baik.

```ts
// ✅ Recommended
export const STATUS = {
  IDLE: 'idle',
  LOADING: 'loading',
  SUCCESS: 'success'
} as const;

export type Status = typeof STATUS[keyof typeof STATUS];
```

## Naming Conventions

Konsistensi adalah kunci agar kode bisa dibaca seperti cerita.

| Entitas | Gaya Penulisan | Contoh |
| --- | --- | --- |
| Variable / Function | camelCase | `getUserProfile`, `isLoading` |
| Class / Interface / Type | PascalCase | `AuthService`, `UserResponse` |
| Constant (Global)| UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| Folder / File | kebab-case | `user-profile/`, `use-auth.ts` |
| Boolean | Prefix is, has, should | `isValid`, `hasPermission` |

## Clean Code & Logic

### Early Returns

Jangan biarkan logika terkubur di dalam nested if. Gunakan early return untuk menangani edge cases di awal fungsi.

```ts
// ✅ Good
function processOrder(order: Order) {
  if (!order.isValid) return;
  if (order.isExpired) throw new Error('Expired');
  
  // Logika utama di sini
}
```

### Immutability

Selalu utamakan const daripada let. Jangan pernah gunakan var. Hindari mutasi objek secara langsung; gunakan spread operator.
umumnya ini akan di atur di `eslint.config.mjs`

### Functional Approach

Utamakan metode array modern (map, filter, reduce, some, every) daripada loop for manual, kecuali pada bagian yang sangat kritis terhadap performa (iterasi jutaan data).

## Performance & Optimization

### Memory Management

1. Cleanup: Selalu bersihkan event listeners, subscriptions, dan timers pada lifecycle penghancuran komponen/modul untuk mencegah memory leaks.

2. Avoid Closures in Loops: Berhati-hatilah membuat fungsi di dalam loop karena bisa membebani alokasi memori.

### Bundle Size & Tree Shaking

1. Named Exports: Gunakan named exports (export const ...) daripada default export untuk mendukung tree-shaking yang maksimal.

2. Lazy Loading: Gunakan dynamic import untuk modul yang besar atau tidak diperlukan saat initial load.

### Computational Efficiency

Gunakan Memoization (seperti useMemo di React) untuk kalkulasi berat yang sering dipanggil dengan input yang sama.

## Stay Agnostic & Lean Dependencies

Selalu jaga agar aplikasi tidak terlalu dependen pada modul eksternal, terutama untuk tugas-tugas sepele. Ini krusial untuk Future Proofing dan efisiensi operasional.

### Native First

Gunakan fitur bawaan JavaScript modern (ES6+) sebelum mempertimbangkan library (misal: gunakan Array.flat() daripada lodash.flatten, atau Intl daripada moment.js).

Hindari library utilitas mikro (seperti is-odd, left-pad). Tulis sendiri jika logikanya sederhana.

### Dependency Impact

1. Deployment Time: Setiap tambahan package memperlambat waktu npm install di pipeline CI/CD. Pikirkan matang-matang sebelum melakukan npm install.

2. Security & Deprecation: Modul eksternal rawan deprecated atau memiliki celah keamanan. Semakin sedikit dependencies, semakin kecil attack surface kita.

### Context-Based Usage

Gunakan library hanya jika ia menyelesaikan masalah yang sangat kompleks (misal: state management skala besar, heavy charts, atau date manipulation antar timezone yang rumit). Pastikan penggunaan benar-benar sesuai kebutuhan bisnis, bukan sekadar mengikuti tren.

### Async & Error Handling

1. Async/Await: Selalu gunakan async/await daripada .then() berantai untuk keterbacaan yang lebih baik.

2. Try-Catch: Setiap operasi async atau operasi yang berisiko gagal wajib dibungkus dalam try-catch dengan penanganan error yang jelas (jangan biarkan catch block kosong).

3. Custom Errors: Gunakan class error kustom untuk membedakan antara operational error dan programmer error.

---

# Tools and Dependencies
1. Node.js v24
2. Yarn (stable) `$yarn set version stable`

## Backer
<a href="https://github.com/adhemukhlis"><img src="https://github.com/adhemukhlis.png" style="width:40px; height:40px; border-radius: 50%;"/></a>
