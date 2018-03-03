# Bab 03: Kebahagiaan Pure dengan Fungsi Pure

## Oh untuk Menjadi Pure Lagi

Satu hal yang perlu kita luruskan adalah gagasan tentang fungsi pure.

>Fungsi pure adalah fungsi yang memberikan input yang sama, akan selalu menghasilkan output yang sama dan tidak memiliki efek samping yang dapat dilihat.

Gunakan `slice` dan `splice`. Mereka adalah dua fungsi yang melakukan hal yang sama - dengan cara yang sangat berbeda, ingat, meskipun begitu mereka tetap sama. Kita mengatakan `slice` dengan *pure* karena menghasilkan output per input yang sama setiap waktu, pasti. Bagaimanapun juga, `splice` akan mengambil arraynya dan mengeluarkannya kembali untuk perubahan selamanya yang merupakan efek yang dapat diamati.

```js
const xs = [1,2,3,4,5];

// pure
xs.slice(0,3); // [1,2,3]

xs.slice(0,3); // [1,2,3]

xs.slice(0,3); // [1,2,3]


// impure
xs.splice(0,3); // [1,2,3]

xs.splice(0,3); // [4,5]

xs.splice(0,3); // []
```

Dalam pemrograman fungsional, kita tidak menyukai fungsi yang sulit seperti `splice` yang me-*mutate* data. Ini tidak akan pernah dilakukan karena kita berusaha untuk mendapatkan fungsi yang dapat diandalkan yang mengembalikan hasil yang sama setiap saat, bukan fungsi yang meninggalkan kegagalan di akhir seperti `splice`.

Mari kita lihat contoh lain.

```js
// impure
const minimum = 21;
const checkAge = age => age >= minimum;

// pure
const checkAge = (age) => {
  const minimum = 21;
  return age >= minimum;
};
```

