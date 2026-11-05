# Ringkasan Materi Kuis 2 Jaringan Komputer

## 1. Layer Transport & Socket

**Fungsi lapisan transport**
- Menyediakan komunikasi logis antar *proses* (bukan antar host seperti lapisan network).
- Sisi pengirim: memecah pesan aplikasi menjadi **segmen**, menambah header, lalu menyerahkannya ke lapisan network. Sisi penerima: menyusun kembali segmen menjadi pesan dan menyerahkannya ke aplikasi.
- Dua protokol utama: **TCP** (andal, berorientasi koneksi, ada flow dan congestion control) dan **UDP** (tidak andal, tanpa koneksi, tanpa kontrol).

**Multiplexing / demultiplexing**

| | UDP | TCP |
|---|---|---|
| Identitas socket | 2 nilai: IP tujuan + port tujuan | 4 nilai: IP sumber, port sumber, IP tujuan, port tujuan |

**Socket programming (Python)**
- UDP memakai `SOCK_DGRAM`; TCP memakai `SOCK_STREAM`.
- Server TCP: *welcoming socket* menunggu koneksi, `accept()` mengembalikan **socket baru** khusus untuk satu klien.

**UDP vs TCP untuk aplikasi**
- Streaming multimedia sering memilih UDP: toleran terhadap loss, sensitif terhadap laju/delay, dan tidak ingin dibatasi congestion control.
- **Fairness**: TCP berbagi link secara adil per *koneksi*. Aplikasi yang membuka banyak koneksi paralel mendapat porsi lebih besar. Contoh: 9 koneksi + 11 koneksi baru → aplikasi baru mendapat 11/20 R ≈ R/2.

---

## 2. Reliable Data Transfer (RDT)

**Evolusi protokol rdt (membangun dari kanal paling sederhana)**

| Protokol | Asumsi kanal | Mekanisme / masalah |
|---|---|---|
| rdt1.0 | Andal sempurna (tanpa bit error, tanpa loss) | Tidak perlu umpan balik |
| rdt2.0 | Bit error | Checksum + ACK/NAK + retransmisi (*stop-and-wait*). **Cacat:** ACK/NAK bisa rusak |
| rdt2.1 | Bit error, ACK/NAK rusak | Tambah **nomor urut 0/1**; sender punya 2× state; receiver membuang duplikat |
| rdt2.2 | Sama, tanpa NAK | Receiver mengirim ACK untuk paket benar terakhir + nomor urutnya; **ACK duplikat** = NAK |
| rdt3.0 | Bit error + **loss** | Tambah **timer** di sender. Premature timeout hanya menyebabkan duplikat yang dibuang lewat nomor urut |

**Checksum (Internet checksum)**
1. Jumlahkan semua kata 16 bit.
2. Carry out ditambahkan kembali (wraparound).
3. Ambil **one's complement** (balik semua bit) sebagai checksum.

**Kinerja stop-and-wait**
- U_sender = (L/R) / (RTT + L/R)
- Contoh: 1 Gbps, propagasi 15 ms, paket 8.000 bit → L/R = 8 µs, RTT = 30 ms → U ≈ 0,00027. Sangat buruk, sehingga dibutuhkan **pipelining**.

**Protokol pipelined: Go-Back-N vs Selective Repeat**

| | Go-Back-N (GBN) | Selective Repeat (SR) |
|---|---|---|
| ACK | Kumulatif | Individual per paket |
| Timer | Satu timer (paket tertua) | Timer per paket |
| Saat timeout | Kirim ulang semua paket dari yang tertua sampai akhir window | Kirim ulang hanya paket yang timeout |
| Receiver, paket out-of-order | **Dibuang**, kirim ulang ACK in-order tertinggi | **Disimpan di buffer** dan di-ACK |
| Window maksimum (k bit) | 2ᵏ − 1 | 2ᵏ⁻¹ |

- Contoh k = 3 → ruang nomor urut 8 → GBN maks 7, SR maks 4.
- Jika window SR melebihi setengah ruang nomor urut (mis. nomor urut 0–3 dengan window 3), receiver **tidak bisa membedakan retransmisi paket lama dari paket baru**.

---

## 3. TCP: Segmen, Koneksi, dan RTT

**Nomor urut dan ACK**
- Sequence number = nomor **byte pertama** dalam data segmen (aliran byte, bukan nomor segmen).
- ACK = nomor byte berikutnya yang diharapkan (**kumulatif**). Contoh: segmen Seq=92 (8 byte) hilang, segmen Seq=100 tiba → ACK=92.

**Flow control**
- Penerima menyatakan ruang buffer kosong lewat **rwnd** = RcvBuffer − (LastByteRcvd − LastByteRead).
- RcvBuffer bawaan di materi: **4096 byte**. rwnd = 0 → sender berhenti mengirim data.

