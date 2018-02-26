# Bab 01: Apa Yang Kita Lakukan?

## Perkenalan

Hai! Saya Profesor Franklin Frisby. Senang berkenalan dengan Anda. Kita akan menghabiskan beberapa waktu bersama, karena saya harus mengajarkan Anda sedikit tentang pemrograman fungsional. Cukup sekian tentang saya, bagaimana dengan Anda? Saya berharap setidaknya Anda sedikit familiar dengan bahasa JavaScript, memiliki sedikit pengalaman tentang Object-Oriented, dan menyukai programmer kelas pekerja. Anda tidak perlu memiliki gelar PhD di bidang Entomologi, Anda hanya perlu tahu bagaimana menemukan dan menghilangkah beberapa bug.

Saya tidak berasumsi bahwa Anda memiliki pengetahuan pemrograman fungsional sebelumnya, karena kita berdua tahu apa yang terjadi saat Anda berasumsi. Akan tetapi, saya berharap Anda mengalami beberapa situasi yang tidak menguntungkan yang muncul saat bekerja dengan keadaan yang bisa berubah, efek samping yang tidak terbatas, dan desain yang tidak memiliki prinsip. Sekarang setelah kita berkenalan, mari kita lanjutkan.

Tujuan bab ini adalah untuk memberikan Anda nuansa tentang apa yang kita cari saat kita menulis program fungsional. Agar bisa memahami bab-bab berikutnya, kita harus memiliki beberapa gagasan tentang apa yang program *fungsional* buat. Jika tidak, kita akan merasa diri kita menulis tanpa tujuan, menghindari benda-benda yang semuanya dibiayai - usaha yang ceroboh memang. Kita membutuhkan sasaran yang jelas untuk melemparkan kode kita, beberapa kompas langit saat air menjadi kasar.

Sekarang, ada beberapa prinsip pemrograman umum - berbagai kredo akronim yang membimbing kita melewati terowongan gelap aplikasi: _DRY (don't repeat yourself)_ (jangan ulangi dirimu) , _YAGNI (ya ain't gonna need it)_ (ya tidak akan membutuhkannya), kopling yang longgar dengan kohesi tinggi, prinsipnya paling tidak mengejutkan, tanggung jawab tunggal, dan sebagainya.

Saya tidak akan menyuruh Anda dengan mencantumkan setiap pedoman yang pernah saya dengar sepanjang tahun... Intinya adalah mereka bertahan dalam keadaan fungsional, walaupun mereka bersikap tangensial terhadap tujuan akhir kita. Akan tetapi saya ingin Anda merasakannya sekarang, sebelum kita melangkah lebih jauh, adalah niat kita saat kita menekan dan memberikan dorongan pada keyboard; fungsional Xanadu kita.

<!--BREAK-->

## Pertemuan Singkat

Mari kita mulai dengan sentuhan yang sedikit gila. Berikut adalah aplikasi _seagull_ (camar). Ketika kawanan burung menyerang mereka dengan menjadi kawanan yang lebih besar, dan ketika mereka berkembang biak, mereka meningkat dengan berkembang biaknya jumlah burung camar. Nah, ini tidak dimaksudkan untuk menjadi kode Object-Oriented yang baik, ingatlah, inilah alasannya untuk menyoroti bahaya dari pendekatan berbasis tugas modern kita. Lihat:

```js
class Flock {
  constructor(n) {
    this.seagulls = n;
  }

  conjoin(other) {
    this.seagulls += other.seagulls;
    return this;
  }

  breed(other) {
    this.seagulls = this.seagulls * other.seagulls;
    return this;
  }
}

const flockA = new Flock(4);
const flockB = new Flock(2);
const flockC = new Flock(0);
const result = flockA
  .conjoin(flockC)
  .breed(flockB)
  .conjoin(flockA.breed(flockB))
  .seagulls;
// 32
```

Siapa di bumi ini yang akan membuat kekejian yang sangat mengerikan? Tidak masuk akal, sulit untuk melacak keadaan internal yang bermutasi. Dan, selamat, jawabannya bahkan tidak benar! Seharusnya `16`, tetapi `flock_a` berakhir secara permanen dalam proses perubahannya. `flock_a` yang malang. Ini adalah anarki pada I.T.! Ini adalah aritmatika binatang liar!

Jika Anda tidak mengerti program ini, tidak masalah, saya juga tidak. Intinya yang perlu diingat di sini adalah bahwa nilai state dan mutable sulit diikuti, bahkan dalam contoh kecil seperti itu.

Mari kita coba lagi, kali ini menggunakan pendekatan yang lebih fungsional:

