## FAQ

- [Kenapa cuplikan terkadang ditulis dengan titik koma dan terkadang tidak?](#kenapa-cuplikan-terkadang-ditulis-dengan-titik-koma-dan-terkadang-tidak)
- [Bukankah perpustakaan eksternal seperti _ (ramda) atau $ (jquery) membuat panggilan impure?](#bukankah-perpustakaan-eksternal-seperti-_-ramda-atau--jquery-membuat-panggilan-impure)
- [Apa maksud `f a` dalam signature?](#apa-maksud-f-a-dalam-signature)
- [Apakah contoh "real world" tersedia?](#apakah-contoh-real-world-tersedia)
- [Kenapa buku ini menggunakan ES5? Apakah versi ES6 tersedia?](#kenapa-buku-ini-menggunakan-es5-apakah-versi-es6-tersedia)
- [Apa fungsinya mengurangi fungsi?](#apa-fungsinya-mengurangi-fungsi)
- [Tidakkah Anda menggunakan bahasa Inggris yang disederhanakan daripada gaya saat ini?](#tidakkah-anda-menggunakan-bahasa-inggris-yang-disederhanakan-daripada-gaya-saat-ini)
- [Apa itu Either? Apa itu Future? Apa itu Task?](#apa-itu-either-apa-itu-future-apa-itu-task)
- [Dimana peta, filter, compose ... metode berasal?](#dimana-peta-filter-compose--metode-berasal)

### Kenapa cuplikan terkadang ditulis dengan titik koma dan terkadang tidak?

> Lihat [#6]

Ada dua aliran di JavaScript, orang yang menggunakannya, dan orang yang tidak (menggunakannya). Kami telah membuat
pilihan di sini untuk menggunakannya, dan sekarang, kami berusaha untuk konsisten dengan pilihan tersebut. Jika ada beberapa yang
hilang, beri tahu kami dan kami akan mengurus kesalahannya.

### Bukankah perpustakaan eksternal seperti _ (ramda) atau $ (jquery) membuat panggilan impure?

> lihat [#50]

Ketergantungan itu tersedia jika berada dalam konteks global, bagian dari bahasa tersebut.
Jadi, tidak, panggilan masih bisa dianggap _pure_.
Untuk bacaan lebih lanjut, lihatlah [artikel ini tentang
CoEffects](http://tomasp.net/blog/2014/why-coeffects-matter/)

### Apa maksud `f a` dalam signature?

> lihat [#62]

Dalam signature, seperti:

`map :: Functor f => (a -> b) -> f a -> f b`

`f` mengacu pada` functor` yang bisa, misalnya, Maybe atau IO. Dengan demikian, signature abstrak
merupakan pilihan terhadap functor itu dengan menggunakan variabel tipe yang pada dasarnya berarti bahwa setiap functor
mungkin digunakan di mana `f` berdiri selama semua `f` berada pada tipe yang sama (jika `f a` yang pertama dalam
signature mewakili `Maybe a`, maka yang kedua **tidak bisa merujuk ke** `IO b` tetapi
harus menjadi `Maybe b`. Contohnya:

```javascript
let maybeString = Maybe.of("Patate")
let f = function (x) { return x.length }
let maybeNumber = map(f, maybeString) // Maybe(6)

// With the following 'refined' signature:
// map :: (string -> number) -> Maybe string -> Maybe number
```

### Apakah contoh "real world" tersedia?

> lihat [#77], [#192]

Jika Anda belum mencapainya, Anda mungkin bisa melihat [Bab
6](https://github.com/MostlyAdequate/mostly-adequate-guide/blob/master/ch6.md) yang menyajikan
aplikasi flickr sederhana
Contoh lain mungkin akan menyusul. Omong-omong, jangan ragu untuk berbagi pengalaman
dengan kami!

### Kenapa buku ini menggunakan ES5? Apakah versi ES6 tersedia?

> lihat [#83], [#235]

Buku ini bertujuan untuk diakses secara luas. Ini sudah dimulai sebelum ES6 keluar, dan sekarang, karena
standar baru semakin diterima, kami mempertimbangkan untuk membuat dua edisi terpisah
ES5 dan ES6. Anggota komunitas yang sudah mengerjakan versi ES6 (lihat
[#235] untuk informasi lebih lanjut).

### Apa fungsinya mengurangi fungsi?

> lihat [#109]

Mengurangi, menumpuk, melipat, menyuntikkan semua fungsi biasa dalam pemrograman digunakan
untuk menggabungkan elemen struktur data secara berurutan. Anda mungkin bisa melihat [pembicaraan
ini](https://www.youtube.com/watch?v=JZSoPZUoR58&ab_channel=NewCircleTraining) untuk mendapatkan beberapa
wawasan lebih tentang fungsi pengurangan.

### Tidakkah Anda menggunakan bahasa Inggris yang disederhanakan daripada gaya saat ini?

> lihat [#176]

Buku ini ditulis dengan gaya sendiri yang memberikan kontribusi untuk membuatnya konsisten secara keseluruhan. Jika
Anda tidak terbiasa dengan bahasa Inggris, berlatihlah dengan bagus. Meskipun begitu, terkadang sebaiknya Anda butuh bantuan
Untuk memahami maknanya, ada [beberapa
terjemahan](https://github.com/MostlyAdequate/mostly-adequate-guide/blob/master/TRANSLATIONS.md)
yang tersedia yang mungkin bisa membantu Anda.

### Apa itu Either? Apa itu Future? Apa itu Task?

> lihat [#194]

Kami memperkenalkan semua struktur ini di seluruh buku. Oleh karena itu, Anda tidak akan menemukan penggunaan
struktur yang sebelumnya tidak dijelaskan. Jangan ragu untuk membaca lagi beberapa bagian lama jika
Anda merasa tidak nyaman dengan tipe tersebut.
Glossary/vade akan datang terakhir untuk mensintesis semua gagasan ini.

### Dimana peta, filter, compose ... metode berasal?

> lihat [#198]

Sebagian besar waktu, metode tersebut didefinisikan di perpustakaan vendor tertentu seperti `ramda` atau
`underscore` Anda juga harus melihat
[support.js](https://github.com/MostlyAdequate/mostly-adequate-guide/blob/master/code%2Fpart1_exercises%2Fsupport.js)
di mana kita mendefinisikan beberapa implementasi yang digunakan untuk latihan. Fungsi itu benar-benar
umum dalam pemrograman fungsional dan meskipun implementasi mereka mungkin sedikit berbeda, makna
mereka tetap cukup konsisten dengan perpustakaan.


[#6]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/6
[#50]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/50
[#62]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/62
[#77]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/77
[#83]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/83
[#109]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/109
[#176]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/176
[#192]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/192
[#194]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/194
[#198]: https://github.com/MostlyAdequate/mostly-adequate-guide/issues/198
[#235]: https://github.com/MostlyAdequate/mostly-adequate-guide/pull/235
