# OUTLINE PROPOSAL SKRIPSI (v02)

**Program Studi Teknik Informatika — Universitas Islam Nahdlatul Ulama (UNISNU) Jepara**

| Item | Isi |
|---|---|
| Nama | Ari Riyanto |
| NIM | 231240001464 |
| Tahun | 2026 |
| Status | Revisi (v01 → v02) — diselaraskan dengan peta jalan riset berbasis data |

---

## Judul Penelitian

> **Implementasi Retrieval-Augmented Generation (RAG) pada Local Large Language Model (LLM) untuk Akurasi dan Keamanan Sistem Tanya Jawab Regulasi Perusahaan**

---

## 1. Latar Belakang (Poinnya)

1. Akses informasi yang cepat dan akurat terhadap regulasi internal korporat, dokumen tata kelola, dan kebijakan perusahaan merupakan kebutuhan krusial bagi efisiensi operasional organisasi. Implementasi kecerdasan buatan, khususnya *Large Language Model* (LLM), mampu memfasilitasi pencarian informasi tersebut melalui asisten virtual interaktif.
2. Namun, adopsi layanan LLM publik berbasis komputasi awan (*Cloud AI*) menghadirkan kerentanan **eksfiltrasi data** — dokumen rahasia perusahaan berpotensi terekspos ke internet publik. Selain itu, model Local LLM dasar **tidak memiliki pengetahuan kontekstual** mengenai regulasi spesifik sebuah perusahaan, sehingga sistem rentan terhadap fenomena **"halusinasi"**.
3. Skripsi ini mengusulkan integrasi metode **Retrieval-Augmented Generation (RAG)** pada ekosistem **Local Large Language Model (LLM)** yang berjalan di dalam jaringan lokal perusahaan. Melalui arsitektur ini, perusahaan dapat memiliki sistem tanya jawab cerdas yang beroperasi secara luring penuh untuk menjamin kedaulatan data, sekaligus mempertahankan akurasi jawaban melalui mekanisme injeksi konteks dari dokumen regulasi berformat PDF.
4. **Posisi riset:** penelitian ini dikerangkakan sebagai **riset berbasis data**, bukan sekadar "membangun aplikasi" — sistem RAG diposisikan sebagai *wrapper* yang diuji secara kuantitatif melalui **perbandingan konfigurasi** (retrieval/embedding/chunking) dengan metrik akurasi, keamanan, dan efisiensi. Data uji memakai dataset publik **ID_Regulations** (>250.000 PDF peraturan Indonesia, Apache-2.0) beserta *gold set* tanya-jawab manual dan dokumen sintetis ber-*canary token*. Skala *on-premise enterprise* dibahas sebagai arah penerapan; pembuktian eksperimen dilakukan pada perangkat lokal yang tersedia.

## 2. Rumusan Masalah

