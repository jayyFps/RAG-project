# DRAF BAB I — PENDAHULUAN

**Skripsi Program Studi Teknik Informatika — UNISNU Jepara**
**Penyusun:** Ari Riyanto (231240001464) | **Versi:** v1 (draf awal, belum disetujui pembimbing)
**Judul penelitian:** Implementasi Retrieval-Augmented Generation (RAG) pada Local Large Language Model (LLM) untuk Akurasi dan Keamanan Sistem Tanya Jawab Regulasi Perusahaan

> **Format sub-bab** mengikuti Pedoman Penulisan Skripsi/Tugas Akhir/Tesis UNISNU Jepara (Peraturan Rektor No. 57/PR/UNISNU/V/2023, Lampiran X — contoh Daftar Isi): **1.1 Latar Belakang Masalah; 1.2 Rumusan Masalah; 1.3 Tujuan Penelitian; 1.4 Manfaat Penelitian.**
> (Catatan: pedoman tersebut **tidak** mencantumkan sub-bab Batasan Masalah maupun Sistematika Penulisan di BAB I — tanyakan ke pembimbing bila ingin menambahkannya.)
>
> **Tanda `[ISI: ...]` = placeholder** yang wajib dilengkapi/verifikasi sendiri. Sitasi memakai gaya APA sejalan dengan contoh panduan.

---

## 1.1 Latar Belakang Masalah

Akses informasi yang cepat, akurat, dan mutakhir terhadap regulasi internal korporat — mencakup dokumen tata kelola, kebijakan perusahaan, hingga peraturan perundang-undangan — merupakan kebutuhan krusial bagi efisiensi operasional organisasi. Besarnya volume dokumen yang terus bertambah membuat pencarian manual berbasis nama berkas menjadi tidak efisien; organisasi membutuhkan kanal tanya jawab yang mampu menjawab secara langsung: *"apa landasan hukum kewajiban pemberian THR bagi pekerja swasta?"* atau *"bagaimana batas waktu kerja lembur maksimal dalam sehari?"* Pengembangan *Large Language Model* (LLM) membuka peluang untuk mewujudkan asisten virtual yang memahami pertanyaan berbahasa alami tersebut (Touvron et al., 2023).

Namun, adopsi layanan LLM publik berbasis komputasi awan (*Cloud AI*) menghadirkan kerentanan serius berupa **eksfiltrasi data**. Dokumen rahasia perusahaan — kebijakan internal, data karyawan, hingga regulasi yang belum dipublikasikan — berpotensi terekspos ke pihak luar ketika dikirim ke layanan pihak ketiga melalui koneksi internet [ISI: tambahkan kasus/riset yang mendukung, mis. laporan insiden kebocoran data pada layanan AI; sertakan tautan terverifikasi]. Guna menjamin kedaulatan data, solusi yang lazim dipertimbangkan adalah pemanfaatan **Local LLM** (model yang dijalankan di infrastruktur sendiri) sehingga seluruh proses inferensi berada di dalam jaringan lokal perusahaan.

Persoalannya, Local LLM yang hanya mengandalkan pengetahuan bawaan model tidak memiliki pengetahuan kontekstual mengenai regulasi spesifik sebuah perusahaan. Tanpa dokumen sebagai sumber rujukan, model rentan menghasilkan jawaban yang tampak meyakinkan tetapi keliru — fenomena yang lazim disebut **halusinasi**. Keterbatasan memori parametrik ini telah menjadi perhatian sejak arsitektur *retrieval-augmented generation* diperkenalkan sebagai cara menghubungkan model bahasa dengan sumber pengetahuan eksternal yang dapat dicari (Lewis et al., 2020).

Untuk menjawab persoalan tersebut, pendekatan **Retrieval-Augmented Generation (RAG)** mengombinasikan *retriever* (pencari dokumen relevan dari korpus) dengan *generator* (LLM). Sebelum menjawab, sistem mengambil potongan dokumen yang relevan lalu menyisipkannya ke dalam prompt, sehingga jawaban dapat ditelusuri kembali ke pasal/klausul sumbernya. Penerapan RAG telah terbukti membantu pada domain layanan publik (Salman et al., 2026) dan layanan perbankan berbahasa Indonesia (Lijaya et al., 2025). Akan tetapi, studi-studi tersebut belum memberikan panduan baku mengenai konfigurasi retrieval untuk **korpus regulasi berbahasa Indonesia** — misalnya pilihan strategi retrieval (*dense*, *BM25*, atau *hybrid*), model embedding, dan ukuran *chunk* dokumen — padahal pilihan tersebut menentukan kualitas konteks yang didapat *retriever* (Chen et al., 2025).

Selain akurasi, dimensi **keamanan** menjadi pertimbangan yang tidak kalah penting. Sistem RAG yang mengambil dokumen dari korpus rentan terhadap **injeksi prompt** — baik serangan langsung ke kanal pertanyaan maupun serangan tidak langsung yang disisipkan pada isi dokumen (Gulyamov et al., 2026; Milani et al., 2026). Tinjauan literatur juga menunjukkan bahwa sebagian besar studi RAG untuk manajemen pengetahuan perusahaan berjalan pada platform *cloud* (Karakurt & Akbulut, 2025), sehingga bukti empiris tentang ketahanan RAG yang sepenuhnya *on-premise* terhadap kebocoran data masih terbatas. Di sisi lain, lingkungan pengujian dengan sumber daya terbatas (misalnya GPU konsumen 6 GB) menuntut keseimbangan antara akurasi, keamanan, dan kebutuhan komputasi.