Pada bagian _impure_, `checkAge` bergantung pada variabel yang dapat berubah nilai `minimum` untuk menentukan hasilnya. Dengan kata lain, itu tergantung pada sistem _state_ yang menyebalkan karena meningkatkan [load kognitif](http://en.wikipedia.org/wiki/Cognitive_load) dengan mengenalkan lingkungan eksternal.

Ini mungkin tidak seperti contoh kebanyakan, tetapi ketergantungan pada _state_ adalah salah satu kontributor terbesar terhadap kompleksitas sistem(http://www.curtclifton.net/storage/papers/MoseleyMarks06a.pdf). `CheckAge` ini dapat mengembalikan hasil yang berbeda tergantung pada faktor eksternal untuk input, yang tidak hanya men-diskualifikasi-nya karena murni, namun juga menempatkan ide kita melalui ringer setiap kali kita mempertimbangkan perangkat lunak.

Bentuk _pure_ ini, di sisi lain, benar-benar mandiri. Kita juga bisa membuat nilai `minimum` menjadi tidak berubah, yang menjaga kemurniannya karena _state_ tidak akan pernah berubah. Untuk melakukan ini, kita harus membuat objek menjadi _freeze_.

```js
const immutableState = Object.freeze({ minimum: 21 });
```

## Efek Samping Mungkin Menyertakan...

Mari kita lihat lebih banyak tentang "efek samping" ini untuk meningkatkan intuisi kita. Jadi, apa sebenarnya *efek samping* yang tidak pasti ini yang disebutkan dalam definisi *fungsi pure*? Kita akan mengacu pada *efek* apapun yang terjadi pada perhitungan kita selain dari hasil perhitungannya.

Tidak ada intrinsik yang buruk tentang efek dan kami akan menggunakannya di semua tempat di bab-bab yang akan datang. Bagian *sisi* itulah yang mengandung konotasi negatif. Air yang tenang bukanlah penetas larva yang melekat, ini adalah bagian *stagnan* yang menghasilkan kawanan, dan saya yakinkan efek sampingnya adalah tempat berkembang biak yang sama dalam program Anda sendiri.

>*Efek samping* adalah perubahan pada sistem _state_ atau *interaksi yang dapat diamati* dengan dunia luar yang terjadi selama hasil penghitungan.

Efek samping mungkin menyertakan, namun tidak terbatas pada

  * mengubah file sistem
  * memasukan rekaman ke database
  * membuat panggilan http
  * mutasi
  * mencetak ke layar / _logging_
  * mendapatkan input pengguna
  * meng-_query_ DOM
  * mengakses sistem _state_

Dan daftarnya akan terus berkelanjutan. Setiap interaksi di luar fungsi adalah efek samping, yang merupakan fakta yang mungkin mendorong Anda untuk mencurigai pemrograman praktis Anda tanpa mereka. Filosofi pemrograman fungsional mengemukakan bahwa efek samping adalah penyebab utama perilaku yang salah.

Bukannya kita dilarang menggunakannya, akan tetapi kita ingin menahan dan menjalankannya dengan cara yang terkontrol. Kita akan belajar bagaimana melakukan ini ketika kita sampai di _functors_ dan _monads_ di bab-bab selanjutnya, tapi untuk saat ini, mari kita coba untuk menjaga agar fungsi tersembunyi ini terpisah dari kebiasaan kita.

Efek samping men-diskualifikasi-kan fungsi dari dan menjadi *pure*. Dan itu masuk akal: fungsi _pure_, menurut definisi, harus selalu mengembalikan output yang sama dengan masukan yang sama, yang tidak mungkin menjamin saat menangani masalah di luar fungsi lokal kita.

Mari kita lihat lebih dekat kenapa kita memaksakan output per input yang sama. Singsingkan lengan baju Anda, kita akan melihat beberapa matematika kelas 8.

## Matematika Kelas 8

Berdasarkan mathisfun.com:

> Fungsi adalah hubungan khusus antara nilai:
> Masing-masing nilai inputnya memberikan satu nilai output kembali.

Dengan kata lain, itu hanya hubungan antara dua nilai: input dan output. Meskipun setiap input memiliki satu output, output itu tidak harus unik per inputnya. Di bawah ini menunjukkan diagram fungsi yang benar-benar valid dari `x` ke `y`;

<img src="images/function-sets.gif" alt="set fungsi" />(http://www.mathsisfun.com/sets/function.html)

Sebaliknya, diagram berikut menunjukkan relasi yang *bukan* fungsi karena nilai input `5` adalah poin ke beberapa output:

<img src="images/relation-not-function.gif" alt="relasi bukan fungsi" />(http://www.mathsisfun.com/sets/function.html)

Fungsi dapat digambarkan sebagai satu pasangan dengan posisi (input, output): `[(1,2), (3,6), (5,10)]` (Terlihat fungsi ini menggandakan inputnya).

Atau mungkin tabel:
<table> <tr> <th>Input</th> <th>Output</th> </tr> <tr> <td>1</td> <td>2</td> </tr> <tr> <td>2</td> <td>4</td> </tr> <tr> <td>3</td> <td>6</td> </tr> </table>

Atau bahkan sebagai grafik dengan `x` sebagai input dan `y` sebagai outputnya:

<img src="images/fn_graph.png" width="300" height="300" alt="grafik fungsi" />


Tidak perlu rincian implementasi jika input menentukan output. Karena fungsi hanyalah pemetaan input ke output, seseorang dapat dengan mudah menuliskan literal objek dan menjalankannya dengan `[]` daripada `()`.

```js
const toLowerCase = {
  A: 'a',
  B: 'b',
  C: 'c',
  D: 'd',
  E: 'e',
  F: 'f',
};
toLowerCase['C']; // 'c'

const isPrime = {
  1: false,
  2: true,
  3: true,
  4: false,
  5: true,
  6: false,
};
isPrime[3]; // true
```

Tentu saja, Anda mungkin ingin menghitung bukan dengan dengan coretan hal tersebut, tapi ini menggambarkan cara berpikir yang berbeda mengenai fungsi. (Anda mungkin berpikir "bagaimana dengan fungsi dengan argumen yang banyak?" Memang, itu menyajikan sedikit ketidaknyamanan saat berpikir tentang aturan matematika. Untuk saat ini, kita dapat menggabungkannya dalam array atau hanya memikirkan `argumen` objek sebagai input. Ketika kita belajar tentang *currying*, kita akan melihat bagaimana kita bisa secara langsung meniru definisi matematis sebuah fungsi.)

Begini penjelasan dramatisnya: Fungsi murni *adalah* fungsi matematis dan itu adalah pemrograman fungsional. Pemrograman dengan malaikat kecil ini bisa memberi manfaat yang besar. Mari kita lihat beberapa alasan mengapa kita rela berusaha keras untuk menjaga kemurnian.

## Kasus untuk Kemurnian

### Dapat di-chace

Sebagai permulaan, fungsi murni selalu bisa di-cache dengan input. Hal ini biasanya dilakukan dengan menggunakan teknik yang disebut dengan memoization:

```js
const squareNumber = memoize(x => x * x);

squareNumber(4); // 16

squareNumber(4); // 16, returns cache for input 4

squareNumber(5); // 25

squareNumber(5); // 25, returns cache for input 5
```

Berikut adalah implementasi yang disederhanakan, meskipun ada banyak versi yang lebih kuat yang tersedia.

```js
const memoize = (f) => {
  const cache = {};

  return (...args) => {
    const argStr = JSON.stringify(args);
    cache[argStr] = cache[argStr] || f(...args);
    return cache[argStr];
  };
};
```

Yang perlu diperhatikan disini adalah Anda dapat mengubah beberapa fungsi _impure_ menjadi _pure_ dengan menunda evaluasi:

```js
const pureHttpCall = memoize((url, params) => () => $.getJSON(url, params));
```

Hal yang menarik di sini adalah kita sebenarnya tidak membuat panggilan http - kita malah mengembalikan fungsi yang akan melakukannya ketika dipanggil. Fungsi ini _pure_ karena akan selalu mengembalikan output yang sama dengan input yang sama: fungsi yang akan membuat panggilan http tertentu diberi `url` dan `params`.

Fungsi `memoize` kita bekerja dengan baik, meskipun tidak menyimpan hasil dari panggilan http, namun cache berfungsi sebagai fungsi yang dihasilkan.

Ini belum terlalu berguna, tetapi kita akan segera mempelajari beberapa trik yang akan membuatnya berguna. Kesimpulannya adalah kita bisa menyimpan setiap fungsi tanpa peduli seberapa destruktif tampilannya.

### Portable / Mendokumentasikan-sendiri

Fungsi _pure_ benar-benar mandiri. Semua kebutuhan fungsi diserahkan ke silver platter. Renungkan ini sejenak... Bagaimana ini bisa bermanfaat? Sebagai permulaan, dependensi fungsi bersifat eksplisit oleh karena itu lebih mudah dilihat dan dipahami - tidak ada bisnis lucu yang terjadi di bawah tenda.

```js
// impure
const signUp = (attrs) => {
  const user = saveUser(attrs);
  welcomeUser(user);
};

// pure
const signUp = (Db, Email, attrs) => () => {
  const user = saveUser(Db, attrs);
  welcomeUser(Email, user);
};
```

Contoh di sini menunjukkan bahwa fungsi _pure_ harus jujur tentang ketergantungannya dan, dengan demikian, beritahu kami apa masalahnya. Hanya dari tanda tangannya, kita tahu bahwa itu akan menggunakan `Db`, `Email`, dan `attrs` yang seharusnya menceritakan sedikit saja.

Kita akan belajar bagaimana membuat fungsi _pure_ seperti ini tanpa menunda evaluasi, namun intinya harus jelas bahwa bentuk _pure_ jauh lebih informatif daripada rekan liciknya _impure_ yang mana terserah siapa yang mengetahuiya.

Hal lain yang perlu diperhatikan adalah bahwa kita dipaksa untuk "menyuntikkan" dependensi, atau menyebarkannya sebagai argumen, yang membuat aplikasi kita lebih fleksibel karena kita telah mem-parameterkan database atau klien email kita atau apa saja yang Anda miliki (jangan khawatir, kita akan mencari cara untuk membuatnya menjadi menarik untuk didengar). Jika kita memilih untuk menggunakan Db yang berbeda kita hanya perlu memanggil fungsi kita dengannya. Jika kita menemukan diri kita sedang menulis aplikasi baru di mana kita ingin menggunakan kembali fungsi yang andal ini, kita cukup memberikan fungsi ini `Db` dan` Email` apa pun yang kita miliki saat itu.

Dalam pengaturan JavaScript, portabilitas dapat berarti fungsi serialisasi dan pengiriman melalui soket. Ini bisa berarti menjalankan semua kode aplikasi kami di pekerja web. Portabilitas adalah sifat yang sangat kuat.

Bertentangan dengan metode dan prosedur "tipikal" dalam pemrograman imperatif yang berakar jauh di lingkungan mereka melalui _state_, ketergantungan, dan efek yang tersedia, fungsi murni dapat dijalankan sesuka hati kita.

Kapan terakhir kali Anda menyalin sebuah metode ke aplikasi baru? Salah satu kutipan favorit saya berasal dari pencipta Erlang, Joe Armstrong: "Masalah dengan bahasa object-oriented adalah mereka memiliki semua lingkungan implisit yang mereka bawa bersama mereka. Anda menginginkan pisang tetapi yang Anda dapatkan adalah seekor gorila yang memegangi pisang ... dan seluruh hutan ".

### Dapat diuji

Selanjutnya, kita menyadari bahwa fungsi _pure_ membuat pengujian menjadi lebih mudah. Kita tidak harus memojokkan gateway pembayaran "nyata" atau meng-setup dan menegaskan keadaan dunia setelah masing-masing ujian. Kami hanya memberikan input fungsi dan menegaskan output.

Sebenarnya, kita menemukan komunitas fungsional yang merintis alat uji baru yang dapat meledakkan fungsi kita dengan input yang dihasilkan dan menegaskan bahwa sifat bergantung pada output. Ini diluar cakupan buku ini, tetapi saya sangat menganjurkan Anda untuk mencari dan mencoba *Quickcheck* - alat uji yang disesuaikan untuk lingkungan fungsional semata.

### Masuk akal

Banyak yang percaya kemenangan terbesar saat bekerja dengan fungsi _pure_ adalah *transparansi referensial*. Tempat kode transparansi referensial dapat diganti dengan nilai evaluasinya tanpa mengubah tingkah laku program.

Karena fungsi _pure_ selalu mengembalikan output yang sama dengan input yang sama, kita dapat mengandalkannya untuk selalu mengembalikan hasil yang sama dan dengan menjaga transparansi referensial. Mari kita lihat sebuah contoh.

```js
const { Map } = require('immutable');

// Aliases: p = player, a = attacker, t = target
const jobe = Map({ name: 'Jobe', hp: 20, team: 'red' });
const michael = Map({ name: 'Michael', hp: 20, team: 'green' });
const decrementHP = p => p.set('hp', p.get('hp') - 1);
const isSameTeam = (p1, p2) => p1.get('team') === p2.get('team');
const punch = (a, t) => (isSameTeam(a, t) ? t : decrementHP(t));

punch(jobe, michael); // Map({name:'Michael', hp:19, team: 'green'})
```

`decrementHP`, `isSameTeam` dan `punch` semuanya _pure_ dan maka dari itu mereka transparansi referensial. Kita bisa menggunakan teknik yang disebut *equational reasoning* dimana satu pengganti "equals untuk equals" untuk alasan tentang kode. Ini seperti mengevaluasi kode secara manual tanpa mempertimbangkan kebiasaan evaluasi program. Dengan menggunakan transparansi referensial, mari kita bermain dengan kode ini sedikit.

Pertama kita akan inline fungsi `isSameTeam`.

```js
const punch = (a, t) => (a.get('team') === t.get('team') ? t : decrementHP(t));
```

Karena data kita tidak berubah, kita bisa mengganti _team_ dengan nilai sebenarnya

```js
const punch = (a, t) => ('red' === 'green' ? t : decrementHP(t));
```

Kami melihat bahwa itu salah dalam kasus ini sehingga kami dapat menghapus seluruh _branch_

```js
const punch = (a, t) => decrementHP(t);
```

Dan jika kita inline `decrementHP`, kita melihat bahwa, dalam kasus ini, _punch_ menjadi panggilan untuk mengurangi `hp` dengan 1.

```js
const punch = (a, t) => t.set('hp', t.get('hp') - 1);
```

Kemampuan untuk berpikir tentang kode ini sangat hebat untuk kode refactoring dan understanding secara umum. Sebenarnya, kami menggunakan teknik ini untuk refactor program _flock of seagulls_ kami. Kami menggunakan penalaran equational untuk memanfaatkan sifat penambahan dan perkalian. Memang, kita akan menggunakan teknik-teknik ini di seluruh buku ini.

### Kode Paralel

Akhirnya, dan inilah _coup de grâce_, kita dapat menjalankan fungsi _pure_ secara paralel karena tidak memerlukan akses ke memori bersama dan tidak dapat, menurut definisi, memiliki kondisi balapan karena beberapa efek samping.

Ini sangat mungkin terjadi di sisi lingkungan server js dengan thread seperti di browser dengan pekerja web meskipun budaya saat ini sepertinya menghindarinya karena kompleksitas saat berhadapan dengan fungsi _impure_.

## Kesimpulan

Kami telah melihat apa itu fungsi _pure_ dan mengapa kita, sebagai programmer fungsional, percaya bahwa itu adalah pakaian malam kucing. Sejak saat ini, kami akan berusaha untuk menulis semua fungsi kami dengan cara yang _pure_. Kami memerlukan beberapa alat tambahan untuk membantu kami melakukannya, namun sementara itu, kami akan mencoba memisahkan fungsi _impure_ dari kode _pure_ lainnya.

Menulis program dengan fungsi _pure_ sangat sulit dilakukan tanpa beberapa alat tambahan di sabuk kita. Kita harus menyulap data dengan menyampaikan argumen di semua tempat, kita dilarang menggunakan _state_, belum lagi efeknya. Bagaimana seseorang bisa menulis program masokistik ini? Mari kita cari alat baru yang disebut _curry_.

[Bab 04: Currying](ch04.md)