1. **Akurasi:** Seberapa besar peningkatan akurasi jawaban sistem tanya jawab regulasi perusahaan ketika Local LLM diperkuat mekanisme **RAG**, dibandingkan Local LLM **tanpa RAG** (*closed-book baseline*), diukur dengan *Exact Match*/F1 dan RAGAS (*faithfulness*, *answer relevancy*, *context precision/recall*)?
2. **Konfigurasi optimal:** Konfigurasi retrieval/embedding mana (ukuran *chunk*/*overlap*, model embedding, strategi retrieval) yang menghasilkan akurasi terbaik, diukur dengan *Recall@k*, *MRR*, dan *nDCG@k*?
3. **Keamanan:** Seberapa tahan sistem RAG lokal terhadap **injeksi prompt** (langsung dan tidak langsung) dan **kebocoran data** (*canary token leakage*), serta berapa *refusal rate* dan tingkat *egress* jaringan yang terukur?
4. **Efisiensi:** Bagaimana *trade-off* antara akurasi, **latensi respons**, **token/detik**, dan **puncak VRAM** pada konfigurasi terbaik di perangkat lokal yang tersedia?

## 3. Tujuan Penelitian

1. Mengukur pengaruh RAG terhadap akurasi Local LLM dibandingkan *closed-book baseline* (RM1).
2. Menentukan konfigurasi retrieval/embedding yang paling akurat untuk dokumen regulasi berbahasa Indonesia (RM2).
3. Mengukur ketahanan sistem RAG lokal terhadap injeksi prompt dan kebocoran data secara kuantitatif (RM3).
4. Menganalisis *trade-off* akurasi–keamanan–efisiensi (latensi/VRAM) antar konfigurasi (RM4).

## 4. Sumber Data

| Dataset | Peran | Jumlah | Lisensi |
|---|---|---|---|
| **ID_Regulations** (Hugging Face) | Korpus utama uji akurasi & evaluasi | Full: >250.000 PDF; subset uji: 1.000 PDF | Apache-2.0 |
| *Gold set* QA manual (`eval/gold_qa.csv`) | Data uji RQ1–RQ2 | ±50–100 pasang pertanyaan–jawaban | Dibuat sendiri |
| Dokumen sintetis ber-*canary token* (`src/make_canary_docs.py`) | Data uji RQ3 | ±50 dokumen | Dibuat sendiri |

## 5. Metode dan Rancangan Eksperimen (Ringkas)

- **Pipeline:** parsing PDF (`pypdf`) → *chunking* (RecursiveCharacterTextSplitter) → embedding lokal (`BAAI/bge-m3`) → *vector store* lokal (ChromaDB) → retrieval top-k → prompt berkonteks → **Local LLM** (Ollama, mis. `llama3.1:8b-instruct-q4_K_M`).
- **Variabel eksperimen (RQ2):** ukuran *chunk*/*overlap*, model embedding, dan strategi retrieval.
- **Perbandingan utama (RQ1):** RAG aktif vs LLM *closed-book* (tanpa retrieval).
- **Metrik:** RAGAS (faithfulness, answer relevancy, context precision/recall), Exact Match/F1, Recall@k, MRR, nDCG@k (akurasi); keberhasilan injeksi (%), *canary leak rate* (%), *refusal rate* (%), *egress* 0% (keamanan); latensi, TTFT, token/s, puncak VRAM/RAM (efisiensi).
- **Alur notebook:** `00_cek_lingkungan` → `01_unduh_dataset` → `02_indexing` → `03_evaluasi_akurasi` → `04_keamanan` → `05_efisiensi`.
- **Hipotesis awal (diuji di BAB IV):** H1 RAG meningkatkan *faithfulness*/akurasi; H2 retrieval yang lebih baik mengungguli *dense-only* pada istilah hukum; H3 kebocoran data ke luar jaringan 0% dan ketahanan injeksi lebih baik daripada API publik; H4 ada *trade-off* akurasi vs latensi/VRAM.

## 6. Manfaat Penelitian

- **Teoritis:** menambah bukti empiris perbandingan strategi RAG pada domain regulasi berbahasa Indonesia dan dimensi keamanan *on-premise* yang jarang diuji kuantitatif.
- **Praktis:** memberikan rekomendasi konfigurasi (model embedding, ukuran *chunk*, strategi retrieval) dan kerangka pengujian keamanan untuk sistem tanya jawab regulasi perusahaan yang berjalan luring.

## 7. Pustaka Utama

1. Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., … Kiela, D. (2020). Retrieval-augmented generation for knowledge-intensive NLP tasks. *Advances in Neural Information Processing Systems*, 33, 9459–9474.
2. Touvron, H., Lavril, T., Izacard, G., Martinet, X., Lachaux, M.-A., Lacroix, T., … Lample, G. (2023). LLaMA: Open and efficient foundation language models. arXiv:2302.13971. https://arxiv.org/abs/2302.13971
3. ⚠️ Rahman, A. F., Novaliendry, D., Hendriyani, Y., & Budayawan, K. (2026). Implementasi sistem retrieval augmented generation (RAG) studi kasus: Question answering dari dokumen PDF. *Jurnal Ilmiah Sain dan Teknologi*, 4, 246–259. — **wajib verifikasi** (kredibilitas jurnal + DOI).
4. Salman, M. D., Rahmaddeni, Nasution, T., & Susanti (2026). Implementation of retrieval-augmented generation method on large language model for development of campus service and information chatbot. *INOVTEK Polbeng — Seri Informatika*, 11(1), 298–309. https://doi.org/10.35314/3y9hy151
5. Lijaya, H., Ho, P., & Santoso, H. (2025). Comparative analysis of RAG-based open-source LLMs for Indonesian banking customer service optimization using simulated data. *Jurnal SISFOKOM*, 14(3), 330–341. https://doi.org/10.32736/sisfokom.v14i3.2383
6. Murtiyoso, M., Tahyudin, I., & Berlilana, B. (2025). A systematic review of retrieval-augmented generation for enhancing domain-specific knowledge in large language models. *Sinkron: Jurnal dan Penelitian Teknik Informatika*, 9(2), 969–977. https://doi.org/10.33395/sinkron.v9i2.14824
7. Hajar, M. R., Utami, E., & Hendi Muhammad, A. (2025). A systematic literature review of retrieval-augmented generation: Methods, applications, and future research directions. *Journal of Applied Computer Science and Technology (JACOST)*, 6(2), 115–128. https://doi.org/10.52158/jacost.v6i2.1170
8. Chen, L.-C., Pardeshi, M. S., Liao, Y.-X., & Pai, K.-C. (2025). Application of retrieval-augmented generation for interactive industrial knowledge management via a large language model. *Computer Standards & Interfaces*, 94, 103995. https://doi.org/10.1016/j.csi.2025.103995
9. Rangan, K., & Yin, Y. (2024). A fine-tuning enhanced RAG system with quantized influence measure as AI judge. *Scientific Reports*, 14, 27446. https://doi.org/10.1038/s41598-024-79110-x
10. Gulyamov, S., dkk. (2026). Prompt injection attacks in large language models and AI agent systems: A comprehensive review of vulnerabilities, attack vectors, and defense mechanisms. *Information*, 17(1), 54. https://doi.org/10.3390/info17010054
11. Milani, A., Franzoni, V., & Florindi, E. (2026). Indirect prompt injection in large language models. *Neural Computing and Applications*, 38(13). https://doi.org/10.1007/s00521-026-12266-x
12. Karakurt, E., & Akbulut, A. (2025). Retrieval-augmented generation (RAG) and large language models (LLMs) for enterprise knowledge management and document automation: A systematic literature review. *Applied Sciences*, 16(1), 368. https://doi.org/10.3390/app16010368

> **Catatan integritas:** seluruh sitasi berstatus *terduga* sampai dibuka sumber primernya (prosedur verifikasi: cari judul persis, cek DOI di doi.org/Crossref, baca abstrak + metode, simpan ke Zotero/Mendeley). Tabel penelitian terdahulu (state of the art): **`draft/tabel_penelitian_terdahulu.md`**.

---

Jepara, ____________
Pengusul,



**Ari Riyanto**
231240001464