```js
const conjoin = (flockX, flockY) => flockX + flockY;
const breed = (flockX, flockY) => flockX * flockY;

const flockA = 4;
const flockB = 2;
const flockC = 0;
const result =
    conjoin(breed(flockB, conjoin(flockA, flockC)), breed(flockA, flockB));
// 16
```

Nah, kali ini kita mendapat jawaban yang benar. Dengan kode yang jauh lebih sedikit. Fungsi nesting adalah anak yang membingungkan... (kami akan memperbaiki situasi ini di bab5). Ini lebih baik, tapi mari kita menggali sedikit lebih dalam. Ada manfaatnya juga memanggil spade spade. Seandainya kita teliti fungsi yang sudah kita buat lebih dekat, kita akan menemukan bahwa kita hanya perlu menambahkan (`conjoin`) dan perkalian (` breed`) yang sederhana.

Sama sekali tidak ada yang spesial dari dua fungsi ini selain nama mereka. Mari kita ubah nama fungsi yang sudah kita buat menjadi `multiply` dan` add` untuk mengungkapkan identitas aslinya.

```js
const add = (x, y) => x + y;
const multiply = (x, y) => x * y;

const flockA = 4;
const flockB = 2;
const flockC = 0;
const result =
    add(multiply(flockB, add(flockA, flockC)), multiply(flockA, flockB));
// 16
```
Dan dengan itu, kita mendapatkan pengetahuan tentang orang sebelumnya:

```js
// associative
add(add(x, y), z) === add(x, add(y, z));

// commutative
add(x, y) === add(y, x);

// identity
add(x, 0) === x;

// distributive
multiply(x, add(y,z)) === add(multiply(x, y), multiply(x, z));
```

Ah iya, sifat setia matematis tersebut seharusnya sangat berguna. Jangan khawatir jika Anda tidak mengenal mereka dalam kepala Anda. Kebanyakan dari kita, sudah lama belajar tentang hukum aritmatika ini. Mari kita lihat apakah kita bisa menggunakan properti ini untuk menyederhanakan program _little seagull_ kita.

```js
// Original line
add(multiply(flockB, add(flockA, flockC)), multiply(flockA, flockB));

// Apply the identity property to remove the extra add
// (add(flockA, flockC) == flockA)
add(multiply(flockB, flockA), multiply(flockA, flockB));

// Apply distributive property to achieve our result
multiply(flockB, add(flockA, flockA));
```

Cemerlang! Kita tidak perlu menulis sejumlah kode khusus selain fungsi pemanggilan. Kita menyertakan definisi `add` dan `multiply` sebagai pelengkap, tetapi sebenarnya tidak perlu menuliskannya - kita pasti mempunyai `add` dan `multiply` yang disediakan oleh beberapa perpustakaan yang ada.

Anda mungkin berpikir "betapa percaya dirinya Anda untuk memberikan contoh mati seperti itu di depan". Atau "program yang nyata tidak sesederhana ini dan tidak dapat dibayangkan begitu saja." Saya memilih contoh ini karena kebanyakan dari kita sudah tahu tentang penambahan dan perbanyakan, jadi mudah dilihat bagaimana matematika sangat berguna bagi kita disini.

Jangan putus asa - sepanjang buku ini, kita akan sebarkan beberapa teori kategori, teori menetapkan, dan kalkulus lambda dan menulis contoh dunia nyata yang mencapai kesederhanaan dan hasil elegan yang sama seperti contoh _flock_ dari _seagulls_. Anda juga tidak perlu menjadi matematikawan. Ini akan terasa alami dan mudah, sama seperti Anda menggunakan framework "normal" atau API.

Mungkin akan mengejutkan jika kita bisa menulis aplikasi sehari penuh sepanjang analog fungsional di atas. Program yang memiliki sifat suara. Program yang singkat, namun mudah dipikirkan. Program yang tidak menemukan roda di setiap belokan. Ketiadaan hukum itu baik jika Anda seorang kriminal, namun dalam buku ini, kita ingin mengakui dan mematuhi hukum matematika.

Kami ingin menggunakan teori di mana setiap bagian cenderung cocok bersama dengan baik. Kami ingin mewakili masalah spesifik kami dalam hal generik, mengkomposit bit kemudian mengeksploitasi properti mereka untuk keuntungan kita sendiri. Ini akan memerlukan sedikit disiplin daripada pendekatan "apapun itu" dari pemrograman imperatif (kita akan membahas definisi "imperatif" yang tepat di buku ini, tapi untuk saat ini anggap saja itu selain pemrograman fungsional). Dengan hasil kerja dengan berprinsip, framework matematis akan benar-benar mengejutkan Anda.

Kami telah melihat sekilas bintang utara fungsional kami, namun ada beberapa konsep konkret yang harus dipegang sebelum kami benar-benar memulai perjalanan kami.

[Bab 02: Fungsi Kelas Pertama](ch02.md)
