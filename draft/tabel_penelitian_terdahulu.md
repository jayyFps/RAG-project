# Tabel Penelitian Terdahulu (State of the Art)

**Skripsi:** Implementasi RAG pada Local LLM untuk Akurasi dan Keamanan Sistem Tanya Jawab Regulasi Perusahaan
**Penyusun:** Ari Riyanto — 231240001464 | **Versi:** v1 (draft)
**Basis:** 12 pustaka utama pada `draft/outline_proposal_v02.md` (± 10 di antaranya jurnal SINTA 3+/Scopus).

> **Kolom:** Peneliti (Tahun) — Venue | Dataset | Metode / Fokus | Metrik | Hasil utama | Celah penelitian | Status.
> **Status:** ✅ = rujukan sudah diverifikasi ke sumber primer (judul/penulis/venue/DOI cocok). ⚠️ = kandidat, **wajib diverifikasi** (lihat prosedur verifikasi di README bagian 6.7).
> **Penting:** sel-sel berlabel *"(baca abstrak)"* menunggu Anda membaca abstrak & metode dari sumber primer — **jangan dikutip detailnya** sebelum diverifikasi.

| # | Peneliti (Tahun) — Venue | Dataset | Metode / Fokus | Metrik | Hasil utama | Celah | Status |
|---|---|---|---|---|---|---|---|
| 1 | Lewis dkk. (2020) — *NeurIPS 2020* | Wikipedia (dense index) + Natural Questions, WebQuestions, TriviaQA | RAG: *parametric* (seq2seq) + *non-parametric memory* (DPR); varian RAG-Sequence & RAG-Token | EM, akurasi open-domain QA | Menetapkan fondasi arsitektur RAG; SOTA pada 3 tugas open-domain QA | Model besar, bukan on-premise; dimensi keamanan belum dibahas | ✅ |
| 2 | Touvron dkk. (2023) — *arXiv:2302.13971* (LLaMA) | Korpus pretraining: CommonCrawl, C4, GitHub, Wikipedia, dll. (±1,4T token) | Model bahasa autoregressive **open-weight** 7B–65B | Perplexity; MMLU, HellaSwag, dll. | LLaMA-13B mengungguli GPT-3 (175B) pada mayoritas benchmark; open-weight memungkinkan **deploy lokal** | Model dasar hanya berbekal pengetahuan umum → **halusinasi** bila ditanya dokumen internal perusahaan | ✅ |
| 3 | ⚠️ Rahman dkk. (2026) — *Jurnal Ilmiah Sain dan Teknologi* | Dokumen PDF (umum) | RAG untuk *question answering* dari dokumen PDF | Akurasi QA *(baca abstrak)* | Implementasi RAG memudahkan QA dari PDF *(verifikasi)* | Belum diuji pada korpus regulasi berbahasa Indonesia + keamanan; kredibilitas jurnal belum terkonfirmasi | ⚠️ |
| 4 | Salman dkk. (2026) — *INOVTEK Polbeng — Seri Informatika* (SINTA 3) | Data layanan & informasi kampus | RAG pada LLM untuk chatbot layanan kampus | Kesesuaian/akurasi jawaban *(baca abstrak)* | RAG diimplementasikan pada chatbot LLM agar jawaban bersumber dokumen layanan kampus | Domain layanan kampus, bukan regulasi perusahaan; keamanan tidak diukur | ✅ |
| 5 | Lijaya dkk. (2025) — *Jurnal SISFOKOM* (SINTA 2/Scopus) | Data simulasi nasabah bank | **Comparative analysis** RAG pada LLM *open-source* untuk layanan nasabah Indonesia | Kualitas jawaban antar-LLM *(baca abstrak)* | Membandingkan kinerja LLM open-source + RAG pada konteks perbankan Indonesia | Data simulasi, bukan data produksi; aspek keamanan/injeksi belum diuji | ✅ |
| 6 | Murtiyoso dkk. (2025) — *Sinkron* (SINTA 3) | Tinjauan sistematis (berbagai studi RAG) | SLR: peningkatan pengetahuan domain-spesifik melalui RAG | — (survei) | Mensintesis metode & arsitektur RAG untuk knowledge domain-spesifik | Survei; tidak ada eksperimen kuantitatif akurasi vs keamanan | ✅ |
| 7 | Hajar dkk. (2025) — *JACOST* (SINTA 2) | Tinjauan sistematis | SLR: metode, aplikasi, dan arah riset RAG | — (survei) | Memetakan metode & aplikasi RAG + arah riset mendatang | Survei; belum ada eksperimen kuantitatif pipeline lokal dengan variabel yang dibandingkan | ✅ |
| 8 | Chen dkk. (2025) — *Computer Standards & Interfaces* (Scopus Q1) | Dokumen pengetahuan industri (manual peralatan / FAQ produksi) | RAG + LLM untuk **manajemen pengetahuan industri** interaktif | Akurasi/kualitas jawaban teknis *(baca abstrak)* | Integrasi RAG membantu jawaban teknis bersumber dokumen industri | Domain manufaktur; deployment on-premise & keamanan belum dibahas | ✅ |
| 9 | Rangan & Yin (2024) — *Scientific Reports* (Scopus Q1) | Korpus QA tertentu *(baca abstrak)* | RAG + **fine-tuning** + *quantized influence measure* sebagai AI judge | Akurasi/kesesuaian jawaban | Enhancement fine-tuning meningkatkan kualitas jawaban RAG dengan pengukur berbiaya rendah | Fokus akurasi; keamanan data tidak diukur | ✅ |
| 10 | Gulyamov dkk. (2026) — *Information* (Scopus) | Studi ekstensif kasus serangan LLM | **Review komprehensif prompt injection** pada LLM & AI agent (vektor serangan, pertahanan) | Taksonomi risiko | Mengkategorikan serangan prompt injection & strategi pertahanannya | Review, bukan pengujian kuantitatif pada sistem RAG lokal | ✅ |
| 11 | Milani dkk. (2026) — *Neural Computing and Applications* (Scopus Q1) | Dataset serangan injeksi tidak langsung *(baca abstrak)* | Studi **indirect prompt injection** pada LLM | Tingkat keberhasilan injeksi *(baca abstrak)* | Indirect prompt injection merupakan ancaman sistematis (mis. menyusup lewat dokumen yang di-retrieve) | Belum dikaitkan dengan pertahanan RAG on-premise (grounding, refusal rate) | ✅ |
| 12 | Karakurt & Akbulut (2025) — *Applied Sciences* (Scopus) | Tinjauan literatur enterprise KM | SLR: RAG + LLM untuk manajemen pengetahuan & otomasi dokumen perusahaan | — (survei) | Memetakan pola penerapan RAG enterprise (pipeline, tools, platform) | Survei; mayoritas platform cloud; akurasi vs keamanan belum diukur empiris | ✅ |