Berdasarkan uraian di atas, penelitian ini dikerangkakan sebagai **riset berbasis data** yang menguji secara kuantitatif pengaruh mekanisme RAG terhadap akurasi dan keamanan sistem tanya jawab regulasi perusahaan pada Local LLM. Pengujian memanfaatkan dataset publik **ID_Regulations** (korpus peraturan Indonesia, lisensi Apache-2.0) beserta *gold set* pertanyaan-jawaban yang disusun manual dan dokumen sintetis ber-*canary token* untuk pengujian keamanan. Keluaran yang diharapkan adalah konfigurasi RAG yang direkomendasikan beserta kerangka evaluasi keamanan yang dapat diterapkan pada sistem tanya jawab regulasi perusahaan yang beroperasi secara luring.

## 1.2 Rumusan Masalah

Berdasarkan latar belakang masalah tersebut, rumusan masalah dalam penelitian ini adalah sebagai berikut.

1. Seberapa besar peningkatan akurasi jawaban sistem tanya jawab regulasi perusahaan ketika Local LLM diperkuat mekanisme RAG dibandingkan Local LLM tanpa RAG (*closed-book baseline*), diukur dengan *Exact Match*/F1 dan metrik RAGAS (*faithfulness*, *answer relevancy*, *context precision/recall*)?
2. Konfigurasi retrieval/embedding mana — meliputi ukuran *chunk*/*overlap*, model embedding, dan strategi retrieval — yang menghasilkan akurasi terbaik pada dokumen regulasi berbahasa Indonesia, diukur dengan *Recall@k*, *MRR*, dan *nDCG@k*?
3. Seberapa tahan sistem RAG lokal terhadap **injeksi prompt** (langsung dan tidak langsung) dan **kebocoran data** (*canary token leakage*), serta berapa *refusal rate* dan tingkat *egress* jaringan yang terukur?
4. Bagaimana *trade-off* antara akurasi, keamanan, **latensi respons**, **token/detik**, dan **puncak VRAM** pada konfigurasi terbaik di perangkat lokal yang tersedia?

## 1.3 Tujuan Penelitian

Sesuai dengan rumusan masalah, tujuan penelitian ini adalah sebagai berikut.

1. Mengukur pengaruh mekanisme RAG terhadap akurasi jawaban Local LLM dibandingkan Local LLM tanpa RAG pada dokumen regulasi perusahaan.
2. Menentukan konfigurasi retrieval/embedding (ukuran *chunk*/*overlap*, model embedding, strategi retrieval) yang paling akurat untuk dokumen regulasi berbahasa Indonesia.
3. Mengukur ketahanan sistem RAG lokal terhadap injeksi prompt dan kebocoran data secara kuantitatif, termasuk *refusal rate* dan *egress* jaringan.
4. Menganalisis *trade-off* antara akurasi, keamanan, dan efisiensi (latensi, token/detik, puncak VRAM) antar konfigurasi untuk memberikan rekomendasi penerapan di perangkat lokal yang tersedia.

## 1.4 Manfaat Penelitian

### 1.4.1 Manfaat Teoritis

1. Menambah bukti empiris tentang pengaruh RAG terhadap akurasi Local LLM pada ranah dokumen regulasi berbahasa Indonesia yang masih jarang dilaporkan.
2. Memperkaya kajian perbandingan strategi retrieval dan model embedding untuk korpus hukum/regulasi berbahasa Indonesia.
3. Memberikan kerangka pengujian keamanan RAG lokal (injeksi prompt, kebocoran *canary token*, *refusal rate*) yang dapat direplikasi pada studi serupa.

### 1.4.2 Manfaat Praktis

1. Bagi perusahaan/organisasi: memberikan rekomendasi konfigurasi RAG dan prosedur evaluasi keamanan untuk membangun sistem tanya jawab regulasi yang berjalan *on-premise* tanpa mengirim data keluar jaringan.
2. Bagi pengembang: menyediakan alur kerja (pipeline) dan tolok ukur (*benchmark*) berbasis dataset publik yang dapat digunakan ulang untuk menguji sistem RAG berbahasa Indonesia.
3. Bagi peneliti lain: menyediakan *gold set* pertanyaan-jawaban regulasi dan dokumen uji *canary* sebagai bahan pembanding penelitian berikutnya.

---

## Catatan Draf (wajib diselesaikan sebelum diajukan)

- [ ] **Semua sitasi di BAB I** diuji ke sumber primer (prosedur verifikasi: README §6.7), terutama yang berstatus ⚠️ (mis. Rahman et al., 2026 — belum dipakai di narasi ini).
- [ ] **Placeholder `[ISI: ...]`** dilengkapi dengan data/rujukan terverifikasi (kasus kebocoran data Cloud AI, data beban kerja dokumen regulasi bila ada).
- [ ] Judul, rumusan masalah, tujuan — **padankan satu-satu** dengan pertanyaan penelitian (RQ) BAB II; setiap RQ sudah punya metrik di README Langkah 5.
- [ ] Konfirmasi ke pembimbing: apakah perlu sub-bab **Batasan Masalah** / **Sistematika Penulisan** di luar 1.1–1.4 pedoman.
- [ ] Simpan PDF semua rujukan ke Zotero/Mendeley; salin daftar pustaka dari `draft/outline_proposal_v02.md` (12 item) bila BAB I ini dipakai untuk proposal semprot.