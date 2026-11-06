# Neural Style Transfer  dari Nol — Kelompok 13

Implementasi Neural Style Transfer berbasis optimasi piksel menggunakan PyTorch dan VGG-19 pralatih. Proyek kelompok mata kuliah [isi nama mata kuliah], dengan metode **Vibe Coding**: seluruh kode dibangun lewat kolaborasi dengan LLM, tanpa menyalin dari tutorial.

## Anggota dan Peran

| Anggota | Peran |
|---|---|
| Yudis Akbar | Prompt Engineer & LLM Navigator (Ketua) |
| Faisal Surya Saputra | Data Pipeline & Preprocessing Engineer |
| Faza Farih Riyadi | Training Loop & Hyperparameter Tuning Engineer |
| Danang Setiyoadi | Lead Documentarian & Error Handler |

## Ringkasan Metode

- **Model:** VGG-19 pralatih ImageNet, dipakai hanya sebagai pengekstrak fitur (bobot dibekukan).
- **Content Loss:** MSE fitur di layer `conv4_2`.
- **Style Loss:** MSE Gram Matrix di layer `conv1_1`, `conv2_1`, `conv3_1`, `conv4_1`, `conv5_1`.
- **Total Variation Loss:** opsional, untuk meredam noise.
- **Optimasi:** yang dioptimasi adalah piksel gambar hasil (optimizer Adam), bukan bobot jaringan. "Fine-tuning" dilakukan lewat penyetelan hyperparameter.
- **Ditulis sendiri:** fungsi loss, Gram Matrix, pipeline data, dan loop optimasi.

## Isi Notebook

| Bagian | Isi |
|---|---|
| 1 | Setup dan konfigurasi (`Config`, seed, device) |
| 2 | Pemuatan dan pra-pemrosesan gambar; 2.5 dataset, augmentasi, DataLoader |
| 3 | Ekstraksi fitur VGG-19 |
| 4–5 | Content Loss, Gram Matrix, Style Loss |
| 6 | Total loss dan loop optimasi |
| 7 | Visualisasi dan ekspor hasil |
| 8 | Eksperimen hyperparameter (E0–E8) |
| 9 | Uji pada beberapa pasangan content–style |
| 10 | Kesimpulan |

Setiap bagian dilengkapi unit test.

## Dataset

Dataset ringan: 4 gambar content dan 3 gambar style. Sumber, lisensi, ukuran asli, dan pipeline pra-pemrosesan ada di [`dokumentasi_dataset.md`](dokumentasi_dataset.md).

Pipeline: RGB → Resize sisi pendek 256 → CenterCrop 256 → ToTensor [0, 1] → normalisasi ImageNet (di dalam model).

## Eksperimen

Setiap eksperimen mengubah satu parameter dari baseline (`style_weight = 1e5`, `lr = 0.01`, 300 iterasi, inisialisasi dari content, MaxPool).

| Kode | Parameter yang diubah |
|---|---|
| E1 | Jumlah iterasi 600 |
| E2 / E3 | `style_weight` 1e4 / 1e6 |
| E4 | `lr` 0.003 |
| E5 | AvgPool menggantikan MaxPool |
| E6 | Inisialisasi dari noise |
| E7 | Bobot layer style rendah-dominan |
| E8 | `tv_weight` 100 / 1000 / 10000 |

Konfigurasi TV yang dipilih: `tv_weight = 1000`. Analisis lengkap dan grafik ada di notebook (Bagian 8 dan 10).

## Cara Menjalankan (Google Colab)

1. Buka `nst_kelompok13.ipynb` di Google Colab.
2. Pilih runtime GPU: **Runtime → Change runtime type → GPU**.
3. Jalankan semua sel: **Runtime → Run all**.

Gambar akan diunduh otomatis dari URL yang tercantum di notebook. Estimasi waktu: sekitar 15 detik per optimasi 300 iterasi di GPU T4.

Dependensi: `torch`, `torchvision`, `numpy`, `Pillow`, `matplotlib`, `requests` (sudah tersedia di Colab).

## Dokumentasi Proyek

- [`dokumentasi_dataset.md`](dokumentasi_dataset.md): sumber dan pipeline dataset
- [`log_interaksi_llm.md`](log_interaksi_llm.md): log interaksi dengan LLM
- [`log_kerja.md`](log_kerja.md): log kerja anggota
- Tautan chat LLM: 

## Keterbatasan

- `style_weight = 1e5` disetel untuk satu gambar style dan belum tentu cocok untuk semua gambar style.
- Gambar berukuran 256×256 karena keterbatasan GPU Colab.
- Penilaian kualitas masih visual; belum ada metrik kuantitatif.

## Catatan Lisensi Gambar

Gambar dari Unsplash memakai Unsplash License. Dua gambar awal dari bioneers.org dan Pinterest lisensinya tidak diketahui dan dipakai hanya untuk keperluan akademik non-komersial.