---

## Sintesis Celah Penelitian (→ RQ skripsi)

Dari 12 baris di atas, gap penelitian dipertajam menjadi empat celah yang **dapat diuji**:

1. **Akurasi (RQ1):** mayoritas studi berfokus pada *perancangan* sistem RAG; sedikit yang melaporkan **perbandingan kuantitatif RAG vs LLM tanpa RAG** pada dokumen regulasi berbahasa Indonesia (baris 4, 5 memberi konteks lokal; baris 1, 8 fondasi/metode).
2. **Konfigurasi retrieval (RQ2):** belum ada perbandingan **sistematis** strategi retrieval/embedding dan ukuran chunk untuk korpus **regulasi berbahasa Indonesia** (baris 5 bandingkan antar-LLM, bukan antar-retrieval).
3. **Keamanan (RQ3):** studi keamanan (baris 10, 11) berdiri sendiri — belum dievaluasi bersama RAG **lokal/on-premise** dengan metrik *canary leakage*, *refusal rate*, dan *egress* (baris 1, 8, 12 tidak mengukur keamanan).
4. **Efisiensi (RQ4):** trade-off akurasi–keamanan–latensi/VRAM jarang dilaporkan bersamaan dalam satu studi lokal terbatas sumber daya (kebutuhan nyata RTX 2060 6 GB).

> Setiap baris di atas **harus** diverifikasi ke sumber primer sebelum masuk daftar pustaka narasi BAB II (prosedur: README §6.7 — cari judul persis, cek DOI di doi.org/Crossref, baca abstrak+metode, simpan PDF ke Zotero/Mendeley). Sel *"(baca abstrak)"* khususnya wajib diisi setelah membaca.