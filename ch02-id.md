# Bab 02: Fungsi Kelas Pertama

## Review Singkat
Ketika kita mengatakan fungsi "kelas satu", maksud kita sama dengan orang lain... jadi dengan kata lain kelas normal. Kita dapat memperlakukan fungsi seperti tipe data lainnya dan tidak ada yang spesial dari mereka - mereka mungkin disimpan dalam array, dilewatkan sebagai parameter fungsi, ditugaskan ke variabel, dan apa yang Anda miliki.

Itu adalah JavaScript 101, tetapi perlu disebutkan sejak pencarian kode cepat di github akan mengungkapkan penghindaran kolektif, atau mungkin ketidaktahuan konsep ini. Haruskah kita mencari contoh tiruan? Tentu.

```js
const hi = name => `Hi ${name}`;
const greeting = name => hi(name);
```

Di sini, pembungkus fungsi sekitar `hi` di dalam `greeting` benar-benar berlebihan. Mengapa? Karena fungsinya *callable* di JavaScript. Jika `hi` memiliki akhiran `()` akan berjalan dan mengembalikan nilainya. Jika tidak, itu hanya akan mengembalikan fungsi yang tersimpan dalam variabel. Hanya untuk memastikan, lihatlah:


```js
hi; // name => `Hi ${name}`
hi("jonas"); // "Hi jonas"
```

Karena `greeting` hanya saat gilirannya memanggil `hi` dengan argumen yang sama, kita bisa menulis:

```js
const greeting = hi;
greeting("times"); // "Hi times"
```

Dengan kata lain, `hi` sudah merupakan fungsi yang mengharapkan satu argumen, kenapa menempatkan fungsi lain di sekitarnya yang hanya memanggil` hi` dengan argumen yang sama? Itu tidak masuk akal. Ini seperti Anda mengenakan jaket parka terberat di bulan Juli hanya untuk menghembuskan udara dan menagih es lilin.

Hal ini sangat bertele-tele dan seperti yang terjadi, praktik buruk untuk mengelilingi fungsi dengan fungsi lainnya hanya untuk menunda evaluasi (kita akan melihatnya sebentar lagi, tapi ini berkaitan dengan maintenance)

Pemahaman yang solid tentang hal ini penting sebelum beralih nantinya, jadi mari kita periksa beberapa contoh menyenangkan yang diambil dari perpustakaan paket npm.

```js
// ignorant
const getServerStuff = callback => ajaxCall(json => callback(json));

// enlightened
const getServerStuff = ajaxCall;
```

Dunia dikotori persis dengan kode ajax seperti ini. Inilah alasan keduanya yang setara dengan:

```js
// this line
ajaxCall(json => callback(json));

// is the same as this line
ajaxCall(callback);

// so refactor getServerStuff
const getServerStuff = callback => ajaxCall(callback);

// ...which is equivalent to this
const getServerStuff = ajaxCall; // <-- look mum, no ()'s
```

Dan itulah folks, dan bagaimana menyelesaikannya. Sekali lagi agar kita mengerti mengapa saya begitu gigih.

```js
const BlogController = {
  index(posts) { return Views.index(posts); },
  show(post) { return Views.show(post); },
  create(attrs) { return Db.create(attrs); },
  update(post, attrs) { return Db.update(post, attrs); },
  destroy(post) { return Db.destroy(post); },
};
```

Pengendali yang tidak masuk akal ini adalah 99% fluff. Kita bisa menuliskannya kembali sebagai:

```js
const BlogController = {
  index: Views.index,
  show: Views.show,
  create: Db.create,
  update: Db.update,
  destroy: Db.destroy,
};
```

... atau bongkar semuanya karena tidak lebih dari sekadar menggabungkan _View_ dan _Db_ kita bersamaan.

## Mengapa Memilih Kelas Pertama?

Oke, mari terjun ke alasan untuk memilih fungsi kelas pertama. Seperti yang kita lihat di contoh `getServerStuff` dan `BlogController`, mudah untuk menambahkan lapisan tipuan yang tidak memberikan nilai tambahan dan hanya meningkatkan jumlah kode yang berlebihan untuk disimpan dan dicari.

Selain itu, fungsi pembungkus yang tidak perlu juga harus diubah, dan kita juga perlu mengubah fungsi pembungkus milik kita juga.

```js
httpGet('/post/2', json => renderPost(json));
```

Jika `httpGet` berubah ini memungkinan untuk mengirim `err`, kita perlu kembali dan mengganti "glue".


```js
// go back to every httpGet call in the application and explicitly pass err along.
httpGet('/post/2', (json, err) => renderPost(json, err));
```

Seandainya kita menulisnya sebagai fungsi kelas pertama, akan perlu banyak perubahan:

```js
// renderPost is called from within httpGet with however many arguments it wants
httpGet('/post/2', renderPost);
```

Selain menghilangkan fungsi yang tidak perlu, kita harus memberi nama dan argumen referensi. Nama adalah salah satu permasalahan, Anda akan melihatnya. Kami memiliki potensi yang tidak tepat - terutama karena usia dan persyaratan kode berubah.

Memiliki banyak nama untuk konsep yang sama adalah kebingungan yang umum dalam proyek ini. Ada juga masalah dalam kode generik. Misalnya, kedua fungsi ini melakukan hal yang sama persis, tetapi orang merasa jauh lebih umum dan dapat menggunakan kembali:

```js
// specific to our current blog
const validArticles = articles =>
  articles.filter(article => article !== null && article !== undefined),

// vastly more relevant for future projects
const compact = xs => xs.filter(x => x !== null && x !== undefined);
```

Dengan menggunakan penamaan khusus, tampaknya kami telah mengikatkan diri pada data spesifik (dalam hal ini `articles`). Hal ini kadang-kadang terjadi dan merupakan sumber dari banyak penemuan.

Saya harus menyebutkannya, seperti dengan kode Berorientasi Objek, Anda harus sadar bahwa `this` yang datang untuk menggigit Anda di jugular. Jika fungsi yang mendasari menggunakan `this` dan kita menyebutnya kelas pertama, kita tunduk pada abstraksi yang bocor ini.

```js
const fs = require('fs');

// scary
fs.readFile('freaky_friday.txt', Db.save);

// less so
fs.readFile('freaky_friday.txt', Db.save.bind(Db));
```

Setelah terikat pada dirinya sendiri, `Db` bebas untuk mengakses kode sampah prototipikalnya. Saya hindari menggunakan `this` karena seperti popok kotor. Sebenarnya tidak diperlukan saat menulis kode fungsional. Namun, ketika berinteraksi dengan perpustakaan lain, Anda mungkin harus menyetujui dunia yang gila di sekitar kita.

Beberapa orang akan berpendapat bahwa `this` diperlukan untuk mengoptimalkan kecepatan. Jika Anda adalah pengoptimalan mikro, silakan tutup buku ini. Jika Anda tidak bisa mendapatkan kembali uang Anda, mungkin Anda bisa menukarnya dengan sesuatu yang lebih menguntungkan.

Dengan itu, kami siap untuk melanjutkan.

[Bab 03: Kebahagiaan Murni dengan Fungsi Murni](ch03.md)
