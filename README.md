<div align="center">

<img width="339" height="450" alt="image" src="https://github.com/user-attachments/assets/a3472026-0ddf-496f-8266-48eac3e8a89e" />

# Suno Link Grabber

**Ekstensi Chrome untuk mengambil semua link share (`suno.com/s/...`) dari lagu-lagu di akun Suno Anda secara otomatis, lalu mengunduhnya sebagai file `.txt`.**

![Manifest V3](https://img.shields.io/badge/Chrome-Manifest%20V3-8b5cf6?logo=googlechrome&logoColor=white)
![Version](https://img.shields.io/badge/version-2.0-ec4899)
![License](https://img.shields.io/badge/license-MIT-34d399)

</div>

---

## Fitur

- **Otomatis penuh**: mengklik menu `...` > **Share** > **Copy Link** pada setiap lagu, tanpa perlu klik manual satu per satu.
- **Panel melayang di halaman Suno**: progress terlihat langsung tanpa membuka popup, sehingga proses tidak terganggu.
- **Auto-scroll**: memuat lagu yang belum tampil (lazy-load) sampai seluruh daftar selesai.
- **Berhenti otomatis** saat semua lagu sudah diproses.
- **Tanpa duplikat**: setiap lagu dikenali dari ID-nya, jadi link yang sama tidak disimpan dua kali.
- **Export ke TXT**: satu link per baris, siap dipakai di tool lain.
- **Tombol praktis**: Mulai, Tes 1 lagu, Stop, Download TXT, Salin ke clipboard, dan Hapus daftar.
- **Panel bisa digeser**, dikecilkan menjadi pil kecil, atau disembunyikan.

Contoh isi file hasil download:

```text
https://suno.com/s/vF9i1KEf6dz6Fg3i
https://suno.com/s/xxxxxxxxxxxxxxxx
https://suno.com/s/yyyyyyyyyyyyyyyy
```

## Instalasi

Ekstensi ini belum ada di Chrome Web Store, jadi dipasang secara manual (mode developer).

1. **Download** repo ini (`Code` > `Download ZIP`) lalu ekstrak, atau clone:
   ```bash
   git clone https://github.com/maxnmrantau/suno-link-grabber.git
   ```
2. Buka `chrome://extensions` di Chrome (atau browser berbasis Chromium seperti Edge dan Brave).
3. Aktifkan **Developer mode** di pojok kanan atas.
4. Klik **Load unpacked**, lalu pilih folder ekstensi (folder yang berisi `manifest.json`).
5. Buka [suno.com](https://suno.com), lalu **refresh** halaman. Panel Link Grabber akan muncul di pojok kanan bawah.

## Cara pakai

1. Buka halaman daftar lagu Anda di Suno (mis. **Library** atau **Create**).
2. Tekan **Tes 1 lagu** untuk memastikan semuanya berjalan. Satu link harus muncul di daftar panel.
3. Tekan **▶ Mulai**. Ekstensi akan memproses lagu satu per satu dan men-scroll sendiri.
4. Tunggu sampai status berubah menjadi **✅ Selesai**.
5. Tekan **Download TXT** (atau **Salin**) untuk mengambil hasilnya.

> **Tips**
> - Selama proses berjalan, jangan klik apa pun di halaman Suno dan jangan pindah tab. Satu-satunya yang aman diklik adalah **Stop**.
> - Klik ikon ekstensi di toolbar untuk menampilkan atau menyembunyikan panel.
> - Tombol **–** di panel mengecilkan panel menjadi pil kecil yang menampilkan jumlah link.

## Cara kerja

Ekstensi berjalan sepenuhnya di browser Anda, dengan tiga bagian:

| File | Fungsi |
| --- | --- |
| `hook.js` | Berjalan di konteks halaman. Menangkap teks yang disalin saat tombol **Copy Link** diklik (`navigator.clipboard.writeText` dan event `copy`), dengan cadangan memantau respons jaringan. |
| `content.js` | Otomasi dan antarmuka: mencari baris lagu (`[data-testid="clip-row"]`), membuka menu `More options`, memilih **Share** lalu **Copy Link**, men-scroll daftar, menyimpan hasil, dan menampilkan panel melayang (Shadow DOM). |
| `background.js` | Service worker kecil: klik ikon ekstensi menampilkan atau menyembunyikan panel. |

Alur per lagu:

```text
hover baris -> klik "More options" -> klik "Share" -> klik "Copy Link"
            -> tangkap link dari clipboard -> simpan -> tutup dialog -> lagu berikutnya
```

Jika menu `...` gagal terbuka, ekstensi memakai tombol **Share** yang tampil langsung di baris lagu sebagai cadangan.

## Struktur proyek

```text
suno-link-grabber/
├── manifest.json     # konfigurasi ekstensi (Manifest V3)
├── background.js     # toggle panel lewat ikon toolbar
├── hook.js           # penangkap clipboard (MAIN world)
├── content.js        # otomasi + panel UI
├── icons/            # ikon 16, 32, 48, 128 px
└── README.md
```

## Izin yang dipakai

| Izin | Alasan |
| --- | --- |
| `storage` | Menyimpan daftar link, status, dan posisi panel di browser. |
| `clipboardRead` | Cadangan untuk membaca link dari clipboard jika penangkapan utama gagal. |
| `host_permissions` (`suno.com`) | Menjalankan ekstensi hanya di situs Suno. |

## Privasi

- Semua proses terjadi **lokal di browser Anda**.
- Ekstensi **tidak mengirim data apa pun** ke server mana pun, tidak memakai analytics, dan tidak meminta login atau password Suno.
- Data hanya tersimpan di `chrome.storage.local` dan bisa dihapus lewat tombol **Hapus** di panel.

## Troubleshooting

| Masalah | Solusi |
| --- | --- |
| Panel tidak muncul | Refresh halaman Suno. Pastikan ekstensi aktif, lalu klik ikon ekstensi di toolbar. |
| Status "Tidak ada baris lagu terdeteksi" | Pastikan Anda berada di halaman yang menampilkan daftar lagu, bukan halaman satu lagu. |
| Status "Dialog Share / Copy Link tidak muncul" | Jangan klik apa pun saat proses berjalan. Jika tetap gagal, UI Suno mungkin berubah, jadi periksa selector di `content.js`. |
| Berhenti sebelum semua lagu terambil | Koneksi mungkin lambat. Jalankan lagi, karena lagu yang sudah ada tidak akan disimpan ganda. |
| Setelah update, ekstensi tidak berubah | Buka `chrome://extensions`, klik **Reload** pada ekstensi, lalu refresh halaman Suno. |

Untuk melihat log, tekan `F12`, buka tab **Console**, dan cari baris berawalan `[SunoScraper]`.

## Catatan penting

- Ekstensi ini **tidak resmi** dan **tidak berafiliasi dengan Suno**.
- Ekstensi bergantung pada struktur tampilan (HTML) Suno. Jika Suno mengubah desainnya, selector di `content.js` mungkin perlu diperbarui.
- Gunakan hanya untuk lagu milik akun Anda sendiri, dan patuhi [Terms of Service Suno](https://suno.com/terms).

## Kontribusi

Issue dan pull request sangat diterima. Jika menemukan bug, sertakan:

1. Teks status dari panel.
2. Log dari Console (baris `[SunoScraper]`).
3. Potongan HTML tombol yang bermasalah (klik kanan > **Inspect** > **Copy outerHTML**).


