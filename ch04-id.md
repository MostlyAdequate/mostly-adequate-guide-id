# Chapter 04: Currying

## Tidak Bisa Hidup Jika Hidup Tanpamu
Ayah saya pernah menjelaskan bahwa bagaimana ada suatu hal bisa dilakukan seseorang tanpa mereka peroleh terlebih dahulu. Microwave adalah salah satunya. _Smart phone_, dan lainnya. Orang tua kita sudah mengingatkan bahwa kehidupan tanpa internet akan lebih baik. Bagi saya, _curying_ termasuk didalamnya.

Konsepnya sederhana: Anda bisa memanggil fungsi dengan argumen yang lebih sedikit dari yang diharapkan. Itu akan mengembalikan fungsi yang mengambil argumen yang tersisa.

Anda dapat memilih untuk memanggil semuanya sekaligus atau hanya memberikan feed di setiap argumen sedikit demi sedikit.

```js
const add = x => y => x + y;
const increment = add(1);
const addTen = add(10);

increment(2); // 3
addTen(2); // 12
```

Di sini kita telah membuat fungsi `add` yang membutuhkan satu argumen dan mengembalikan sebuah fungsi. Dengan memanggilnya, fungsi yang dikembalikan akan mengingat argumen pertama mulai saat itu melalui penutupan. Memanggilnya dengan dua argumen sekaligus akan sedikit menyedihkan, namun, kita bisa menggunakan fungsi pembantu khusus yang disebut `curry` untuk menentukan dan memanggil fungsi seperti ini dengan lebih mudah.

Mari kita atur beberapa fungsi _curried_. Mulai sekarang, kita akan memanggil `curry` kita
fungsi yang didefinisikan dalam [Lampiran A - Dukungan Fungsi Esensial](./appendix_a.md).

```js
const match = curry((what, s) => s.match(what));
const replace = curry((what, replacement, s) => s.replace(what, replacement));
const filter = curry((f, xs) => xs.filter(f));
const map = curry((f, xs) => xs.map(f));
```

Pola yang saya ikuti sangat sederhana, namun penting. Saya telah memposisikan secara strategis data yang kami operasikan pada (String, Array) sebagai argumen terakhir. Ini akan menjadi jelas saat digunakan.

(Sintaks `/r/g` adalah ekspresi reguler yang berarti _cocok dengan setiap huruf 'r'_. Baca [lebih banyak tentang ekspresi reguler](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) jika Anda suka.)

```js
match(/r/g, 'hello world'); // [ 'r' ]

const hasLetterR = match(/r/g); // x => x.match(/r/g)
hasLetterR('hello world'); // [ 'r' ]
hasLetterR('just j and s and t etc'); // null

filter(hasLetterR, ['rock and roll', 'smooth jazz']); // ['rock and roll']

const removeStringsWithoutRs = filter(hasLetterR); // xs => xs.filter(x => x.match(/r/g))
removeStringsWithoutRs(['rock and roll', 'smooth jazz', 'drum circle']); // ['rock and roll', 'drum circle']

const noVowels = replace(/[aeiou]/ig); // (r,x) => x.replace(/[aeiou]/ig, r)
const censored = noVowels('*'); // x => x.replace(/[aeiou]/ig, '*')
censored('Chocolate Rain'); // 'Ch*c*l*t* R**n'
```

Apa yang ditunjukkan di sini adalah kemampuan untuk "pre-load" fungsi dengan satu atau dua argumen untuk menerima fungsi baru yang mengingat argumen tersebut.

Saya menganjurkan Anda untuk meng-_clone_ repositori Mostly Adequate (`git clone
https://github.com/MostlyAdequate/mostly-adequate-guide.git`), salin kode di atas dan
lanjutkan di REPL. Fungsi curry (dan sebenarnya semuanya sudah didefinisikan dalam lampiran) telah
tersedia dari modul `exercises/support.js`. 

## Lebih dari Sekedar Permainan Kata-kata / Saus Spesial

Currying berguna untuk banyak hal. Kita bisa membuat fungsi baru hanya dengan memberikan fungsi dasar kita, beberapa argumen seperti yang terlihat pada `hasSpaces`,`findSpaces`, dan `censored`.

Kita juga memiliki kemampuan untuk mengubah fungsi yang bekerja pada elemen tunggal menjadi fungsi yang bekerja pada array hanya dengan membungkusnya dengan `map`:

```js
const getChildren = x => x.childNodes;
const allTheChildren = map(getChildren);
```

Memberikan lebih sedikit argumen fungsi dari yang diharapkan biasanya disebut dengan *aplikasi parsial*. Fungsi aplikasi parsial dapat menghilangkan banyak kode boiler plate. Pertimbangkan apakah fungsi `allTheChildren` di atas dengan `map` belum di-_uncurried_ dari lodash (perhatikan argumennya dalam urutan yang berbeda):

```js
const allTheChildren = elements => map(elements, getChildren);
```

