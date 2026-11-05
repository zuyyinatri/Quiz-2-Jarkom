# Kuis 2 Jaringan Komputer — Bank Soal, Jawaban, dan Penjelasan

Total: **60 soal unik** (soal yang muncul berulang di beberapa percobaan hanya ditulis sekali).
Soal dikelompokkan per topik. Jawaban benar ditandai dengan ✅.

> Catatan: pada tangkapan layar percobaan yang belum dinilai tidak ada kunci jawaban, jadi jawaban dihitung/diverifikasi dari materi. Soal yang dinilai (Correct/Incorrect) dicocokkan dengan hasil penilaian tersebut.

**Daftar topik**

1. [Layer Transport & Socket](#1-layer-transport--socket)
2. [Reliable Data Transfer (rdt, GBN, SR)](#2-reliable-data-transfer-rdt-gbn-sr)
3. [TCP: Segmen, Koneksi, dan RTT](#3-tcp-segmen-koneksi-dan-rtt)
4. [Congestion Control](#4-congestion-control)
5. [Web, HTTP, Cookie, dan Web Cache](#5-web-http-cookie-dan-web-cache)
6. [FTP dan E-mail](#6-ftp-dan-e-mail)
7. [DNS](#7-dns)
8. [P2P, BitTorrent, dan DHT](#8-p2p-bittorrent-dan-dht)

---

## 1. Layer Transport & Socket

### Soal 1
Di sisi pengirim, lapisan transport memperlakukan pesan dari aplikasi dengan cara

- a. Menentukan rute yang akan dilalui setiap pesan
- b. Memecahnya menjadi segmen-segmen lalu meneruskannya ke lapisan jaringan ✅
- c. Mengenkripsinya lalu mengirimkannya langsung ke lapisan link
- d. Menggabungkannya dengan pesan aplikasi lain menjadi satu datagram

**Jawaban:** b

**Penjelasan:** Sisi pengirim memecah pesan aplikasi menjadi segmen, menambahkan header transport, lalu menyerahkannya ke lapisan network. Penentuan rute adalah tugas lapisan network, bukan transport. Enkripsi bukan fungsi dasar transport, dan pesan tidak digabung menjadi satu datagram.

---

### Soal 2
Socket TCP diidentifikasi oleh

- a. Satu nilai yaitu port tujuan
- b. Tiga nilai yaitu IP sumber, IP tujuan, dan port tujuan
- c. Empat nilai yaitu IP sumber, port sumber, IP tujuan, dan port tujuan ✅
- d. Dua nilai yaitu IP tujuan dan port tujuan

**Jawaban:** c

**Penjelasan:** TCP melakukan demultiplexing berorientasi koneksi, sehingga socket dibedakan oleh 4-tuple (IP sumber, port sumber, IP tujuan, port tujuan). Karena itu dua klien dengan port tujuan sama tetap diarahkan ke socket yang berbeda. Berbeda dengan UDP yang hanya memakai IP tujuan dan port tujuan.

---

### Soal 3
Pada server TCP Python, fungsi `accept()` mengembalikan

- a. Alamat IP server dan nomor port server
- b. Socket baru yang khusus dipakai berkomunikasi dengan satu klien tertentu ✅
- c. Data pertama yang dikirim oleh klien
- d. Socket penyambut (welcoming socket) yang sama untuk semua klien

**Jawaban:** b

**Penjelasan:** Welcoming socket hanya menunggu permintaan koneksi. Setiap kali ada klien yang terhubung, `accept()` membuat connection socket baru yang khusus untuk klien itu, sedangkan welcoming socket tetap terbuka untuk klien berikutnya.

---

### Soal 4
Pada pemrograman socket Python, tipe socket yang dipakai untuk layanan UDP adalah

- a. SOCK_RAW
- b. SOCK_STREAM
- c. SOCK_DGRAM ✅
- d. SOCK_SEQPACKET

**Jawaban:** c

**Penjelasan:** `SOCK_DGRAM` adalah socket datagram untuk UDP, sedangkan `SOCK_STREAM` dipakai untuk TCP (aliran byte andal).

---

### Soal 5
Alasan aplikasi streaming multimedia sering memilih UDP dibanding TCP adalah

- a. UDP menyediakan koneksi yang lebih andal
- b. UDP memiliki congestion control yang lebih baik
- c. Aplikasi tersebut toleran terhadap loss tetapi sensitif terhadap laju pengiriman ✅
- d. UDP menjamin pengiriman tanpa loss

**Jawaban:** c

**Penjelasan:** Streaming bisa mentoleransi sedikit paket hilang, tetapi sangat sensitif terhadap delay dan laju. TCP memperlambat pengiriman saat congestion dan melakukan retransmisi yang menambah delay. UDP tidak andal dan tidak punya congestion control, sehingga opsi a, b, dan d salah.

---

### Soal 6
Sebuah link berkapasitas R sudah dipakai oleh 9 koneksi TCP. Aplikasi baru membuka 11 koneksi TCP paralel. Laju agregat yang diperoleh aplikasi baru tersebut kira-kira

- a. R/10
- b. R/2 ✅
- c. R/11
- d. R/20

**Jawaban:** b

**Penjelasan:** Setelah aplikasi baru membuka 11 koneksi, total ada 9 + 11 = 20 koneksi yang berbagi link secara adil, masing-masing R/20. Aplikasi baru memegang 11 koneksi sehingga mendapat 11 × R/20 = 11R/20 ≈ R/2. Ini menunjukkan celah fairness TCP: aplikasi yang membuka banyak koneksi paralel mendapat porsi bandwidth lebih besar.

---

## 2. Reliable Data Transfer (rdt, GBN, SR)

### Soal 7
Protokol rdt1.0 mengasumsikan kanal yang mendasarinya

- a. Dapat menghilangkan paket tetapi tidak membalik bit
- b. Dapat membalik bit tetapi tidak menghilangkan paket
- c. Tidak pernah membalik bit maupun menghilangkan paket ✅
- d. Dapat membalik bit dan menghilangkan paket

**Jawaban:** c

**Penjelasan:** rdt1.0 adalah kasus paling sederhana: kanal sepenuhnya andal, tanpa bit error dan tanpa loss, sehingga sender dan receiver hanya perlu satu state dan tidak ada umpan balik.

---

### Soal 8
Cacat fatal pada rdt2.0 muncul ketika

- a. Sender mengirim paket terlalu cepat
- b. Receiver mengirim NAK
- c. Pesan ACK atau NAK yang diterima sender rusak ✅
- d. Paket data mengalami bit error

**Jawaban:** c

**Penjelasan:** rdt2.0 sudah menangani bit error pada data (lewat checksum, ACK/NAK, dan retransmisi). Masalahnya muncul bila ACK/NAK itu sendiri rusak: sender tidak tahu apakah receiver menerima paket dengan benar, dan jika sender mengirim ulang begitu saja, receiver bisa menerima duplikat. Solusinya adalah nomor urut pada rdt2.1.

---

### Soal 9
Sender rdt2.1 memiliki dua kali lebih banyak state dibanding rdt2.0 karena

- a. Harus menjalankan dua timer sekaligus
- b. Harus memeriksa checksum data dan checksum ACK secara terpisah
- c. Harus mengingat apakah paket yang sedang menunggu ACK bernomor urut 0 atau 1 ✅
- d. Harus menyimpan salinan dua paket terakhir yang dikirim

**Jawaban:** c

**Penjelasan:** rdt2.1 menambahkan nomor urut 0/1 pada paket. Sender harus mengingat nomor urut paket yang sedang dikirim, sehingga setiap state "menunggu data dari atas" dan "menunggu ACK/NAK" muncul dalam dua versi (seq 0 dan seq 1) dan jumlah state menjadi dua kali lipat. Belum ada timer sampai rdt3.0.

---

### Soal 10
Pada rdt2.2 yang bebas NAK, bagaimana receiver menyatakan bahwa paket yang diterimanya rusak?

- a. Mengirim paket kosong tanpa checksum
- b. Tidak mengirim apa pun sampai timeout
- c. Mengirim NAK yang berisi nomor urut paket yang rusak
- d. Mengirim ACK untuk paket terakhir yang diterima dengan benar beserta nomor urutnya ✅

**Jawaban:** d

**Penjelasan:** rdt2.2 menghilangkan NAK. Receiver mengirim ACK untuk paket terakhir yang benar dengan nomor urutnya. Jika sender menerima ACK duplikat (nomor urut yang sama dua kali), ia tahu paket berikutnya tidak diterima dengan benar dan mengirim ulang. Ini mirip ide duplicate ACK pada TCP.

---

### Soal 11
Pada rdt3.0, apa yang terjadi jika timeout terlalu cepat (premature timeout) padahal ACK hanya terlambat?

- a. Receiver mengirim NAK agar sender berhenti
- b. Sender mengirim ulang paket yang sama sehingga receiver menerima duplikat dan membuangnya berdasarkan nomor urut ✅
- c. Koneksi diputus oleh sender
- d. Data yang diterima menjadi salah urut dan dikirim ke aplikasi

**Jawaban:** b

**Penjelasan:** Timeout prematur menyebabkan retransmisi yang sebenarnya tidak perlu. Receiver mendeteksi paket duplikat lewat nomor urut 0/1, membuangnya, dan tetap mengirim ACK. Protokol tetap benar, hanya efisiensinya turun.

---

### Soal 12
Dua kata 16 bit `1000000000000001` dan `1000000000000010` dijumlahkan dengan aturan one's complement (carry out ditambahkan kembali ke hasil). Nilai checksum yang dikirim adalah

- a. 0000000000000011
- b. 0000000000000100
- c. 1111111111111011 ✅
- d. 1111111111111100

**Jawaban:** c

**Penjelasan:**
1. Jumlahkan: `1000000000000001 + 1000000000000010 = 1 0000000000000011` (ada carry out 1).
2. Tambahkan carry ke hasil (wraparound): `0000000000000011 + 1 = 0000000000000100`.
3. Checksum adalah one's complement dari hasil itu (bit dibalik): `1111111111111011`.

Opsi b adalah jumlah setelah wraparound, tetapi belum di-complement.

---

### Soal 13
Link 1 Gbps, delay propagasi 15 ms, paket 8.000 bit. Dengan stop-and-wait, utilisasi sender kira-kira

- a. 0,27
- b. 0,027
- c. 0,0027
- d. 0,00027 ✅

**Jawaban:** d

**Penjelasan:**
- Waktu transmisi: D_trans = L/R = 8.000 / 10⁹ = 8 µs = 0,008 ms
- RTT = 2 × 15 ms = 30 ms
- U_sender = (L/R) / (RTT + L/R) = 0,008 / 30,008 ≈ 0,000267 ≈ **0,00027**

Sender hanya sibuk mengirim sekitar 0,027% dari waktu. Ini alasan dibutuhkannya pipelining.

---

### Soal 14
Sebuah protokol pipelined memakai nomor urut sepanjang 3 bit. Ukuran window maksimum untuk Go-Back-N dan Selective Repeat berturut-turut adalah

- a. 4 dan 4
- b. 7 dan 7
- c. 8 dan 8
- d. 7 dan 4 ✅

**Jawaban:** d

**Penjelasan:** Dengan k = 3 bit ada 2³ = 8 nomor urut.
- Go-Back-N: window maksimum = 2ᵏ − 1 = **7**
- Selective Repeat: window maksimum = 2ᵏ⁻¹ = **4** (setengah ruang nomor urut)

---

### Soal 15
Perbedaan Selective Repeat dibanding Go-Back-N pada sisi sender adalah

- a. SR memakai satu timer untuk paket tertua, GBN memakai timer per paket
- b. SR mengirim ulang semua paket dalam window ketika timeout
- c. SR memakai timer per paket dan hanya mengirim ulang paket yang timeout ✅
- d. SR tidak memakai window, GBN memakai window

**Jawaban:** c

**Penjelasan:** Pada SR sender memiliki timer untuk setiap paket yang belum di-ACK dan hanya mengirim ulang paket yang timeout. GBN memakai satu timer (untuk paket tertua yang belum di-ACK) dan mengirim ulang seluruh paket dari yang tertua itu sampai akhir window. Kedua protokol sama-sama memakai window.

---

### Soal 16
Pada Go-Back-N, receiver menerima paket dengan nomor urut lebih tinggi dari yang diharapkan (ada gap). Tindakan receiver adalah

- a. Mengirim NAK untuk paket yang hilang
- b. Menyimpan paket tersebut di buffer dan mengirim ACK untuk paket itu
- c. Membuang paket tersebut dan mengirim ulang ACK untuk nomor urut berurutan tertinggi ✅
- d. Mengirim ACK untuk paket tersebut tanpa menyimpannya

**Jawaban:** c

**Penjelasan:** Receiver GBN tidak mem-buffer paket out-of-order. Paket itu dibuang dan receiver mengirim ulang ACK kumulatif untuk paket in-order terakhir. Penyimpanan paket out-of-order dan ACK individual adalah ciri Selective Repeat.

---

### Soal 17
Selective Repeat memakai nomor urut 0, 1, 2, 3 dengan ukuran window 3. Masalah yang dapat terjadi adalah

- a. Sender tidak pernah dapat memajukan window-nya
- b. Timer per paket tidak pernah berakhir
- c. Receiver kehabisan buffer karena menyimpan paket out-of-order
- d. Receiver tidak dapat membedakan retransmisi paket lama dengan paket baru yang bernomor urut sama ✅

**Jawaban:** d

**Penjelasan:** Syarat SR: ukuran window ≤ setengah ruang nomor urut. Ruang = 4, jadi window maksimum = 2, sedangkan di sini window = 3. Akibatnya window sender dan receiver bisa tumpang tindih sehingga retransmisi paket lama bisa disangka paket baru dengan nomor urut yang sama, dan data duplikat diteruskan ke aplikasi.

---

## 3. TCP: Segmen, Koneksi, dan RTT

### Soal 18
Nomor urut (sequence number) pada segmen TCP menyatakan

- a. Jumlah byte yang sudah diterima oleh penerima
- b. Nomor byte terakhir dari data segmen tersebut
- c. Nomor byte pertama dari data segmen tersebut dalam aliran byte ✅
- d. Nomor segmen ke berapa dalam koneksi

**Jawaban:** c

**Penjelasan:** TCP bekerja pada aliran byte, sehingga sequence number adalah nomor byte pertama dalam data segmen, bukan nomor segmen. Contoh: segmen dengan Seq = 92 dan 8 byte data membawa byte 92–99.

---

### Soal 19
Host A mengirim dua segmen berturut-turut: Seq=92 dengan 8 byte data, lalu Seq=100 dengan 20 byte data. Segmen pertama hilang dan hanya segmen kedua yang tiba di Host B. Nilai ACK yang dikirim Host B adalah

- a. ACK=92 ✅
- b. ACK=120
- c. ACK=100
- d. ACK=108

**Jawaban:** a

**Penjelasan:** ACK TCP bersifat kumulatif dan berisi nomor byte berikutnya yang diharapkan. Byte 92–99 (segmen pertama) belum diterima, sehingga B masih menunggu byte 92 dan mengirim ACK=92 (duplicate ACK), meskipun segmen kedua sudah tiba.

---

### Soal 20
Ukuran RcvBuffer bawaan (typical default) pada TCP yang disebutkan dalam materi adalah

- a. 2.048 byte
- b. 1.024 byte
- c. 4.096 byte ✅
- d. 65.535 byte

**Jawaban:** c

**Penjelasan:** Nilai default RcvBuffer yang umum disebut dalam materi adalah 4096 byte (OS biasanya mengatur ukurannya secara otomatis).

---

### Soal 21
Nilai rwnd pada header TCP dipakai penerima untuk memberi tahu pengirim mengenai

- a. Ukuran segmen maksimum yang boleh dikirim
- b. Jumlah byte yang sudah diterima secara berurutan
- c. Jumlah segmen yang hilang di jaringan
- d. Ruang buffer penerima yang masih kosong ✅

**Jawaban:** d

**Penjelasan:** rwnd (receive window) = RcvBuffer − (LastByteRcvd − LastByteRead), yaitu sisa ruang buffer penerima. Sender menjaga jumlah data yang belum di-ACK tidak melebihi rwnd (flow control) agar buffer penerima tidak meluap. Jika rwnd = 0, sender berhenti mengirim data.

---

### Soal 22
Bit flag pada header TCP yang dipakai untuk memulai pembentukan koneksi adalah

- a. PSH
- b. SYN ✅
- c. FIN
- d. RST

**Jawaban:** b

**Penjelasan:** Bit SYN dipakai pada tahap awal three-way handshake (SYN, SYN+ACK, ACK). FIN menutup koneksi, RST mereset koneksi, dan PSH meminta data segera diteruskan ke aplikasi.

---

### Soal 23
Mengapa two-way handshake tidak selalu cukup untuk membentuk koneksi di jaringan nyata?

- a. Karena pesan dapat tertunda, diretransmisi, atau salah urut sehingga koneksi setengah terbuka dan data lama dapat diterima ✅
- b. Karena TCP tidak memiliki field ACK
- c. Karena klien harus mengirim data bersama permintaan koneksi
- d. Karena server tidak dapat memilih nomor urut awal

**Jawaban:** a

**Penjelasan:** Di jaringan dengan delay variabel, permintaan koneksi lama yang tertunda/diretransmisi bisa tiba kemudian. Server bisa membuka koneksi "setengah terbuka" atau menerima data lama sebagai data baru. Three-way handshake membuat kedua pihak saling mengonfirmasi nomor urut awal sehingga masalah ini dihindari.

---

### Soal 24
Untuk menutup sisi koneksinya, sebuah host TCP mengirim segmen dengan

- a. Bit URG bernilai 1
- b. Bit FIN bernilai 1 ✅
- c. Bit RST bernilai 1
- d. Bit SYN bernilai 1

**Jawaban:** b

**Penjelasan:** Penutupan koneksi TCP yang normal memakai segmen dengan FIN = 1. Tiap sisi menutup arahnya sendiri, lalu pihak lawan membalas dengan ACK. RST dipakai untuk menolak/mereset koneksi secara paksa, bukan penutupan normal.

---

### Soal 25
Nilai alpha yang umum dipakai pada rumus EstimatedRTT TCP adalah

- a. 0,5
- b. 0,25
- c. 0,125 ✅
- d. 0,875

**Jawaban:** c

**Penjelasan:** EstimatedRTT = (1 − α) × EstimatedRTT + α × SampleRTT dengan α = 0,125 (1/8). Ini adalah exponential weighted moving average, di mana pengaruh sampel lama mengecil secara eksponensial. Nilai 0,875 adalah (1 − α).

---

### Soal 26
EstimatedRTT saat ini 100 ms dan alpha = 0,125. Diperoleh SampleRTT baru 180 ms. Nilai EstimatedRTT yang baru adalah

- a. 140 ms
- b. 105 ms
- c. 110 ms ✅
- d. 170 ms

**Jawaban:** c

**Penjelasan:** EstimatedRTT baru = 0,875 × 100 + 0,125 × 180 = 87,5 + 22,5 = **110 ms**.

---

### Soal 27
Soal lanjutan Soal 26: EstimatedRTT lama = 100 ms, SampleRTT = 180 ms (sehingga EstimatedRTT baru = 110 ms), α = 0,125. Jika DevRTT lama = 5 ms, β = 0,25, dan selisih SampleRTT terhadap EstimatedRTT lama adalah 80 ms, nilai TimeoutInterval yang baru adalah

- a. 133,75 ms
- b. 110 ms
- c. 190 ms
- d. 205 ms ✅

**Jawaban:** d

**Penjelasan:**
- DevRTT baru = (1 − β) × DevRTT + β × |SampleRTT − EstimatedRTT| = 0,75 × 5 + 0,25 × 80 = 3,75 + 20 = 23,75 ms
- TimeoutInterval = EstimatedRTT + 4 × DevRTT = 110 + 4 × 23,75 = 110 + 95 = **205 ms**

Opsi a (133,75) adalah jebakan karena lupa mengalikan DevRTT dengan 4. Opsi b hanya EstimatedRTT, dan opsi c memakai selisih 80 ms langsung.

---

### Soal 28
Jika nilai timeout TCP ditetapkan terlalu pendek dibanding RTT, akibatnya adalah

- a. Terjadi premature timeout dan retransmisi yang tidak perlu ✅
- b. Reaksi terhadap segmen yang hilang menjadi lambat
- c. Receive window menjadi nol
- d. Koneksi tidak pernah terbentuk

**Jawaban:** a

**Penjelasan:** Timeout yang terlalu pendek membuat sender mengira segmen hilang padahal ACK hanya belum tiba, sehingga terjadi retransmisi yang tidak perlu. Timeout yang terlalu panjang akan membuat reaksi terhadap loss lambat (opsi b adalah akibat timeout terlalu panjang).

---

### Soal 29
TCP melakukan fast retransmit ketika sender

- a. Menerima tiga ACK duplikat untuk data yang sama ✅
- b. Mendeteksi timeout pada segmen tertua
- c. Menerima segmen dengan bit RST
- d. Menerima rwnd bernilai nol

**Jawaban:** a

**Penjelasan:** Jika sender menerima 3 duplicate ACK (total 4 ACK dengan nomor sama) untuk data yang sama, TCP menganggap segmen setelahnya hilang dan langsung mengirim ulang tanpa menunggu timeout. Timeout adalah mekanisme deteksi loss yang berbeda.

---

## 4. Congestion Control

### Soal 30
Pada skenario kemacetan dengan router berbuffer tak hingga (skenario 1), ketika laju kedatangan mendekati kapasitas R

- a. Throughput turun menjadi nol
- b. Router mengirim notifikasi kemacetan ke pengirim
- c. Delay antrian membesar tanpa batas meskipun throughput mendekati R/2 ✅
- d. Paket mulai hilang karena buffer penuh

**Jawaban:** c

**Penjelasan:** Pada skenario 1 (dua pengirim, satu router dengan buffer tak hingga, kapasitas link R), tiap koneksi paling banyak mencapai throughput R/2. Saat laju kedatangan mendekati batas itu, antrian di router bertumbuh tanpa batas sehingga delay menuju tak hingga. Tidak ada paket yang hilang karena buffer tak hingga, dan router tidak mengirim notifikasi apa pun.

---

### Soal 31
Pendekatan congestion control yang dipakai TCP termasuk kategori

- a. Network-assisted dengan bit tunggal dari router
- b. End-to-end, kemacetan disimpulkan dari loss dan delay yang diamati end system ✅
- c. Berbasis reservasi bandwidth di setiap router
- d. Network-assisted dengan explicit rate dari router

**Jawaban:** b

**Penjelasan:** TCP tidak mendapat bantuan eksplisit dari jaringan. End system menyimpulkan adanya kemacetan dari loss (timeout/duplicate ACK) dan delay. Network-assisted (bit tunggal atau explicit rate) dipakai oleh ATM ABR.

---

### Soal 32
Pada fase slow start TCP, nilai awal cwnd adalah

- a. 2 MSS
- b. Sama dengan rwnd
- c. Setengah dari ssthresh
- d. 1 MSS ✅

**Jawaban:** d

**Penjelasan:** Koneksi TCP memulai slow start dengan cwnd = 1 MSS, lalu menggandakannya setiap RTT (setiap ACK menambah 1 MSS) hingga mencapai ssthresh atau terjadi loss.

---

### Soal 33
Koneksi TCP dimulai dengan cwnd = 1 MSS dan ssthresh = 8 MSS, tanpa loss sama sekali. Berapa nilai cwnd pada round transmisi ke-7 (round pertama cwnd = 1 MSS)?

- a. 16 MSS
- b. 11 MSS ✅
- c. 64 MSS
- d. 8 MSS

**Jawaban:** b

**Penjelasan:**

| Round | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|
| cwnd (MSS) | 1 | 2 | 4 | 8 | 9 | 10 | **11** |

Slow start (eksponensial) berlangsung sampai cwnd mencapai ssthresh = 8 (round 4). Setelah itu masuk congestion avoidance, yaitu bertambah linear 1 MSS per RTT, sehingga round 7 = 11 MSS.

---

### Soal 34
Pada TCP Reno, cwnd = 16 MSS ketika terjadi loss yang dideteksi lewat timeout. Setelah kejadian ini, nilai ssthresh dan cwnd berturut-turut adalah

- a. ssthresh = 8 MSS, cwnd = 1 MSS ✅
- b. ssthresh = 16 MSS, cwnd = 8 MSS
- c. ssthresh = 1 MSS, cwnd = 1 MSS
- d. ssthresh = 8 MSS, cwnd = 8 MSS

**Jawaban:** a

**Penjelasan:** Pada loss karena **timeout**, ssthresh = cwnd/2 = 8 MSS dan cwnd di-reset ke 1 MSS, lalu kembali ke slow start. Opsi d adalah perilaku Reno untuk loss yang dideteksi lewat 3 duplicate ACK (fast recovery): cwnd = ssthresh (+3 MSS), bukan 1.

---

### Soal 35
Mengabaikan slow start, loss terjadi saat ukuran window W = 1.000.000 bit dan RTT = 200 ms. Throughput rata-rata koneksi TCP tersebut kira-kira

- a. 5 Mbps
- b. 2,5 Mbps
- c. 3,75 Mbps ✅
- d. 7,5 Mbps

**Jawaban:** c

**Penjelasan:** Throughput rata-rata TCP = 0,75 × W / RTT, karena window berayun antara W/2 dan W (rata-rata 3W/4).
= 0,75 × 1.000.000 bit / 0,2 s = **3.750.000 bps = 3,75 Mbps**.
(Laju saat window penuh adalah W/RTT = 5 Mbps, itu opsi a.)

---

### Soal 36
Untuk mencapai throughput 10 Gbps dengan segmen 1.500 byte dan RTT 100 ms, sebuah koneksi TCP memerlukan window in-flight sekitar

- a. 83.333 segmen ✅
- b. 8.333 segmen
- c. 833 segmen
- d. 833.333 segmen

**Jawaban:** a

**Penjelasan:**
- Window (bit) = throughput × RTT = 10 × 10⁹ × 0,1 = 10⁹ bit
- Satu segmen = 1.500 × 8 = 12.000 bit
- Jumlah segmen = 10⁹ / 12.000 ≈ **83.333 segmen**

Soal ini menunjukkan bahwa pada link berkapasitas tinggi, TCP butuh window yang sangat besar, dan loss rate yang dapat ditoleransi menjadi sangat kecil.

---

### Soal 37
Pada ATM ABR, bit CI pada RM cell menandakan

- a. Indikasi kemacetan pada jalur ✅
- b. RM cell telah kembali ke sender
- c. Tidak boleh ada kenaikan laju (kemacetan ringan)
- d. Laju eksplisit yang diizinkan

**Jawaban:** a

**Penjelasan:** Pada ATM ABR (network-assisted congestion control), RM cell membawa tiga informasi penting: bit **CI** (congestion indication, ada kemacetan di jalur), bit **NI** (no increase, kemacetan ringan sehingga laju tidak boleh naik), dan field **ER** (explicit rate, laju maksimum yang diizinkan). Opsi b adalah fungsi bit lain yang menandai arah RM cell, bukan CI.

---

## 5. Web, HTTP, Cookie, dan Web Cache

### Soal 38
Komponen cookie yang disimpan di sisi pengguna dan dikelola oleh browser adalah

- a. Baris header Set-cookie pada respons HTTP
- b. Baris header Cookie pada permintaan HTTP berikutnya
- c. Berkas cookie di host pengguna ✅
- d. Basis data back-end di situs web

**Jawaban:** c

**Penjelasan:** Cookie memiliki empat komponen: (1) header Set-cookie pada respons HTTP, (2) header Cookie pada permintaan HTTP berikutnya, (3) **berkas cookie yang disimpan di host pengguna dan dikelola browser**, dan (4) basis data back-end di situs web. Hanya komponen ketiga yang berada di sisi pengguna dan dikelola browser.

---

### Soal 39
Susan mengakses sebuah situs e-commerce untuk pertama kali. Apa yang dilakukan situs tersebut agar dapat mengenali Susan pada kunjungan berikutnya?

- a. Membuat ID unik dan entri basis data, lalu mengirimkannya lewat header Set-cookie ✅
- b. Membuka koneksi TCP persisten yang tidak pernah ditutup
- c. Menyimpan alamat IP Susan secara permanen di server
- d. Meminta Susan memasukkan nama pengguna pada setiap permintaan HTTP

**Jawaban:** a

**Penjelasan:** Server membuat ID unik dan entri di basis data back-end, lalu mengirim ID itu lewat header `Set-cookie` pada respons. Browser menyimpannya dan menyertakannya pada header `Cookie` di permintaan berikutnya, sehingga HTTP yang stateless dapat "mengingat" pengguna. IP tidak andal (bisa berubah dan dibagi), dan koneksi persisten tetap ditutup setelah timeout.

---

### Soal 40
Kode status yang dikirim server sebagai jawaban Conditional GET jika objek di cache masih mutakhir adalah

- a. 301 Moved Permanently
- b. 404 Not Found
- c. 200 OK
- d. 304 Not Modified ✅

**Jawaban:** d

**Penjelasan:** Pada Conditional GET, cache mengirim header `If-modified-since: <tanggal>`. Jika objek tidak berubah, server membalas `304 Not Modified` tanpa isi objek, sehingga menghemat bandwidth dan waktu. Jika objek berubah, server mengirim `200 OK` beserta objek barunya.

---

### Soal 41
Cache web dikatakan berperan sebagai klien sekaligus server. Pernyataan yang tepat adalah

- a. Klien bagi ISP dan server bagi router institusi
- b. Server bagi DNS dan klien bagi browser
- c. Server bagi origin server dan klien bagi browser pengguna
- d. Server bagi browser yang meminta objek dan klien bagi origin server ✅

**Jawaban:** d

**Penjelasan:** Bagi browser, cache (proxy server) berperan sebagai server karena melayani permintaan objek. Ketika objek belum ada di cache, cache meminta ke origin server sebagai klien. Opsi c membalik perannya.

---

### Soal 42
Internet yang padat dengan cache memberi manfaat berupa

- a. Objek dinamis dapat dilayani tanpa diproses server
- b. Penyedia konten dengan sumber daya terbatas tetap dapat menyampaikan konten secara efektif ✅
- c. Origin server tidak lagi perlu menyimpan objek asli
- d. Browser tidak lagi memerlukan koneksi TCP

**Jawaban:** b

**Penjelasan:** Cache mengurangi waktu respons, mengurangi lalu lintas di access link institusi, dan mengurangi beban origin server, sehingga penyedia konten dengan sumber daya terbatas tetap bisa menyampaikan kontennya dengan efektif. Objek dinamis tidak bisa dilayani begitu saja dari cache, origin server tetap menyimpan objek aslinya, dan browser tetap memakai TCP.

---

### Soal 43
Sebuah institusi memiliki access link 1,54 Mbps dan laju data rata-rata dari origin server ke browser 1,50 Mbps. Jika dipasang cache dengan hit rate 0,4, berapa utilisasi access link setelah cache dipasang?

- a. Sekitar 0,97
- b. Sekitar 0,40
- c. Sekitar 0,58 ✅
- d. Sekitar 0,39

**Jawaban:** c

**Penjelasan:**
- Permintaan yang harus diambil dari origin server (cache miss) = 1 − 0,4 = 0,6
- Laju lewat access link = 0,6 × 1,50 Mbps = 0,9 Mbps
- Utilisasi = 0,9 / 1,54 ≈ **0,58**

Sebelum ada cache utilisasinya 1,50/1,54 ≈ 0,97 (opsi a), yang berarti delay antrian sangat besar.

---

## 6. FTP dan E-mail

### Soal 44
Informasi "state" yang dipertahankan server FTP selama satu sesi adalah

- a. Daftar semua file yang pernah diunduh klien sejak server dinyalakan
- b. Direktori kerja saat ini dan status autentikasi klien ✅
- c. Salinan setiap file yang sedang ditransfer
- d. Nomor port acak yang dipakai untuk koneksi kontrol

**Jawaban:** b

**Penjelasan:** Berbeda dengan HTTP yang stateless, server FTP bersifat stateful: ia melacak direktori kerja saat ini dan status autentikasi pengguna selama sesi kontrol berlangsung. Port kontrol FTP juga bukan port acak (port 21).

---

### Soal 45
Kode respons FTP 331 memiliki arti

- a. Koneksi data tidak dapat dibuka
- b. Terjadi kesalahan saat menulis file
- c. Username diterima, password diperlukan ✅
- d. Koneksi data sudah terbuka dan transfer dimulai

**Jawaban:** c

**Penjelasan:** `331 Username OK, password required` dikirim setelah perintah `USER`. Klien kemudian mengirim `PASS`. Kode lain yang umum: 125 (koneksi data sudah terbuka, transfer dimulai), 425 (tidak dapat membuka koneksi data), 452 (error menulis file).

---

### Soal 46
SMTP mensyaratkan header dan body pesan surat berada dalam format

- a. Biner tanpa pengkodean
- b. 7-bit ASCII ✅
- c. 8-bit Latin-1
- d. Unicode UTF-8

**Jawaban:** b

**Penjelasan:** SMTP adalah protokol lama yang mengharuskan seluruh pesan (header dan body) dalam 7-bit ASCII. Data biner atau non-ASCII harus dikodekan dulu (misalnya dengan MIME/base64).

---

### Soal 47
Perintah SMTP yang digunakan klien untuk menyatakan alamat penerima surat adalah

- a. RCPT TO ✅
- b. MAIL FROM
- c. DATA
- d. HELO

**Jawaban:** a

**Penjelasan:** Urutan dialog SMTP: `HELO` (perkenalan), `MAIL FROM` (alamat pengirim), `RCPT TO` (alamat penerima), `DATA` (isi surat, diakhiri baris berisi satu titik), `QUIT`.

---

### Soal 48
Bob membaca e-mail dari laptop dan ponsel secara bergantian dan ingin struktur folder serta status pesannya sama di kedua perangkat. Protokol akses yang paling sesuai adalah

- a. POP3 mode download-and-delete
- b. SMTP
- c. IMAP ✅
- d. POP3 mode download-and-keep

**Jawaban:** c

**Penjelasan:** IMAP menyimpan pesan, folder, dan status pesan di server sehingga konsisten di banyak perangkat. POP3 stateless antar sesi: download-and-delete memindahkan surat ke satu perangkat saja, dan download-and-keep membuat salinan terpisah di tiap perangkat tanpa sinkronisasi status. SMTP hanya untuk mengirim, bukan mengakses surat.

---

### Soal 49
Respons yang diberikan server POP3 ketika sebuah perintah berhasil dijalankan adalah

- a. 200 OK
- b. ACK
- c. +OK ✅
- d. 250 OK

**Jawaban:** c

**Penjelasan:** POP3 memakai respons `+OK` (sukses) atau `-ERR` (gagal). Format `250 OK` adalah gaya balasan SMTP, dan `200 OK` adalah gaya HTTP.

---

## 7. DNS

### Soal 50
Resource record DNS dengan type=A memiliki value berupa

- a. Nama mail server yang terkait dengan domain
- b. Alamat IP dari hostname pada field name ✅
- c. Nama kanonik dari sebuah alias
- d. Hostname dari authoritative name server untuk domain tersebut

**Jawaban:** b

**Penjelasan:** Jenis RR DNS: **A** = hostname → alamat IP; **NS** = domain → hostname authoritative name server; **CNAME** = alias → nama kanonik; **MX** = nama mail server untuk domain.

---

### Soal 51
Server DNS yang pertama kali dihubungi oleh sebuah host saat melakukan query nama adalah

- a. Root name server
- b. Authoritative name server
- c. Local DNS name server ✅
- d. TLD server

**Jawaban:** c

**Penjelasan:** Host mengirim query ke local DNS name server (default name server, biasanya dari ISP/institusi). Local server inilah yang kemudian menghubungi root, TLD, atau authoritative server bila perlu, atau langsung menjawab dari cache.

---

### Soal 52
Pada iterated query DNS, server yang dihubungi dan tidak mengetahui jawaban akan

- a. Membalas dengan nama server lain yang sebaiknya dihubungi ✅
- b. Menolak query dan menutup koneksi
- c. Meneruskan query ke server lain dan menunggu jawabannya
- d. Mengembalikan alamat IP dari cache meskipun sudah kedaluwarsa

**Jawaban:** a

**Penjelasan:** Pada iterated query, server membalas "saya tidak tahu, tanyakan ke server ini" (referral) dan klien yang melanjutkan query ke server berikutnya. Meneruskan query lalu menunggu jawabannya adalah ciri recursive query, yang membebani server yang dihubungi.

---

### Soal 53
Mengapa root name server relatif jarang dihubungi dalam praktik?

- a. Karena root server hanya melayani query recursive
- b. Karena hanya ada 13 root server sehingga aksesnya dibatasi
- c. Karena local name server biasanya sudah menyimpan cache alamat TLD server ✅
- d. Karena authoritative server selalu menjawab langsung ke host

**Jawaban:** c

**Penjelasan:** Local DNS server meng-cache pemetaan nama ke server TLD (dan hasil query sebelumnya), sehingga query ke root jarang diperlukan. Root server juga tidak membatasi akses dan umumnya melayani iterated query, bukan recursive.

---

### Soal 54
Serangan yang mengirimkan balasan DNS palsu agar disimpan di cache server DNS disebut

- a. Amplification attack
- b. DNS poisoning ✅
- c. DDoS terhadap root server
- d. Traffic filtering

**Jawaban:** b

**Penjelasan:** DNS poisoning (cache poisoning) menyuntikkan RR palsu ke cache DNS server, sehingga pengguna yang meminta nama itu diarahkan ke alamat IP palsu (misalnya ke situs penipuan). DDoS terhadap root server bertujuan membanjiri server, dan traffic filtering adalah pertahanan, bukan serangan.

---

## 8. P2P, BitTorrent, dan DHT

### Soal 55
Laju upload tiap klien adalah u, F/u = 1 jam, laju upload server u_s = 10u, dan d_min ≥ u_s. Untuk N = 30 klien, waktu minimum distribusi file dengan pendekatan client-server adalah

- a. 0,75 jam
- b. 0,3 jam
- c. 3 jam ✅
- d. 1 jam

**Jawaban:** c

**Penjelasan:** D_cs ≥ max{ N·F/u_s , F/d_min }.
- N·F/u_s = 30·F / (10u) = 3 · (F/u) = **3 jam**
- F/d_min ≤ F/u_s = 0,1 jam, jadi suku pertama yang dominan.

Waktu distribusi client-server tumbuh linear terhadap N.

---

### Soal 56
Laju upload tiap klien adalah u, F/u = 1 jam, laju upload server u_s = 10u, dan d_min ≥ u_s. Untuk N = 30 peer, waktu minimum distribusi file dengan pendekatan P2P adalah

- a. 3 jam
- b. 0,1 jam
- c. 0,75 jam ✅
- d. 1 jam

**Jawaban:** c

**Penjelasan:** D_P2P ≥ max{ F/u_s , F/d_min , N·F/(u_s + Σu_i) }.
- F/u_s = 0,1 jam
- N·F/(u_s + N·u) = 30·F / (10u + 30u) = (30/40) · (F/u) = **0,75 jam**

Suku ketiga yang dominan. Dibanding client-server (3 jam), P2P jauh lebih cepat karena setiap peer ikut menambah kapasitas upload.

---

### Soal 57
Dalam BitTorrent, sebuah file dibagi menjadi potongan (chunk) berukuran

- a. 256 Kb ✅
- b. 64 Kb
- c. 128 Kb
- d. 1 Mb

**Jawaban:** a

**Penjelasan:** Dalam materi BitTorrent, file dibagi menjadi chunk berukuran 256 Kb, dan kumpulan peer yang berbagi satu file disebut torrent.

---

### Soal 58
Dalam BitTorrent, mekanisme tit-for-tat berarti Alice

- a. Mengirim chunk kepada semua peer secara bergiliran
- b. Mengirim chunk kepada peer yang memiliki chunk paling langka
- c. Hanya menerima chunk tanpa pernah mengirim
- d. Mengirim chunk kepada empat peer yang saat ini mengirim chunk kepadanya dengan laju tertinggi ✅

**Jawaban:** d

**Penjelasan:** Alice mengirim chunk ke 4 peer yang saat ini mengirim data kepadanya dengan laju tertinggi (unchoke). Daftar ini dievaluasi ulang setiap 10 detik, dan setiap 30 detik Alice juga memilih satu peer acak (optimistic unchoke) agar peer baru punya kesempatan. Memilih chunk paling langka (opsi b) adalah strategi *rarest first* untuk *meminta* chunk, bukan untuk memilih tujuan pengiriman.

---

### Soal 59
Pada DHT dengan ruang ID 0 sampai 63 dan peer dengan ID 1, 12, 13, 25, 32, 40, 48, 60, pasangan key-value dengan key = 45 disimpan pada peer

- a. 60
- b. 32
- c. 40
- d. 48 ✅

**Jawaban:** d

**Penjelasan:** Aturan DHT: key disimpan di peer dengan ID terdekat yang *tidak lebih kecil* dari key, yaitu **successor** langsung dari key (bila tidak ada, kembali ke awal lingkaran/mod 64). Peer setelah 45 adalah 48, sehingga key 45 disimpan di peer 48.

---

### Soal 60
Untuk menangani peer churn pada circular DHT, setiap peer menyimpan alamat dari

- a. Satu predecessor saja
- b. Dua successor berikutnya ✅
- c. Semua peer dalam jaringan
- d. Satu successor saja

**Jawaban:** b

**Penjelasan:** Jika setiap peer hanya mengenal satu successor, kepergian successor itu memutus lingkaran. Dengan menyimpan dua successor berikutnya dan memeriksanya secara berkala (ping), peer dapat menyambung kembali ke successor kedua saat yang pertama keluar. Menyimpan semua peer tidak skalabel.