**Manajemen koneksi**
- **Three-way handshake**: SYN → SYN+ACK → ACK. Two-way handshake tidak cukup karena pesan bisa tertunda/diretransmisi/salah urut sehingga muncul koneksi setengah terbuka dan data lama terterima.
- Penutupan: kirim segmen **FIN = 1**, dibalas ACK (tiap arah ditutup sendiri).
- Flag lain: **RST** (reset paksa), **PSH** (dorong data ke aplikasi), URG (data mendesak).

**Estimasi RTT dan timeout**
- EstimatedRTT = (1 − α) · EstimatedRTT + α · SampleRTT, dengan **α = 0,125**
- DevRTT = (1 − β) · DevRTT + β · |SampleRTT − EstimatedRTT|, dengan **β = 0,25**
- **TimeoutInterval = EstimatedRTT + 4 · DevRTT**
- Contoh: Est = 100, Sample = 180 → Est baru = 110 ms. DevRTT 5 → 23,75 → Timeout = 110 + 95 = **205 ms**.
- Timeout terlalu pendek → premature timeout + retransmisi tak perlu. Terlalu panjang → reaksi terhadap loss lambat.

**Deteksi loss**
- **Timeout** pada segmen tertua.
- **Fast retransmit**: 3 ACK duplikat → kirim ulang tanpa menunggu timeout.

---

## 4. Congestion Control

**Penyebab dan skenario**
- Skenario 1 (buffer router tak hingga, kapasitas R, dua pengirim): throughput per koneksi maksimum **R/2**. Saat laju kedatangan mendekati R/2, **delay antrian membesar tanpa batas**, belum ada loss.
- Skenario dengan buffer terbatas: terjadi loss dan retransmisi, sehingga kapasitas terbuang untuk duplikat.

**Dua pendekatan**

| Pendekatan | Cara kerja | Contoh |
|---|---|---|
| **End-to-end** | End system menyimpulkan kemacetan dari loss dan delay | **TCP** |
| **Network-assisted** | Router memberi umpan balik (bit tunggal atau explicit rate) | **ATM ABR**, juga ECN |

**TCP congestion control (AIMD)**
- **Slow start**: cwnd awal = **1 MSS**, menggandakan tiap RTT sampai mencapai ssthresh.
- **Congestion avoidance**: setelah cwnd ≥ ssthresh, naik **linear +1 MSS per RTT**.
- Contoh (ssthresh = 8): round 1–7 → 1, 2, 4, 8, 9, 10, **11** MSS.
- **Loss karena timeout** (Reno): ssthresh = cwnd/2, cwnd = **1 MSS**, kembali ke slow start. (cwnd 16 → ssthresh 8, cwnd 1)
- **Loss karena 3 duplicate ACK**: ssthresh = cwnd/2, cwnd = ssthresh (+3 MSS), fast recovery.

**Throughput TCP**
- Throughput rata-rata ≈ **0,75 · W / RTT** (window berayun W/2 ↔ W). Contoh: W = 10⁶ bit, RTT = 200 ms → 3,75 Mbps.
- Window yang diperlukan untuk mencapai laju tertentu = throughput × RTT. Contoh: 10 Gbps × 100 ms = 10⁹ bit ÷ 12.000 bit/segmen ≈ **83.333 segmen**.

**ATM ABR (network-assisted)**
- RM cell membawa: **CI** (indikasi kemacetan), **NI** (no increase, kemacetan ringan), **ER** (explicit rate, laju yang diizinkan).

---

## 5. Web, HTTP, Cookie, dan Web Cache

**Cookie** (membuat HTTP yang stateless bisa mengenali pengguna). Empat komponen:
1. Header `Set-cookie` pada respons HTTP
2. Header `Cookie` pada permintaan berikutnya
3. **Berkas cookie di host pengguna** (dikelola browser)
4. Basis data back-end di situs web

Alur kunjungan pertama: server membuat ID unik + entri di basis data → kirim lewat `Set-cookie` → browser menyimpan dan mengirimkannya pada permintaan berikutnya.

**Web cache (proxy server)**
- Cache berperan sebagai **server bagi browser** dan **klien bagi origin server**.
- Manfaat: waktu respons lebih cepat, lalu lintas access link berkurang, beban origin server berkurang, sehingga penyedia konten dengan sumber daya terbatas pun dapat menyampaikan konten secara efektif.
- **Contoh perhitungan utilisasi**: access link 1,54 Mbps, laju 1,50 Mbps. Dengan hit rate 0,4 → lalu lintas = 0,6 × 1,50 = 0,9 Mbps → utilisasi = 0,9/1,54 ≈ **0,58** (sebelumnya ≈ 0,97).
- **Conditional GET**: header `If-modified-since`. Objek belum berubah → `304 Not Modified` (tanpa isi objek). Objek berubah → `200 OK` + objek.

