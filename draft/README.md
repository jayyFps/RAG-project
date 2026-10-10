# Folder `draft/`

Tempat menyimpan **draf tulisan** skripsi: proposal, bab, dan slide presentasi.
Simpan di sini agar riwayat perubahan tidak mengotori berkas final di akar repo.

## Aturan penamaan berkas

```
bab<N>_<topik>_v<XX>.md
bab<N>_<topik>_v<XX>.docx   # bila draf untuk pembimbing
slide_<acara>_v<XX>.pptx
```

Contoh:

```
bab1_pendahuluan_v01.md
bab2_tinjauan_pustaka_v03.md
bab3_metodologi_v02.md
slide_seminar_proposal_v01.pptx
```

## Aturan penulisan (selaras prinsip README)

- **Satu versi per perubahan**, jangan timpa (`v01` → `v02` → ...). Yang final diberi
  penanda `_final`.
- **Setiap angka & klaim** harus bisa dilacak ke notebook dan berkas di `eval/results/`.
- **Tulis ulang dengan bahasa sendiri.** AI boleh membantu menyusun, tetapi draf wajib
  diverifikasi dan ditulis ulang peneliti (lihat bagian *Etika Penelitian dan Penggunaan AI*
  di `README.md`).
- **Jangan menaruh data mentah atau berkas besar** di sini — gunakan `data/` atau Git LFS.

## Alur kerja yang disarankan

1. Tulis draf di `draft/` → minta masukan pembimbing.
2. Revisi → naikkan versi (`v02`, `v03`, ...).
3. Setelah disetujui → susun berkas final di akar repo / sesuai format kampus.
