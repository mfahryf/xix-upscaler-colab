# XIX-Upscaler Video Colab (Experimental)

Mode ini memakai GPU Google Colab dan Google Drive milik pengguna. XIX tidak menyediakan server, GPU, penyimpanan, atau biaya komputasi. Notebook tidak memakai layanan perantara dan tidak berusaha mempertahankan sesi Colab secara paksa.

## Status tahap ini

Pembaruan kinerja: hasil tetap MP4/H.264 dengan skala, FPS, dan pilihan suara dari desktop. Penyusunan video memakai `libx264 veryfast`, CRF 18, dan dua thread agar penggunaan RAM tetap terbatas. Kompresi lebih cepat dapat mengubah ukuran file dan detail gambar; bukan jaminan video diterima Adobe Stock.

Worker menampilkan `[COLAB-PERF]` dan menyimpan `work/performance.json` untuk setiap percobaan job, termasuk percobaan gagal/jeda yang masih sempat diselesaikan worker. Laporan memisahkan waktu penyalinan input, pemuatan model, pembacaan frame, interpolasi, upscale, penulisan MP4, checkpoint, audio, dan penyimpanan/verifikasi hasil. Upload desktop serta pemasangan notebook sebelumnya tidak termasuk. Penggunaan memori GPU bukan persentase aktivitas GPU; nilai puncak berlaku sejak proses worker dimulai. Untuk mempertahankan laporan bersama job selesai, aktifkan **Simpan file kerja di Drive**.

Uji encoder tanpa AI pada input ZIP hasil desktop dapat diulang dengan `colab/worker/tools/benchmark_encoder.py <zip> --presets medium current` (atur `PYTHONPATH` ke `colab/worker/src`). Script membaca 24 frame pertama hasil 5K, membandingkan kompresi, memeriksa codec/FPS/resolusi, dan menghitung SSIM. Berkas uji sementara dibersihkan otomatis dan ZIP asli tidak diubah. Angka ini hanya pengukuran encoder lokal, bukan kecepatan GPU Colab atau penilaian kualitas model AI.

Worker video dan notebook **Run all** sudah tersedia untuk pengujian. Worker memakai NanoVSR-644k untuk upscale 4× atau Real-ESRGAN resmi untuk upscale native 2×/4×, serta RIFE untuk target FPS yang lebih tinggi. Worker memproses satu pekerjaan pada satu waktu dan menyimpan checkpoint agar pekerjaan dapat dilanjutkan setelah sesi terputus.

Integrasi desktop sudah tersambung: **Start** memeriksa video, menghubungkan akun bila perlu, mengunggah pekerjaan, dan membuka notebook resmi. **DRIVE** mengatur akun; **Pause/Stop** mengirim permintaan jeda/pembatalan. Desktop memantau progres, mengunduh hasil, dan memulihkan pekerjaan tersimpan ketika dibuka kembali. Pengujian lokal tidak menggantikan pengujian login Google dan GPU Colab nyata.

Folder pekerjaan dibuat oleh desktop, bukan dibuat manual oleh pengguna:

`MyDrive/XIX-Upscaler/jobs/<job-uuid>/`

Folder tersebut berisi manifest schema 2, input, dua file status pertukaran, kontrol, dan tempat hasil. Worker **0.2.0** mempertahankan identitas file yang dibuat desktop. Pilihan Mute, target FPS, model, dan skala berasal dari desktop—bukan dipilih lagi di notebook. Notebook menolak folder tanpa marker desktop yang valid, agar pekerjaan tidak diam-diam masuk ke akun lain.

## Menjalankan notebook

1. Di desktop pilih **Video (Colab Experimental)** untuk NanoVSR 4× atau **Video (Colab ESRGAN)** untuk Real-ESRGAN 2×/4×, lalu pilih video, Folder Hasil, FPS, serta MUTE; tekan **Start** dan selesaikan login Google bila diminta.
2. Tunggu unggahan selesai. Desktop membuka [XIX-Upscaler Colab](https://colab.research.google.com/github/mfahryf/xix-upscaler-colab/blob/main/XIX-Upscaler-Colab.ipynb).
3. Pilih runtime **GPU**.
4. Pilih **Runtime → Run all**.
5. Izinkan akses Google Drive saat diminta, menggunakan akun yang sama dengan desktop.
6. Biarkan tab Colab aktif sampai ringkasan antrean muncul.

Jika sebagian unggahan gagal, notebook tidak dibuka otomatis. Tombol **BUKA COLAB** tetap tersedia untuk pekerjaan yang sudah siap. Setelah putaran itu berakhir, **Start** mencoba kembali unggahan yang masih tersimpan.

Worker menulis hasil sebagai `output.mp4` di folder job. `status.json` berisi progres atau alasan kegagalan. Mode Mute mempertahankan video tanpa audio; bila Mute mati, audio sumber dipertahankan. Target interpolasi yang didukung maksimal 60 fps, termasuk 23.976, 29.97, dan 59.94 dengan nilai waktu yang presisi.

Desktop menyimpan hasil ke Folder Hasil setelah pemeriksaan ukuran, checksum, dan metadata video. Secara default hanya folder pekerjaan yang sudah selesai diverifikasi dipindahkan ke Sampah Drive. Aktifkan **Simpan file kerja di Drive** pada ADV bila ingin mempertahankannya. Input dan hasil lokal tidak dihapus otomatis.

Menutup desktop tidak membatalkan proses. Buka kembali untuk memulihkan pekerjaan; bila izin Google sudah tidak berlaku, hubungkan kembali lewat **DRIVE**. Akun yang berbeda tidak digunakan untuk mengunggah ulang pekerjaan lama. Tekan **Start** untuk mencoba kembali transfer tersimpan yang gagal. Pause menunggu titik aman dari worker dan dapat dibatalkan sebelum semua video mengonfirmasi jeda. Saat dilanjutkan, notebook dibuka kembali untuk **Run all**. Stop menyimpan permintaan pembatalan sebelum dikirim, dan tetap dapat dicoba kembali bila koneksi putus.

Jika runtime terputus, buka kembali notebook dan pilih **Run all**. Checkpoint yang sudah lengkap diverifikasi sebelum dipakai; potongan yang rusak tidak dianggap selesai.

## Batas penggunaan

- Google tidak menjamin GPU, jenis GPU, durasi sesi, atau kuota Colab.
- Input maksimal **100 MB** (104.857.600 byte) dan durasi maksimal **60 detik**, diperiksa desktop dan worker.
- Worker hanya memproses video yang manifest-nya cocok dengan versi worker.
- Inferensi GPU nyata harus diuji manual di Colab sebelum fitur dianggap siap untuk pengguna umum.
- Jangan mengubah URL paket, hash, atau cell notebook. Jika pemeriksaan paket gagal, hentikan proses dan gunakan notebook resmi yang cocok dengan versi aplikasi.

Tidak ada installer desktop yang dibangun pada tahap ini.