---

## 6. FTP dan E-mail

**FTP**
- Dua koneksi paralel: koneksi **kontrol** (port 21) dan koneksi **data**.
- Server **stateful**: menyimpan direktori kerja saat ini dan status autentikasi selama sesi.
- Kode respons: **331** (username OK, password diperlukan), 125 (koneksi data terbuka, transfer dimulai), 425 (tidak dapat membuka koneksi data), 452 (error menulis file).

**E-mail**
- **SMTP** (mengirim): mewajibkan pesan dalam **7-bit ASCII**. Dialog: `HELO` → `MAIL FROM` → `RCPT TO` (alamat penerima) → `DATA` → `QUIT`. Respons bergaya kode numerik (mis. 250).
- **Protokol akses** (mengambil surat):
  - **POP3**: respons `+OK` / `-ERR`; mode *download-and-delete* atau *download-and-keep*; tidak menyinkronkan folder/status antar perangkat.
  - **IMAP**: surat, folder, dan status disimpan di server → konsisten di banyak perangkat.

---

## 7. DNS

**Jenis resource record**

| Type | name | value |
|---|---|---|
| **A** | hostname | alamat IP |
| **NS** | domain | hostname authoritative name server |
| **CNAME** | alias | nama kanonik |
| **MX** | domain | nama mail server |

**Hierarki dan alur query**
- Host → **local DNS name server** (dihubungi pertama kali) → bila perlu root → TLD → authoritative.
- **Iterated query**: server yang tidak tahu membalas dengan referral ("tanyakan ke server X"); klien/local server yang melanjutkan.
- **Recursive query**: server meneruskan query ke server lain dan menunggu jawaban (membebani server).
- Root server jarang dihubungi karena local name server biasanya sudah men-cache alamat server TLD.

**Keamanan**
- **DNS poisoning**: balasan palsu disuntikkan ke cache DNS sehingga pengguna diarahkan ke IP palsu.
- Serangan lain: DDoS terhadap root server, amplification attack. Traffic filtering adalah pertahanan.

---

## 8. P2P, BitTorrent, dan DHT

**Waktu distribusi file (F = ukuran file, u_s = upload server, u_i = upload peer, d_min = download terkecil)**
- Client-server: **D_cs ≥ max{ N·F/u_s , F/d_min }** (tumbuh linear terhadap N)
- P2P: **D_P2P ≥ max{ F/u_s , F/d_min , N·F/(u_s + Σu_i) }**
- Contoh (F/u = 1 jam, u_s = 10u, N = 30): client-server = **3 jam**, P2P = 30/(10+30) = **0,75 jam**.

**BitTorrent**
- File dibagi menjadi chunk **256 Kb**; kumpulan peer disebut *torrent*.
- **Tit-for-tat**: Alice mengirim chunk ke **4 peer** yang mengirim kepadanya dengan laju tertinggi (dievaluasi tiap 10 detik) + 1 peer acak tiap 30 detik (*optimistic unchoke*).
- Peminta chunk memakai strategi **rarest first**.

**DHT (Distributed Hash Table)**
- Pasangan (key, value) disimpan di peer dengan ID terdekat yang ≥ key, yaitu **successor** dari key (mod ukuran ruang ID). Contoh: ID peer 1, 12, 13, 25, 32, 40, 48, 60; key 45 → peer **48**.
- **Circular DHT**: tiap peer mengenal successor-nya.
- **Peer churn**: tiap peer menyimpan **dua successor berikutnya** dan memeriksanya secara berkala, sehingga lingkaran tetap tersambung saat satu successor keluar.

---

## Rumus Cepat

| Konsep | Rumus |
|---|---|
| Utilisasi stop-and-wait | (L/R) / (RTT + L/R) |
| Window maks GBN / SR (k bit) | 2ᵏ − 1 / 2ᵏ⁻¹ |
| EstimatedRTT | 0,875 · Est + 0,125 · Sample |
| DevRTT | 0,75 · Dev + 0,25 · \|Sample − Est\| |
| TimeoutInterval | Est + 4 · DevRTT |
| rwnd | RcvBuffer − (LastByteRcvd − LastByteRead) |
| Throughput TCP rata-rata | 0,75 · W / RTT |
| Window untuk laju tertentu | throughput × RTT |
| Utilisasi link dengan cache | (1 − hit rate) × laju / kapasitas |
| Reno loss timeout | ssthresh = cwnd/2, cwnd = 1 MSS |
| D_client-server | max{ N·F/u_s , F/d_min } |
| D_P2P | max{ F/u_s , F/d_min , N·F/(u_s + Σu_i) } |
