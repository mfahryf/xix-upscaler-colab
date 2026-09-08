# XIX-Upscaler Colab

Notebook publik **0.2.0** untuk engine **Video (Colab Experimental)** pada XIX-Upscaler. Gunakan bersama versi desktop yang mendukung pekerjaan Google Drive dengan worker 0.2.0. Runtime Python Colab 3.10–3.13 didukung.

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/mfahryf/xix-upscaler-colab/blob/main/XIX-Upscaler-Colab.ipynb)

## Cara menggunakan

1. Di desktop, pilih video, Folder Hasil, Mute, dan Target FPS, lalu tekan **Start**.
2. Hubungkan akun Google bila diminta dan tunggu seluruh upload selesai. Desktop membuka notebook ini; tautan **Open in Colab** juga tersedia di atas.
3. Pilih runtime **GPU**, lalu **Runtime → Run all**.
4. Izinkan mount Google Drive dengan **akun yang sama** seperti di desktop.
5. Biarkan tab Colab terbuka. Worker memproses job satu per satu; desktop memantau, mengunduh, dan memverifikasi hasil ke Folder Hasil.

Semua pengaturan berasal dari desktop. Notebook tidak menyediakan unggahan file atau membuat folder antrean baru. Setiap input dibatasi **100 MB (104.857.600 byte)** dan **60 detik**; batas diperiksa kembali oleh worker. NanoVSR melakukan upscale 4×, dan RIFE mendukung Target FPS yang lebih tinggi hingga 60 fps. Mute menghilangkan audio bila diaktifkan.

## Folder desktop dan pemulihan

Notebook mewajibkan `MyDrive/XIX-Upscaler/desktop-marker.json` dengan format versi 1 dan identitas instalasi desktop yang valid, serta folder `MyDrive/XIX-Upscaler/jobs` yang sudah ada. Desktop membuat keduanya dan seluruh file pertukaran sebelum menerbitkan job.

Jika muncul **Akun Drive tidak cocok**, pastikan mount memakai akun yang sama seperti desktop, tekan Start di desktop, tunggu upload selesai, lalu jalankan Run all kembali. Marker rusak atau tidak sesuai juga menghentikan notebook; jangan membuat atau mengedit marker dan folder job sendiri. Notebook memeriksa penanda desktop yang ada, bukan membandingkan alamat email akun.

Jika runtime terputus, jalankan **Run all** lagi; checkpoint yang valid dipakai untuk melanjutkan. **Pause** berlaku setelah checkpoint aman dan dapat dibatalkan sebelum semua video mengonfirmasi jeda. Saat dilanjutkan, desktop membuka notebook kembali agar pengguna dapat menjalankan **Run all**. **Stop** membatalkan job yang belum selesai. Menutup aplikasi tidak membatalkan pekerjaan; desktop yang mendukung pemulihan melanjutkan pemantauan ketika dibuka kembali. Ringkasan Colab membedakan job selesai, dijeda, dibatalkan, gagal, dan dilewati.

Dengan **Simpan file kerja di Drive** mati, desktop memindahkan folder job ke Trash hanya setelah hasil lokal terverifikasi. Input lokal dan hasil lokal tidak dihapus otomatis.

## Paket terverifikasi

Notebook memasang worker XIX yang versinya dikunci dan memverifikasi ukuran serta SHA-256 paket sebelum menjalankannya. Model NanoVSR dan RIFE diunduh dari sumber resminya dan juga diverifikasi.

- Worker: `0.2.0`; manifest pekerjaan: versi `2`, protokol `drive-slots-v1`.
- [Commit artefak tetap](https://github.com/mfahryf/xix-upscaler-colab/commit/0c8647e5b437cd3cbc3cbe5723971afb701fb20f).
- Wheel: `xix_colab_worker-0.2.0-py3-none-any.whl`, **38.911 byte**.
- SHA-256: `f2eb63c30f6639874ee1771d154b72480609b4664f49f18c271bbaa22eeee117`.
- Dibangun dengan `setuptools==83.0.0` dan `SOURCE_DATE_EPOCH=1788307200`. Rincian ada di [worker-release.json](worker-release.json).

## Kecepatan dan laporan waktu

Penyusunan MP4 memakai H.264 (`libx264`, preset `veryfast`, CRF 18, 2 thread untuk membatasi RAM). Ukuran frame, FPS, dan pilihan suara tidak diubah oleh pembaruan ini. Pengaturan kompresi lebih cepat dapat mengubah ukuran file dan detail gambar; ini bukan janji kelulusan penilaian Adobe Stock.

Setiap percobaan pekerjaan menampilkan `[COLAB-PERF]` dan menyimpan `work/performance.json`: waktu penyalinan input, pemuatan model, pembacaan video, interpolasi, upscale, penulisan MP4, checkpoint, penggabungan, audio, dan verifikasi hasil. Laporan tetap dibuat untuk pekerjaan gagal atau dijeda selama proses worker masih berjalan; penghentian paksa runtime tidak dapat dijamin. Jika Drive tidak dapat ditulisi, ringkasan tetap ada di keluaran notebook. Aktifkan **Simpan file kerja di Drive** di desktop bila ingin mempertahankan laporan bersama job selesai.

Waktu dihitung sejak worker menerima job, bukan sejak Start desktop. Waktu pemasangan notebook dan upload desktop tidak termasuk. Waktu penulisan MP4 mencakup menunggu encoder; encoder dapat bekerja bersamaan dengan AI. Nama GPU dan penggunaan memori dicatat bila tersedia, tetapi bukan persentase aktivitas GPU. Angka puncak memori mencakup umur proses worker, bukan hanya job terakhir.

Uji lokal 24 frame dari hasil 5120×2880/30 FPS: pengukuran awal preset lama `medium` 23,189 detik, `veryfast` 6,057 detik; pengulangan berikutnya 25,684 dan 23,659 detik. Beban komputer membuat waktu bervariasi, jadi angka percepatan awal tidak dapat digeneralisasi. SSIM terhadap video sumber uji masing-masing 0,998827 dan 0,998488, dengan hasil berkas identik pada pengulangan preset yang sama. Ini hanya uji kompresi cuplikan 0,8 detik tanpa inferensi AI, bukan perkiraan waktu keseluruhan di Colab. Buka ulang notebook resmi dan Run all untuk memasang paket terbaru; checkpoint lama yang valid tetap digunakan.

Jangan mengubah URL paket atau checksum. Jika verifikasi gagal, buka notebook resmi yang sesuai dari desktop. Notebook ini tidak memproses manifest lama versi 1.

Repo ini hanya memuat file distribusi Colab. Source aplikasi desktop XIX-Upscaler tidak disimpan di sini.

Google Colab tidak menjamin ketersediaan GPU, jenis GPU, kuota, atau durasi runtime.

Rilis ini sudah melalui pengujian otomatis lokal dan pemasangan wheel pada lingkungan Python bersih. Login Google, transfer Drive, dan inferensi GPU Colab nyata masih memerlukan pengujian manual pemilik proyek sebelum dinyatakan siap untuk penggunaan umum.