Kami biasanya tidak mendefinisikan fungsi yang bekerja pada array, karena kita bisa memanggil `map (getChildren)` ke dalam baris. Sama dengan `sort`, `filter`, dan fungsi orde tinggi lainnya (Fungsi orde tinggi: Fungsi yang mengambil atau mengembalikan fungsi).

Ketika kita berbicara tentang *fungsi pure*, kami mengatakan bahwa mereka mengambil 1 input ke 1 output. Currying melakukan persis seperti ini: setiap argumen mengembalikan fungsi baru untuk mengharapkan argumen yang tersisa. Itu, adalah cara lama, 1 input ke 1 output.

Tidak masalah jika outputnya adalah fungsi lain - itu memenuhi syarat _pure_. Kami mengizinkan lebih dari satu argumen pada satu waktu, tapi ini dilihat sebagai hanya dengan menghapus `()` tambahan agar lebih baik.

## Kesimpulan

Currying sangat berguna dan saya sangat senang bekerja dengan fungsi carry setiap hari. Ini adalah alat sebagai sabuk yang membuat pemrograman fungsional kurang verbose dan membosankan.

Kita dapat membuat fungsi baru yang berguna dengan cepat hanya dengan menyampaikan beberapa argumen dan sebagai bonus, kita telah mempertahankan definisi fungsi matematika meskipun dengan argumen yang banyak.

Mari kita pelajari alat penting lain yang disebut `compose`.

[Bab 05: Pengodean dengan Composing](ch05.md)

## Latihan

#### Catatan Latihan

Sepanjang buku ini, Anda mungkin menemukan bagian 'Latihan' seperti ini. Latihan bisa jadi
dilakukan langsung di-browser asalkan Anda membaca dari [gitbook](https://mostly-adequate.gitbooks.io/mostly-adequate-guide) (disarankan).

Perhatikan bahwa, untuk semua latihan dalam buku ini, Anda selalu memiliki beberapa fungsi pembantu
yang tersedia dalam lingkup global. Oleh karena itu, apa pun yang didefinisikan dalam [Lampiran A](./appendix_a.md),
[Lampiran B](./appendix_b.md) dan [Lampiran C] (./appendix_c.md) tersedia untuk Anda! Dan jika
itu tidak cukup, beberapa latihan juga akan menentukan fungsi yang spesifik untuk masalah
yang mereka hadapi; Faktanya, anggap mereka tersedia juga.

> Petunjuk: Anda dapat mengirimkan solusi Anda dengan melakukan `Ctrl + Enter` di editor yang disematkan!

#### Menjalankan Latihan di Mesin Anda (opsional)

Jika Anda lebih suka melakukan latihan secara langsung dalam file menggunakan editor Anda sendiri:

- clone repositori (`git clone git@github.com/MostlyAdequate/mostly-adequate-guide.git`)
- masuk ke bagian *latihan* (`cd mostly-adequate-guide/exercises`)
- pasang plumbering yang diperlukan menggunakan [npm](https://docs.npmjs.com/getting-started/installing-node) (`npm install`)
- Selesaikan jawaban dengan memodifikasi file yang bernama *latihan\_\** pada folder bab yang sesuai
- jalankan koreksi dengan npm (misalnya `npm run ch04`)

Unit tes akan berjalan pada jawaban Anda dan akan memberikan petunjuk jika terjadi kesalahan. Ngomong-ngomong,
Jawaban untuk latihan tersedia dalam file bernama *jawaban\_\**.

#### Ayo Berlatih!

{% exercise %}  
Refactor untuk menghapus semua argumen dengan menerapkan sebagian fungsi.
  
{% initial src="./exercises/ch04/exercise_a.js#L3;" %}  
```js  
const words = str => split(' ', str);  
```  
  
{% solution src="./exercises/ch04/solution_a.js" %}  
{% validation src="./exercises/ch04/validation_a.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}  


---


{% exercise %}  
Refactor untuk menghapus semua argumen dengan menerapkan sebagian fungsi.
  
{% initial src="./exercises/ch04/exercise_b.js#L3;" %}  
```js  
const filterQs = xs => filter(x => match(/q/i, x), xs);
```  
  
{% solution src="./exercises/ch04/solution_b.js" %}  
{% validation src="./exercises/ch04/validation_b.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}  


---


Menimbang fungsi berikut:

```js  
const keepHighest = (x, y) => (x >= y ? x : y);  
```  

{% exercise %}  
Refactor `max` to not reference any arguments using the helper function `keepHighest`.  
Refactor `max` untuk tidak mereferensi argumen apapun dengan menggunakan fungsi pembantu `keepHighest`.
  
{% initial src="./exercises/ch04/exercise_c.js#L7;" %}  
```js  
const max = xs => reduce((acc, x) => (x >= acc ? x : acc), -Infinity, xs);  
```  
  
{% solution src="./exercises/ch04/solution_c.js" %}  
{% validation src="./exercises/ch04/validation_c.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}  
