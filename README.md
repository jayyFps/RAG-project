# Implementasi Retrieval-Augmented Generation (RAG) pada Local Large Language Model (LLM) untuk Akurasi dan Keamanan Sistem Tanya Jawab Regulasi Perusahaan

> **Status dokumen:** *working README* — peta jalan riset skripsi. Setiap bagian boleh diperbarui seiring proses bimbingan.
> **Catatan integritas:** semua rujukan di dokumen ini berstatus **terduga** sampai Anda membuka berkas aslinya (lihat [Langkah 6](#langkah-6--studi-literatur-dan-tabel-penelitian-terdahulu-state-of-the-art)). Jangan salin sitasi dari AI tanpa verifikasi.

---

## Identitas Penelitian

| Item | Isi |
|---|---|
| Nama Mahasiswa | Ari Riyanto |
| NIM | 231240001464 |
| Program Studi | Teknik Informatika |
| Fakultas | Sains dan Teknologi — Universitas Islam Nahdlatul Ulama (UNISNU) Jepara |
| Dosen Pembimbing 1 | Pak Teguh |
| Dosen Pembimbing 2 | Pak Gentur |
| Judul Sementara | Implementasi Retrieval-Augmented Generation (RAG) pada Local Large Language Model (LLM) untuk Akurasi dan Keamanan Sistem Tanya Jawab Regulasi Perusahaan |
| Tahun | 2026 |

---

## Cara Membaca README Ini

README ini disusun mengikuti **urutan pengerjaan** riset, dari nol sampai siap proposal. Ikuti berurutan:

1. [Langkah 1 — Mencari Dataset Publik](#langkah-1--mencari-dataset-publik)
2. [Langkah 2 — Persiapan Lingkungan Pengembangan Lokal](#langkah-2--persiapan-lingkungan-pengembangan-lokal)
3. [Langkah 3 — Menentukan Topik Penelitian](#langkah-3--menentukan-topik-penelitian)
4. [Langkah 4 — Menentukan Judul Penelitian](#langkah-4--menentukan-judul-penelitian)
5. [Langkah 5 — Menentukan Pertanyaan Penelitian](#langkah-5--menentukan-pertanyaan-penelitian)
6. [Langkah 6 — Studi Literatur dan Tabel Penelitian Terdahulu (State of the Art)](#langkah-6--studi-literatur-dan-tabel-penelitian-terdahulu-state-of-the-art)

Setelah itu: [Checklist Sebelum Commit & Push](#checklist-sebelum-commit--push), [Etika Penelitian dan Penggunaan AI](#etika-penelitian-dan-penggunaan-ai-akademik), dan [Daftar Referensi](#daftar-referensi).

---

## Prinsip Kerja (Wajib Dipatuhi)

Diadaptasi dari *Buku Ajar Metodologi Penelitian* Prodi Teknik Informatika UNISNU Jepara:

- **Data mentah itu abadi.** Isi `data/raw/` tidak pernah diedit langsung. Semua pembersihan menghasilkan berkas baru di `data/processed/` **lewat kode**.
- **Ketertelusuran.** Setiap angka pada laporan harus bisa dilacak: notebook mana, data versi mana, tanggal commit berapa.
- **Setiap rujukan berstatus terduga** sampai artikel aslinya Anda temukan dan baca (risiko halusinasi sitasi AI).
- **AI adalah asisten, peneliti penanggung jawab.** Keluaran AI wajib diverifikasi: kode dijalankan, fakta ditelusuri ke sumber primer, draf ditulis ulang dengan bahasa sendiri.
- **Patuhi lisensi data & pelindungan data pribadi** (UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi). Jangan unggah data sensitif ke layanan AI pihak ketiga.

---

## Struktur Folder Proyek

```
skripsi-rag-regulasi/
|-- literatur/            # PDF jurnal, catatan ringkasan, tabel penelitian terdahulu
|-- data/
|   |-- raw/              # dataset/dokumen mentah, TIDAK PERNAH diubah
|   |-- processed/        # hasil parsing, chunking, embedding (dihasilkan kode)
|-- notebook/             # Jupyter: 00_cek_lingkungan, 01_unduh_dataset, 02_indexing, 03_evaluasi_akurasi, 04_keamanan, 05_efisiensi
|-- src/                  # kode produksi: ingest, retriever, generator, guardrail
|-- vectorstore/          # indeks vektor lokal (Chroma/FAISS) -- biasanya di-ignore Git
|-- model/                # model/prompt tersimpan, log eksperimen
|-- draft/                # draf proposal, bab skripsi, slide
|-- eval/                 # kumpulan pertanyaan uji, rubrik, hasil evaluasi
|-- README.md             # dokumen ini
|-- requirements.txt      # daftar pustaka Python + versinya
|-- environment.yml       # (opsional) definisi conda environment
`-- .gitignore            # berkas yang dikecualikan dari Git
```

Pola ini mengikuti Tabel 2.1 buku metopen. Tujuan praktisnya: ketika pembimbing bertanya "angka ini dari mana?", Anda cukup membuka notebook bernomor dan data yang dihasilkannya.

---

## Langkah 1 — Mencari Dataset Publik

### 1.1 Tiga Saringan Kelayakan Data

Sebuah kandidat data layak dipakai bila lolos tiga saringan:

1. **Ketersediaan legal** — benar-benar bisa diperoleh dan lisensinya jelas (boleh dipakai/didistribusikan?).
2. **Kebermaknaan** — jawabannya bermanfaat bagi pihak tertentu (mis. divisi kepatuhan perusahaan).
3. **Kelayakan teknis** — ukuran data, komputasi, dan waktu (1 semester) memadai.

### 1.2 Masalah Utama Topik "Regulasi Perusahaan"

Regulasi/internal policy perusahaan **jarang tersedia publik** karena bersifat rahasia. Ada tiga strategi yang lazim diterima penguji; pilih satu dan tuliskan alasannya di proposal:

| Strategi | Sumber data | Kelebihan | Risiko |
|---|---|---|---|
| **A. Dokumen perusahaan nyata** | SOP/kebijakan internal perusahaan mitra | Paling relevan, menguatkan sisi "keamanan data" | Perlu izin, NDA, wajib anonimisasi; sulit dibagi |
| **B. Korpus legal publik sebagai proksi** | Kontrak publik, regulasi OJK/UU, GDPR/EU AI Act | Bisa dibagikan & direplikasi, lisensi jelas | Dianggap "bukan regulasi perusahaan asli" |
| **C. Dokumen sintetis + gold set** | Regulasi perusahaan buatan sendiri | Kontrol penuh atas skenario uji & keamanan | Harus menjelaskan validitasnya |

> **Rekomendasi untuk skripsi:** pakai **Strategi B** sebagai korpus utama (agar dapat direplikasi dan diuji) **plus** sebagian kecil **Strategi C** untuk skenario keamanan (menyisipkan *canary token* / uji injeksi). Strategi A dijadikan studi kasus tambahan bila ada mitra.
>
> **✅ Keputusan (dikunci):** korpus utama = **ID_Regulations** (Strategi B), pertanyaan uji dibuat manual (lihat Langkah 5), skenario keamanan memakai dokumen sintetis ber-*canary token* (Strategi C).

### 1.3 Kandidat Dataset Publik

> **Saringan tambahan tugas ini:** dataset harus punya **minimal 1.000 baris/dokumen** agar eksperimen retrieval & evaluasi bermakna. Kolom "≥ 1.000?" menandai status tersebut (✅ = terpenuhi; ⚠️ = perlu dicek ulang).

| # | Dataset | Isi & ukuran | Sumber (tautan) | Lisensi | ≥ 1.000? |
|---|---|---|---|---|---|
| 1 | **ID_Regulations** ✅ **(DATASET DIPILIH)** | > 250.000 dokumen PDF peraturan Indonesia (hasil scrape) | https://huggingface.co/datasets/horelulus/ID_Regulations | Apache-2.0 | ✅ |
| 2 | **Indonesian_Legal_QA** | 8.078 pasang tanya-jawab hukum Indonesia (kasus nyata) | https://huggingface.co/datasets/Azzindani/Indonesian_Legal_QA | Apache-2.0 | ✅ |
| 3 | **Legal_Contract_Syn** | 18.293 baris kontrak legal sintetis (Bahasa Indonesia) | https://huggingface.co/datasets/Azzindani/Legal_Contract_Syn | Perlu dicek | ✅ |
| 4 | **Indonesian Regulations Dataset** (Kaggle) | Kumpulan UU & Peraturan Daerah Indonesia | https://www.kaggle.com/datasets/hermansugiharto/indonesian-regulations-dataset | Perlu dicek | ✅ (verifikasi jumlah berkas) |
| 5 | **MultiEURLEX** | 65.000 UU Uni Eropa, 23 bahasa, berlabel EUROVOC | https://huggingface.co/datasets/coastalcph/multi_eurlex | Perlu dicek (CC) | ✅ |
| 6 | **LEDGAR** | ± 80.000 ketentuan kontrak SEC EDGAR (versi lengkap hingga ratusan ribu) | https://huggingface.co/datasets/coastalchp/ledgar | Perlu dicek (turunan SEC EDGAR) | ✅ |
| 7 | **MAUD** (Merger Agreement Understanding Dataset) | 38.000+ anotasi klausul perjanjian merger | https://huggingface.co/datasets/theatticusproject/maud | Perlu dicek | ✅ |
| 8 | **ObliQA** | 27.869 pertanyaan kepatuhan dari rulebook ADGM (regulasi keuangan) | https://github.com/RegNLP/ObliQADataset | Perlu dicek | ✅ |
| 9 | **CUAD** (Contract Understanding Atticus Dataset) | 510 kontrak komersial, 13.000+ anotasi klausul | https://huggingface.co/datasets/theatticusproject/cuad-qa · https://www.kaggle.com/datasets/konradb/atticus-open-contract-dataset-aok-beta | CC BY 4.0 | ⚠️ (anotasi > 1.000, tetapi dokumen 510) |
| 10 | **Financial Product & Services Customer Complaints** (Kaggle) | 1.473.407 baris, 16 kolom (keluhan nasabah bank) | https://www.kaggle.com/datasets/taeefnajib/bank-customer-complaints | Perlu dicek | ✅ |
| 11 | **SEC EDGAR Filings (1993–2024)** (Kaggle) | Kompilasi seluruh pengajuan berkas ke SEC (jutaan baris) | https://www.kaggle.com/datasets/joaobrasoliveira/securities-and-exchange-comission-sec-master | Publik | ✅ |
| 12 | **Peraturan Indonesia (JDIH / data.go.id)** | UU, PP, Peraturan OJK (unduh resmi) | https://peraturan.go.id · https://data.go.id | Terbuka (dokumen hukum publik) | ✅ |

> **✅ KEPUTUSAN DATASET (dikunci):** **ID_Regulations** (`horelulus/ID_Regulations`, > 250.000 dokumen PDF peraturan Indonesia) dipakai sebagai **satu-satunya korpus utama**.
> - **Alasan:** 100% Bahasa Indonesia, domain hukum/regulasi, lisensi Apache-2.0, ukuran jauh melebihi syarat 1.000 data, dan tidak perlu izin pihak ketiga.
> - **Pertanyaan uji (gold set):** dibuat **manual ± 50–100 pertanyaan** berpasangan jawaban + rujukan pasal, disimpan di `eval/` (lihat Langkah 5). Korpus ini **tidak** menyertakan pasangan tanya-jawab, jadi gold set memang harus dibuat sendiri.
> - **Skenario keamanan (RQ3):** tambahkan **dokumen sintetis kecil ber-*canary token*** (mis. 50–100 dokumen) ke dalam korpus uji — cukup untuk mengukur injeksi prompt & kebocoran data tanpa mengubah dataset utama.
>
> Dataset lain (Indonesian_Legal_QA, LEDGAR, MultiEURLEX, CUAD, MAUD, ObliQA, dll.) hanya menjadi **pembanding/rujukan**, bukan data utama.
>
> **Verifikasi:** tautan di atas dapat dibuka. Sebelum dipakai, **cek ulang jumlah baris & lisensi** pada halaman dataset, lalu catat di tabel 1.4.

> **Aturan:** begitu memilih dataset, isi tabel metadata di bawah dan simpan berkas aslinya di `data/raw/`.

### 1.4 Metadata Dataset (Wajib Diisi)

| Nama dataset | Sumber (URL/DOI) | Tanggal unduh | Jumlah baris/dokumen | Jumlah fitur/kolom | Lisensi | Hash berkas (md5/sha256) |
|---|---|---|---|---|---|---|
| **ID_Regulations** (korpus utama) | https://huggingface.co/datasets/horelulus/ID_Regulations | 2026-10-10 | > 250.000 dokumen PDF (293 arsip ZIP; ukuran & jumlah PDF per arsip bervariasi) | 293 berkas ZIP berisi PDF | Apache-2.0 | `c3cd53681aab66fd47e5eba186b9c9e231e7ec511adbb96c315d782d513f0a1f` (batch-1-chunk-1); selengkapnya di `data/raw/ID_Regulations/_manifest.json` |
| **Gold set QA manual** (data uji RQ1–RQ2) | Dibuat sendiri → `eval/` | `<YYYY-MM-DD>` | 50–100 pasang pertanyaan-jawaban | 3 kolom (pertanyaan, jawaban, rujukan pasal) | — | `<isi setelah dibuat>` |
| **Dokumen sintetis ber-canary** (uji RQ3) | Dibuat sendiri → `data/raw/synthetic/` | `<YYYY-MM-DD>` | 50–100 dokumen | 1 kolom (teks) | — | `<isi setelah dibuat>` |

> Isi `<YYYY-MM-DD>` dan hash dengan perintah, mis. `Get-FileHash data\raw\<berkas>.zip -Algorithm SHA256` (PowerShell). Simpan berkas asli di `data/raw/` dan **jangan** diedit.
>
> **Strategi penyimpanan data (keputusan):** simpan cukup **arsip ZIP asli** di `data/raw/ID_Regulations/_zips/` (±50% hemat ruang dibanding menyimpan PDF hasil ekstraksi). Folder `pdfs/` adalah **artefak turunan** — boleh dihapus dan diregenerasi kapan saja dengan menjalankan kembali notebook `01_unduh_dataset` (ZIP yang sudah ada di `_zips/` akan dipakai ulang otomatis, tanpa unduh ulang).
>
> **Berkas bantu yang sudah disiapkan:**
> - `notebook/00_cek_lingkungan.ipynb` — memeriksa Python/OS, paket, GPU+CUDA, Ollama, dan model embedding (jalankan pertama di tiap mesin).
> - `notebook/01_unduh_dataset.ipynb` — mengunduh arsip **ZIP** ID_Regulations (`batch-N-chunk-M.zip`, mode *subset* atau *full*), mengekstrak PDF, menghitung jumlah dokumen, dan menulis `_manifest.json`.
> - `notebook/02_indexing.ipynb` — pipeline *parsing → chunking → embedding → ChromaDB*; parameter `CHUNK_SIZE`, `CHUNK_OVERLAP`, `EMBED_MODEL` dijadikan variabel eksperimen (RQ2).
> - `notebook/03_evaluasi_akurasi.ipynb` — mengukur **Recall@k, MRR, nDCG@k**, Exact Match/F1, dan **RAGAS** (faithfulness, answer relevancy, context precision/recall) memakai `eval/gold_qa.csv` + baseline LLM *tanpa* RAG (RQ1–RQ2).
> - `notebook/04_keamanan.ipynb` — uji **injeksi prompt langsung & tidak langsung**, **canary token leakage**, dan **refusal rate** pada dokumen sintetis (RQ3).
> - `notebook/05_efisiensi.ipynb` — mengukur **latensi retrieval**, **TTFT/total latensi generasi**, **token/detik**, serta **puncak VRAM & RAM** (RQ4).
> - `eval/gold_qa.csv` — template gold set (50–100 pertanyaan); isi kolom `sumber` dengan nama berkas dari ID_Regulations.
> - `src/make_canary_docs.py` — membuat dokumen sintetis ber-*canary* + prompt uji injeksi (`python src/make_canary_docs.py --docs 60`).

### 1.5 Checklist Langkah 1

- [x] Minimal **1 dataset publik** terpilih, sudah lolos 3 saringan. → **ID_Regulations**
- [x] Berkas asli tersimpan di `data/raw/` (tidak diedit). → `data/raw/ID_Regulations/_zips/` (ZIP asli; PDF diekstrak ulang dari ZIP saat eksperimen dijalankan)
- [x] Metadata + lisensi tercatat (tabel 1.4). → ID_Regulations: Apache-2.0, diunduh 2026-10-10
- [x] Strategi data (A/B/C) sudah diputuskan dan alasannya siap dipertanggungjawabkan. → **B + C**
- [x] `data/` sudah masuk `.gitignore`.

---

## Langkah 2 — Persiapan Lingkungan Pengembangan Lokal

Tujuan: menyiapkan **meja kerja riset** yang tertib dan dapat direproduksi. Semua dijalankan **lokal** (on-premise) karena inti skripsi ini adalah keamanan data.

### 2.1 Perangkat Lunak Wajib

| Perangkat | Versi disarankan | Fungsi untuk skripsi ini |
|---|---|---|
| **Git** | terbaru | Rekam jejak perubahan (commit, tag) |
| **Miniconda / Anaconda** | terbaru | Isolasi environment per proyek |
| **Python** | 3.11 atau 3.12 | Bahasa utama; hindari versi terlalu baru agar pustaka ML stabil |
| **VS Code** | terbaru | Editor + terminal + Jupyter |
| **Jupyter Notebook / JupyterLab** | terbaru | EDA & eksperimen interaktif |
| **OpenCode** | terbaru | Asisten riset agentic (membaca proyek, menulis/menjalankan kode) |
| **Hermes** | terbaru | Asisten riset agentic (alternatif/pendamping) |
| **Ollama** (atau **LM Studio** / **llama.cpp**) | terbaru | Menjalankan **Local LLM** (inference on-premise) |
| **ChromaDB** atau **FAISS** | terbaru | Vector database lokal |
| **Zotero** / **Mendeley** | terbaru | Manajemen referensi (sitasi otomatis) |

> **Catatan GPU (khusus RTX 2060 — 6 GB VRAM):** pastikan `nvidia-smi` mendeteksi GPU, lalu pasang **torch dengan CUDA** (bukan versi CPU):
> ```powershell
> # sesuaikan cu121/cu124 dengan driver CUDA Anda
> pip install torch --index-url https://download.pytorch.org/whl/cu121
> ```
> Karena VRAM hanya 6 GB:
> - Model embedding `bge-m3` (±2,2 GB fp16) **aman** untuk lolos.
> - Local LLM 7B–8B 4-bit **pas-pasan**; sebagian layer bisa tumpah ke CPU/RAM. Bila terlalu lambat, pilih `phi3:mini` (3B) atau `qwen2.5:7b`.
> - Jalankan embedding (notebook 02) dan generasi (notebook 03/04) **bergantian**, jangan bersamaan, agar VRAM tidak penuh.
> - Pantau pemakaian VRAM dengan `nvidia-smi -l 1` saat menjalankan run.
>
> **Penting:** menjalankan proyek di dua mesin (laptop = unduh & susun, PC rumah = uji GPU) itu wajar. Cukup **commit kode & notebook**, jangan commit `data/`/`vectorstore/` (sudah di-`.gitignore`); lalu di rumah jalankan ulang `notebook/01` → `notebook/02`.

### 2.2 Perintah Instalasi Dasar (Windows / PowerShell)

```powershell
# 1) Buat environment khusus (jangan pakai base)
conda create -n skripsi-rag python=3.11 -y
conda activate skripsi-rag

# 2) Inisialisasi Git
git init
git branch -M main
```

`requirements.txt` (versi awal — sesuaikan setelah uji coba):

```txt
# --- inti RAG ---
langchain==0.3.*
langchain-community==0.3.*
chromadb==0.5.*          # vector store lokal (alternatif: faiss-cpu)
sentence-transformers==3.0.*
pypdf==5.*               # parsing PDF
python-docx==1.*         # parsing DOCX (SOP perusahaan)

# --- Local LLM ---
ollama==0.4.*            # klien Python ke server Ollama

# --- evaluasi ---
ragas==0.2.*             # faithfulness, answer relevancy, context precision/recall
langchain-ollama==0.2.*  # LLM & embedding lokal untuk RAGAS (tanpa API eksternal)
datasets==3.*
pandas==2.*
scikit-learn==1.*

# --- utilitas ---
python-dotenv==1.*
tqdm==4.*
psutil==5.*               # pantau RAM/CPU untuk metrik efisiensi (RQ4)
jupyterlab==4.*
```

```powershell
pip install -r requirements.txt

# GPU NVIDIA (mis. RTX 2060): pasang torch CUDA, ganti cu121 sesuai driver
pip install torch --index-url https://download.pytorch.org/whl/cu121
```

# 3) Jalankan Local LLM lewat Ollama (di terminal terpisah)
ollama pull llama3.1:8b-instruct-q4_K_M   # contoh model generator
ollama pull nomic-embed-text              # contoh model embedding lokal
ollama list                               # verifikasi model tersedia
```

> **Model alternatif** yang sering dipakai untuk riset on-premise: `mistral:7b`, `qwen2.5:7b`, `gemma2:9b`, `phi3:mini` (ringan). Embedding: `nomic-embed-text`, `bge-m3` (multibahasa, cocok untuk Bahasa Indonesia). Catat **nama + ukuran kuantisasi** setiap model yang diuji — ini bagian dari metode.

### 2.3 Kode Verifikasi Lingkungan

Sudah tersedia: **`notebook/00_cek_lingkungan.ipynb`** (jalankan pertama di setiap mesin). Isi intinya:

```python
import sys, platform
import pandas, numpy, sklearn
import chromadb, sentence_transformers, ragas
import ollama

print("Python  :", sys.version.split()[0])
print("OS      :", platform.platform())
print("NumPy   :", numpy.__version__)

# Cek koneksi ke Local LLM
resp = ollama.chat(model="llama3.1:8b-instruct-q4_K_M",
                   messages=[{"role": "user", "content": "Balas satu kata: siap"}])
print("LLM     :", resp["message"]["content"])
print("Embedding model siap:", sentence_transformers.SentenceTransformer is not None)
```

Jika blok di atas berjalan tanpa galat, lingkungan Anda siap.

### 2.4 `.gitignore` (Rekomendasi)

```gitignore
# Data & artefak besar (JANGAN masuk Git)
data/raw/*
data/processed/*
vectorstore/
model/*.bin
model/*.gguf
!data/raw/.gitkeep
!data/processed/.gitkeep

# Python
__pycache__/
*.pyc
.ipynb_checkpoints/
.venv/
.env

# OS/editor
.DS_Store
Thumbs.db
```

> Sumber data besar **tidak** di-commit; cukup dokumentasikan tautan + lisensi di README ini agar orang lain bisa mengunduh ulang.

### 2.5 Checklist Langkah 2

- [ ] `conda env` aktif, `requirements.txt` terpasang.
- [ ] `git init` selesai, ada commit pertama.
- [ ] Ollama berjalan & minimal 1 model generator + 1 embedding ter-*pull*.
- [ ] Notebook verifikasi berjalan tanpa galat.
- [ ] `.gitignore` aktif; `git status` tidak menampilkan `data/raw`.
- [ ] Git terhubung ke repo GitHub (lihat [Checklist Sebelum Commit & Push](#checklist-sebelum-commit--push)).

---

## Langkah 3 — Menentukan Topik Penelitian

### 3.1 Posisi Riset: Riset Berbasis Data vs Design Science

Buku metopen menegaskan dua "wajah" riset informatika. Tentukan sejak awal, karena menentukan bentuk bukti:

| Aspek | Riset Berbasis Data (utama di prodi) | Design Science Research (DSR) |
|---|---|---|
| Orientasi | Membandingkan/optimasi metode & metrik | Membangun artefak sistem pembungkus model |
| Pertanyaan khas | "Konfigurasi/metode mana paling akurat & aman?" | "Bagaimana merancang sistem yang memenuhi kebutuhan?" |
| Bukti utama | Metrik kuantitatif (RAGAS, Recall@k, dll.) | Evaluasi performa artefak + metrik model |
| Contoh judul | Perbandingan strategi retrieval pada RAG lokal | Rancang bangun sistem tanya jawab regulasi berbasis RAG |

> **Saran:** bungkus topik RAG ini sebagai **riset berbasis data** (bukan sekadar membangun aplikasi). Yang menjadi inti ilmiah bukan antarmuka, melainkan **perbandingan konfigurasi** yang diukur. Sistemnya menjadi *wrapper* — itu sah, dan DSR dipakai sebagai kerangka pelengkap bila perlu.

### 3.2 Menajamkan Topik (dari luas ke spesifik)

Isi rantai berikut (contoh terisi, silakan ganti):

- **Objek/tugas:** tanya-jawab atas dokumen regulasi/kebijakan perusahaan.
- **Masalah:** LLM umum *halusinasi* dan tidak punya akses ke dokumen internal; mengirim dokumen ke API publik **melanggar kerahasiaan**.
- **Solusi yang diuji:** **RAG** di atas **Local LLM** (data tidak keluar dari infrastruktur).
- **Dimensi yang diukur:** **akurasi** (kesetiaan jawaban ke sumber) dan **keamanan** (kebocoran data, injeksi prompt).
- **Pembanding (baseline):** LLM tanpa RAG (closed-book), dan variasi strategi retrieval/chunking/embedding.

### 3.3 Kalimat Topik (Working Statement)

> "Penelitian ini mengevaluasi dan mengoptimalkan **Retrieval-Augmented Generation** pada **Local Large Language Model** untuk sistem tanya jawab **regulasi perusahaan**, dengan mengukur **akurasi jawaban** (kesetiaan pada sumber) dan **keamanan data** (ketahanan terhadap injeksi prompt dan kebocoran informasi), lalu membandingkan konfigurasi retrieval/embedding yang berbeda."

### 3.4 Checklist Langkah 3

- [ ] Pendekatan ditetapkan: riset berbasis data (+ DSR bila perlu).
- [ ] Objek, masalah, solusi, dimensi ukur, dan baseline jelas.
- [ ] Topik lolos 3 saringan (data, kebermaknaan, kelayakan teknis).

---

## Langkah 4 — Menentukan Judul Penelitian

### 4.1 Pola Judul Riset Berbasis Data

Pola yang lazim dan diterima:

```
Optimasi <TUGAS> tentang <OBJEK/A> menggunakan <ALGORITMA B> dan <ALGORITMA C>
```

- `<TUGAS>`: prediksi / klasifikasi / clustering / peringkasan / **tanya jawab (QA)** / deteksi.
- `<ALGORITMA B>` dan `<C>`: dua (atau lebih) metode/pendekatan yang **dibandingkan** dan diukur.

Petunjuk memilih `B` dan `C` untuk tema RAG — pilih **satu variabel** yang dibandingkan agar terukur:

| Variabel yang dibandingkan | Contoh "Algoritma B" vs "C" |
|---|---|
| Strategi retrieval | Dense retrieval vs BM25 vs Hybrid |
| Model embedding | `bge-m3` vs `nomic-embed-text` vs `multilingual-e5` |
| Ukuran chunk / overlap | chunk 256 vs 512 vs 1024 token |
| Local LLM | Llama 3.1 8B vs Mistral 7B vs Qwen2.5 7B |
| Re-ranking | Tanpa re-ranker vs dengan cross-encoder re-ranker |
| Pendekatan | Naive RAG vs Advanced/Modular RAG |

### 4.2 Alternatif Judul (Silakan Pilih / Sesuaikan)

1. **Perbandingan Strategi Retrieval pada Retrieval-Augmented Generation Berbasis Local Large Language Model untuk Akurasi dan Keamanan Sistem Tanya Jawab Regulasi Perusahaan** *(paling dekat dengan contoh pola)*
2. **Optimasi Tanya Jawab Regulasi Perusahaan Menggunakan Retrieval-Augmented Generation pada Local Large Language Model dan Re-ranking**
3. **Evaluasi Akurasi dan Keamanan Sistem Tanya Jawab Regulasi Perusahaan Berbasis RAG dengan Perbandingan Model Embedding Lokal**
4. **Perbandingan Dense, Sparse, dan Hybrid Retrieval pada RAG Berbasis Local Large Language Model untuk Dokumen Kebijakan Perusahaan**

### 4.3 Kriteria Judul yang Baik

- Memuat **objek**, **metode/pendekatan yang dibandingkan**, dan **tujuan pengukuran**.
- Mengandung kata kunci bidang: RAG, Local LLM, retrieval/embedding.
- Tidak terlalu panjang (idealnya ≤ 20 kata inti), tidak memakai singkatan tak baku.
- Sesuai dengan **data yang benar-benar tersedia** dan **metrik yang bisa diukur**.
- Judul ≠ judul aplikasi. Fokus ke **perbandingan/evaluasi**, bukan "membangun aplikasi".

### 4.4 Judul Terpilih

Usulan judul final (mengikuti pola `4.1`, satu variabel perbandingan utama = **strategi retrieval/embedding**):

```
Perbandingan Strategi Retrieval pada Retrieval-Augmented Generation Berbasis
Local Large Language Model untuk Akurasi dan Keamanan Sistem Tanya Jawab
Regulasi Perusahaan
```

> **Status:** *usulan* — wajib disetujui dan disesuaikan oleh dosen pembimbing sebelum
> dipakai resmi. Bila pembimbing menghendaki variabel lain (mis. model embedding atau
> ukuran chunk), ganti frasa "Strategi Retrieval" sesuai keputusan tersebut.

### 4.5 Checklist Langkah 4

- [x] Judul mengikuti pola dan memuat metode yang dibandingkan. → "Perbandingan Strategi Retrieval ..."
- [x] 1 variabel perbandingan utama sudah dipilih (agar terukur). → **strategi retrieval/embedding**
- [x] Judul selaras dengan dataset (Langkah 1) dan metrik (Langkah 5). → ID_Regulations + Recall@k/MRR/nDCG, RAGAS, canary leak
- [ ] Judul disetujui pembimbing.

---

## Langkah 5 — Menentukan Pertanyaan Penelitian

Rumusan masalah (BAB I) dan pertanyaan penelitian (BAB II) harus **selaras**, lalu setiap RQ dipetakan ke metrik dan eksperimen.

### 5.1 Rumusan Masalah (Kalimat Tanya)

1. Bagaimana merancang pipeline RAG **on-premise** (Local LLM) untuk tanya jawab dokumen regulasi perusahaan?
2. Konfigurasi retrieval/embedding mana yang menghasilkan **akurasi** jawaban terbaik?
3. Seberapa **aman** sistem RAG lokal dibandingkan (a) LLM API publik dan (b) LLM lokal **tanpa** RAG terhadap kebocoran data dan injeksi prompt?
4. Bagaimana **trade-off** akurasi, keamanan, waktu respons, dan kebutuhan sumber daya komputasi antar konfigurasi?

### 5.2 Pertanyaan Penelitian → Metrik → Eksperimen

| RQ | Pertanyaan Penelitian | Metrik | Eksperimen/Perbandingan |
|---|---|---|---|
| **RQ1** | Seberapa baik pipeline RAG lokal menjawab pertanyaan regulasi dibanding LLM tanpa RAG (*baseline*)? | RAGAS: *faithfulness*, *answer relevancy*, *context precision/recall*; akurasi jawaban (EM/F1) | Retrieval aktif vs *closed-book* |
| **RQ2** | Strategi retrieval/embedding mana yang paling akurat? | `Recall@k`, `MRR`, `nDCG`; RAGAS | Dense vs BM25 vs Hybrid; model embedding A vs B; chunk 256 vs 512 vs 1024 |
| **RQ3** | Seberapa tahan sistem terhadap upaya **kebocoran data** dan **injeksi prompt**? | Tingkat keberhasilan injeksi (%), tingkat kebocoran *canary token* (%), *refusal rate* | Uji *red-teaming* (indirect & direct prompt injection), uji data keluar jaringan, uji *jailbreak* |
| **RQ4** | Berapa biaya/kompromi (akurasi vs latensi vs VRAM)? | Latensi (s), token/s, puncak VRAM/RAM (GB), ukuran indeks | Profiling per konfigurasi |

> **Gold set (data uji) untuk RQ1–RQ2:** karena ID_Regulations **tidak** menyertakan pasangan tanya-jawab, buat **± 50–100 pertanyaan** Bahasa Indonesia beserta jawaban rujukan + pasal/klausul sumbernya, lalu simpan di `eval/` (mis. `eval/gold_qa.csv`). Set ini dipakai untuk menghitung EM/F1, RAGAS, dan Recall@k. Untuk RQ3, siapkan **dokumen sintetis ber-*canary token*** + daftar prompt injeksi di `eval/red_team/`.

### 5.3 Hipotesis (Dugaan Sementara, Uji di BAB IV)

- **H1:** RAG lokal meningkatkan *faithfulness* dan akurasi jawaban dibanding LLM tanpa retrieval pada dokumen regulasi.
- **H2:** Hybrid retrieval (dense + BM25) mengungguli dense-only pada pertanyaan bernuansa hukum yang memuat singkatan/istilah khusus.
- **H3:** Sistem RAG lokal memiliki tingkat kebocoran data ke luar jaringan **0%** dan lebih tahan terhadap kebocoran konteks dibanding arsitektur berbasis API publik.
- **H4:** Ada *trade-off*: konfigurasi paling akurat cenderung menambah latensi/kebutuhan VRAM.

### 5.4 Metrik Keamanan (Rincian)

| Aspek | Cara ukur | Ukuran |
|---|---|---|
| Indirect prompt injection | Sisipkan instruksi jahat ke dokumen di korpus, lihat apakah muncul di jawaban | % serangan berhasil |
| Direct prompt injection / jailbreak | Prompt uji untuk membocorkan system prompt / dokumen lain | % berhasil |
| Kebocoran data | *Canary token* di dokumen; cek apakah token muncul pada respons atau keluar jaringan | % token bocor; ada/tidak *egress* |
| Ketergantungan pada sumber | Cek apakah setiap klaim jawaban punya rujukan pasal/klausul | % jawaban bergrounding |

### 5.5 Checklist Langkah 5

- [ ] 4 rumusan masalah dalam kalimat tanya.
- [ ] RQ selaras dengan rumusan masalah, judul, dan dataset.
- [ ] Setiap RQ punya metrik + eksperimen + (hipotesis) yang jelas.
- [ ] Ada metrik **akurasi** dan **keamanan** (inti judul).

---

## Langkah 6 — Studi Literatur dan Tabel Penelitian Terdahulu (State of the Art)

### 6.1 Strategi Penelusuran

- **Basis data:** Google Scholar, IEEE Xplore, ACM DL, arXiv, Sinta/Garuda (konteks Indonesia).
- **Kata kunci Boolean** (contoh):
  ```
  ("retrieval-augmented generation" OR "RAG") AND ("local language model" OR "on-premise LLM" OR "private LLM")
  AND ("enterprise" OR "regulatory" OR "compliance" OR "policy document")

  ("RAG") AND ("privacy" OR "security" OR "prompt injection" OR "data leakage")

  ("retrieval") AND ("hybrid" OR "dense" OR "BM25") AND ("question answering") AND ("evaluation")
  ```
- **Filter:** 5–10 tahun terakhir; utamakan **artikel jurnal/prosiding primer** (bukan blog).
- **Snowballing:** telusuri daftar pustaka artikel yang sudah didapat (ke belakang) dan artikel yang mengutipnya (ke depan).
- **Target:** 10–15 artikel relevan untuk tabel di bawah; simpan semuanya ke Zotero/Mendeley.

### 6.2 Tabel Penelitian Terdahulu (State of the Art)

> **Kolom:** Peneliti (Tahun) | Dataset | Metode/Algoritma | Metrik | Hasil utama | Celah penelitian.
> **Status:** ✅ = sudah diverifikasi ke sumber primer (judul/penulis/venue cocok). ⚠️ = kandidat, **wajib diverifikasi** sebelum masuk daftar pustaka.

| # | Peneliti (Tahun) | Dataset | Metode / Fokus | Metrik | Hasil utama | Celah | Status |
|---|---|---|---|---|---|---|---|
| 1 | Lewis dkk. (2020) — *NeurIPS 2020* | Wikipedia (dense index) | RAG: parametric + non-parametric memory (DPR + seq2seq) | EM, akurasi QA | Menetapkan fondasi arsitektur RAG; SOTA pada 3 tugas open-domain QA | Model besar, bukan on-premise; belum bahas keamanan | ✅ |
| 2 | Karpukhin dkk. (2020) — *EMNLP 2020* | Natural Questions, TriviaQA, dll. | Dense Passage Retrieval (dual-encoder) | Top-20 retrieval accuracy | Retrieval dense mengungguli BM25 9–19% absolut | Dominan bahasa Inggris; belum uji dokumen legal berbahasa Indonesia | ✅ |
| 3 | Gao dkk. (2023) — *arXiv:2312.10997* | Berbagai | Survei: Naive/Advanced/Modular RAG | — (survei) | Taksonomi RAG + kerangka evaluasi & tantangan | Survei, bukan eksperimen; celah keamanan belum ditonjolkan | ✅ |
| 4 | Es dkk. (2023/2025) — *arXiv:2309.15217* | Berbagai | RAGAS: evaluasi RAG tanpa ground-truth | Faithfulness, answer relevancy, context precision/recall | Kerangka evaluasi otomatis tanpa anotasi manusia | Belum menyertakan dimensi keamanan/privacy | ✅ |
| 5 | Yu dkk. (2025) — *KnowledgeNLP 2025*, DOI 10.18653/v1/2025.knowledgenlp-1.13 | EKRAG (dokumen korporat: rilis produk, blog, laporan keuangan) | Benchmark RAG enterprise; EM-as-judge & RM-as-judge | Correctness, relevance, faithfulness | Panduan optimasi pipeline RAG untuk QA korporat | Tak fokus pada LLM lokal & pertahanan keamanan | ✅ |
| 6 | Bodea dkk. (2026) — *arXiv:2601.03979* (SaTML 2026) | Tinjauan sistematis | SoK: risiko & mitigasi privasi pada RAG | Taksonomi + strategi evaluasi | Sistematisasi pertama risiko privasi RAG | Survei; belum ada eksperimen pipeline lokal spesifik | ✅ |
| 7 | Gill dkk. (2025) — *arXiv:2509.21324* | Dataset enterprise beragam | Kerangka kapabilitas RAG L1–L5; uji LangChain, Azure AI Search, OpenAI, Corvic | Kualitas jawaban per level | Multi-space retrieval & orkestrasi dinamis menaikkan kapabilitas | Platform cloud; belum menimbang keamanan on-premise | ✅ |
| 8 | Deußer dkk. (2026) — *arXiv:2609.30009* | ObliQA (rulebook ADGM) | RAG domain-adapted (LegalBERT + BM25 fusion + RAFT/LoRA, model 2B–12B 4-bit) | Recall@10, RePASs | Recall@10 naik 0,256 → 0,774; RAFT memperbaiki kualitas jawaban | Penulis menyoroti bahwa skor RePASs **belum** menjamin grounding; keamanan tak diuji | ✅ |
| 9 | Karakurt & Akbulut (2025) — *Applied Sciences* 16(1):368, DOI 10.3390/app16010368 | Tinjauan literatur enterprise KM | Survei aplikasi RAG + LLM pada manajemen pengetahuan perusahaan | — (survei) | Memetakan platform & pola penerapan RAG enterprise | Survei; belum mengukur akurasi vs keamanan secara empiris | ✅ |
| 10 | OWASP (2025) — *OWASP Top 10 for LLM Applications* (LLM01: Prompt Injection) | Kasus nyata/industri | Kerangka risiko keamanan LLM; injeksi prompt (langsung & tidak langsung) | Kategorisasi risiko | Rujukan standar risiko keamanan aplikasi LLM | Pedoman praktik; bukan studi akademik terukur | ⚠️ |

### 6.3 Jurnal Minimal SINTA 3 dan Jurnal Scopus (Rujukan Sudah Diverifikasi)

> **Cara membaca tabel ini:** setiap tautan DOI di bawah **sudah diuji dengan membuka `https://doi.org/...`** dan mengarah ke artikel yang benar (judul–penulis–venue cocok). Status akreditasi/indeksasi diambil dari halaman penerbit; **periksa ulang di [SINTA](https://sinta.kemdikbud.go.id) dan [ScimagoJR](https://www.scimagojr.com)** sebelum dikutip di naskah. Setelah terverifikasi, impor ke Zotero dan baca minimal abstrak + metode.

**A. Jurnal terakreditasi SINTA (minimal SINTA 3):**

| # | Artikel (peneliti, tahun) | Jurnal (venue) | Akreditasi | DOI (tautan terverifikasi) |
|---|---|---|---|---|
| 1 | Salman, M. D., Rahmaddeni, Nasution, T., & Susanti (2026). *Implementation of Retrieval-Augmented Generation Method on Large Language Model for Development of Campus Service and Information Chatbot* | INOVTEK Polbeng — Seri Informatika, 11(1), 298–309 | SINTA 3 | https://doi.org/10.35314/3y9hy151 |
| 2 | Lijaya, H., Ho, P., & Santoso, H. (2025). *Comparative Analysis of RAG-Based Open-Source LLMs for Indonesian Banking Customer Service Optimization Using Simulated Data* | Jurnal SISFOKOM, 14(3), 330–341 | SINTA 2 / Scopus | https://doi.org/10.32736/sisfokom.v14i3.2383 |
| 3 | Murtiyoso, M., Tahyudin, I., & Berlilana, B. (2025). *A Systematic Review of Retrieval-Augmented Generation for Enhancing Domain-Specific Knowledge in Large Language Models* | Sinkron: Jurnal dan Penelitian Teknik Informatika, 9(2), 969–977 | SINTA 3 | https://doi.org/10.33395/sinkron.v9i2.14824 |
| 4 | Hajar, M. R., Utami, E., & Hendi Muhammad, A. (2025). *A Systematic Literature Review of Retrieval-Augmented Generation: Methods, Applications, and Future Research Directions* | Journal of Applied Computer Science and Technology (JACOST), 6(2), 115–128 | SINTA 2 | https://doi.org/10.52158/jacost.v6i2.1170 |

**B. Jurnal terindeks Scopus:**

| # | Artikel (peneliti, tahun) | Jurnal (venue) | Indeks | DOI (tautan terverifikasi) |
|---|---|---|---|---|
| 5 | Chen, L.-C., Pardeshi, M. S., Liao, Y.-X., & Pai, K.-C. (2025). *Application of retrieval-augmented generation for interactive industrial knowledge management via a large language model* | Computer Standards & Interfaces, 94, 103995 | Scopus (Q1) | https://doi.org/10.1016/j.csi.2025.103995 |
| 6 | Pasaribu, J., Yudistira, N., & Mahmudy, W. F. (2024). *Tabular Data Classification and Regression: XGBoost or Deep Learning with Retrieval-Augmented Generation* | IEEE Access | Scopus (Q1) | https://doi.org/10.1109/ACCESS.2024.3518205 |
| 7 | Rangan, K., & Yin, Y. (2024). *A fine-tuning enhanced RAG system with quantized influence measure as AI judge* | Scientific Reports, 14, 27446 | Scopus (Q1) | https://doi.org/10.1038/s41598-024-79110-x |
| 8 | Amugongo, L. M., Mascheroni, P., Brooks, S., Doering, S., & Seidel, J. (2025). *Retrieval augmented generation for large language models in healthcare: A systematic review* | PLOS Digital Health, 4(6), e0000877 | Scopus | https://doi.org/10.1371/journal.pdig.0000877 |
| 9 | Li, Z., Wang, Z., Wang, W., Hung, K., Xie, H., & Wang, F. L. (2025). *Retrieval-augmented generation for educational application: A systematic survey* | Computers and Education: Artificial Intelligence, 8, 100417 | Scopus | https://doi.org/10.1016/j.caeai.2025.100417 |
| 10 | Gulyamov, S., dkk. (2026). *Prompt Injection Attacks in Large Language Models and AI Agent Systems: A Comprehensive Review of Vulnerabilities, Attack Vectors, and Defense Mechanisms* | Information, 17(1), 54 | Scopus (keamanan) | https://doi.org/10.3390/info17010054 |
| 11 | Milani, A., Franzoni, V., & Florindi, E. (2026). *Indirect prompt injection in large language models* | Neural Computing and Applications, 38(13) | Scopus (Q1, keamanan) | https://doi.org/10.1007/s00521-026-12266-x |
| 12 | Karakurt, E., & Akbulut, A. (2025). *Retrieval-Augmented Generation (RAG) and Large Language Models (LLMs) for Enterprise Knowledge Management and Document Automation: A Systematic Literature Review* | Applied Sciences, 16(1), 368 | Scopus | https://doi.org/10.3390/app16010368 |
| 13 | Neha, F., Bhati, D., & Shukla, D. K. (2025). *Retrieval-Augmented Generation (RAG) in Healthcare: A Comprehensive Review* | AI, 6(9), 226 | Scopus | https://doi.org/10.3390/ai6090226 |

### 6.4 Target Publikasi (Venue) yang Disarankan

| Venue | Akreditasi / Indeks | Penerbit / Penyelenggara | Catatan untuk skripsi ini |
|---|---|---|---|
| INOVTEK Polbeng — Seri Informatika | SINTA 3 | Politeknik Negeri Bengkalis | Cocok: chatbot/RAG bahasa Indonesia |
| Sinkron | SINTA 3 | Politeknik Ganesha | SLR & eksperimen teknik informatika |
| JACOST | SINTA 2 | Universitas Negeri Semarang | RAG & aplikasi komputer |
| Jurnal SISFOKOM | SINTA 2 / Scopus | STMIK Atma Luhur | Sistem informasi & komputer |
| Applied Sciences | Scopus (Q1–Q2) | MDPI | RAG enterprise / knowledge management |
| Information | Scopus | MDPI | Keamanan LLM & prompt injection |
| IEEE Access | Scopus (Q1) | IEEE | Multidisiplin, akses terbuka |
| Neural Computing and Applications | Scopus (Q1) | Springer | ML/AI + keamanan |

> **Saran strategi publikasi:** kejar **1 jurnal SINTA 2–3** dulu (lebih cepat, konteks lokal), lalu **1 jurnal Scopus** setelah eksperimen diperluas. Selalu buka halaman resmi jurnal untuk **scope, template, dan biaya (APC)** terkini — daftar di atas bisa berubah.

### 6.5 Contoh Artikel Tambahan (Bila Perlu Menambah)

Tambahkan bila ingin memperkuat sisi model lokal/evaluasi: **Asai dkk. (2023) Self-RAG** (arXiv:2310.11511), atau makalah model lokal spesifik (Llama, Mistral, Qwen) — **verifikasi dulu**.

### 6.6 Research Gap yang Diusulkan (Spesifik & Dapat Diuji)

Dari tabel di atas, celah penelitian skripsi ini dapat dirumuskan sebagai:

1. **Kebanyakan studi RAG berfokus pada akurasi**, sedangkan dimensi **keamanan (kebocoran data, injeksi prompt)** pada RAG **lokal/on-premise** belum banyak diuji secara kuantitatif.
2. **Studi RAG enterprise umumnya memakai model/platform cloud**, padahal perusahaan membutuhkan **deployment on-premise** agar data tidak keluar.
3. **Belum banyak perbandingan sistematis** antara strategi retrieval (dense/BM25/hybrid) dan model embedding untuk **dokumen regulasi berbahasa Indonesia**.
4. **Trade-off akurasi vs keamanan vs biaya komputasi** jarang dilaporkan bersamaan dalam satu studi lokal.

### 6.7 Verifikasi Sitasi (Prosedur Wajib)

Untuk **setiap** baris tabel (terutama yang ⚠️):

1. Cari judul **persis** di Google Scholar/DOI; tolak hasil yang hanya "mirip".
2. Cek DOI di `doi.org`/Crossref, nama venue resmi, volume-halaman.
3. Baca minimal **abstrak + metode**; pastikan artikel benar berkata seperti yang Anda kutip.
4. Simpan PDF + metadata ke Zotero/Mendeley dengan gaya (mis. IEEE/APA).
5. Jika tidak ditemukan sama sekali → **buang** dan catat sebagai indikasi halusinasi.

### 6.8 Checklist Langkah 6

- [ ] ≥ 10 artikel relevan terkumpul di Zotero/Mendeley (termasuk ≥ 1 dari jurnal SINTA 3+ dan ≥ 1 dari jurnal Scopus, lihat bagian 6.3).
- [ ] Tabel penelitian terdahulu terisi (dataset–metode–metrik–hasil–celah).
- [ ] Semua baris berstatus ✅ (sudah diverifikasi ke sumber primer).
- [ ] Minimal 1 dataset ≥ 1.000 data sudah dipilih dan tersimpan di `data/raw/` (lihat Langkah 1).
- [ ] ≥ 1 research gap spesifik dan dapat diuji.
- [ ] Sitasi & daftar pustaka dihasilkan alat (bukan diketik manual).

---

## Checklist Sebelum Commit & Push

```powershell
git add README.md .gitignore requirements.txt src/ notebook/ eval/ literatur/ draft/
git commit -m "docs: susun README peta jalan riset RAG (langkah 1-6)"
git tag v0.1-readme
git remote add origin https://github.com/<username>/<repo>.git   # sekali saja
git push -u origin main
```

Sebelum push, pastikan:

- [ ] `git status` bersih; tidak ada `data/raw/*`, `vectorstore/`, atau model besar yang ikut.
- [ ] Tidak ada kredensial/API key di kode (`.env` sudah di-ignore).
- [ ] Judul, RQ, dataset, dan metrik di README sudah konsisten satu sama lain.
- [ ] Setiap sitasi di README berstatus **terverifikasi**.
- [ ] Commit message deskriptif.

---

## Etika Penelitian dan Penggunaan AI (Akademik)

Diadaptasi dari buku ajar metopen (Bab I & III) dan pedoman UNISNU:

1. **Dilarang fatal:** fabrikasi (mengada-adakan data/hasil/sumber), falsifikasi (memanipulasi agar hasil tampak sesuai keinginan), plagiarisme.
2. **Gunakan AI secara etis:** untuk penjelasan, draf awal, dan kode; jangan menyalin mentah; selalu pahami & uji sendiri.
3. **Verifikasi setiap sitasi** dari AI ke sumber aslinya (model dapat berhalusinasi).
4. **Nyatakan penggunaan AI** secara transparan sesuai kebijakan prodi.
5. **Lindungi data:** jangan kirim data sensitif ke layanan AI pihak ketiga; manfaatkan Local LLM — sejalan dengan UU No. 27 Tahun 2022.
6. **Tanggung jawab ilmiah tetap pada peneliti manusia.**

---

## Ringkasan Alur Riset (Siklus Ilmiah)

```
Observasi & identifikasi masalah        ->  (Langkah 3)
        |
Pertanyaan penelitian terukur            ->  (Langkah 5)
        |
Studi literatur & hipotesis              ->  (Langkah 6)
        |
Pengumpulan & persiapan data             ->  (Langkah 1 & 2)
        |
Pemodelan & eksperimen (RAG lokal)       ->  (implementasi)
        |
Evaluasi kuantitatif & interpretasi      ->  (RQ1-RQ4)
        |
Kesimpulan, penulisan, & publikasi
```

Alur ini **iteratif**: hasil evaluasi sering mengembalikan peneliti ke tahap persiapan data atau perumusan masalah.

---

## Daftar Referensi

**Buku & pedoman:**

- Zyen, A. K. (2026). *Buku Ajar Metodologi Penelitian: Riset Berbasis Data dan Data Mining untuk Informatika*. Program Studi Teknik Informatika FST UNISNU Jepara. (file lokal: `Buku_ajar_metopen_pertemuan_1.pdf`, `..._2.pdf`, `..._3.pdf`)
- Universitas Islam Nahdlatul Ulama Jepara. (2023). *Pedoman Penulisan Skripsi, Tugas Akhir, dan Tesis* (Peraturan Rektor Nomor 57/PR/UNISNU/V/2023). (file lokal: `k3027_panduan-skripsi.pdf`)

**Artikel (status ✅ = sudah diverifikasi ke sumber primer):**

1. Lewis, P., Perez, E., Piktus, A., Petroni, F., Karpukhin, V., Goyal, N., … Kiela, D. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. NeurIPS 2020. arXiv:2005.11401. https://doi.org/10.48550/arXiv.2005.11401 ✅
2. Karpukhin, V., Oğuz, B., Min, S., Lewis, P., Wu, L., Edunov, S., Chen, D., & Yih, W. (2020). *Dense Passage Retrieval for Open-Domain Question Answering*. EMNLP 2020. arXiv:2004.04906. https://doi.org/10.48550/arXiv.2004.04906 ✅
3. Gao, Y., Xiong, Y., Gao, X., Jia, K., Pan, J., Bi, Y., … Wang, H. (2023). *Retrieval-Augmented Generation for Large Language Models: A Survey*. arXiv:2312.10997. https://doi.org/10.48550/arXiv.2312.10997 ✅
4. Es, S., James, J., Espinosa-Anke, L., & Schockaert, S. (2023/2025). *Ragas: Automated Evaluation of Retrieval Augmented Generation*. arXiv:2309.15217. https://doi.org/10.48550/arXiv.2309.15217 ✅
5. Yu, T., Zhou, W., Yang, L., Shukla, A., Madugula, M., Gundecha, P., … Zhang, V. (2025). *EKRAG: Benchmark RAG for Enterprise Knowledge Question Answering*. In *Proceedings of the 4th International Workshop on Knowledge-Augmented Methods for NLP* (hlm. 152–159). ACL. https://doi.org/10.18653/v1/2025.knowledgenlp-1.13 ✅
6. Bodea, A.-E., Meisenbacher, S., Klymenko, A., & Matthes, F. (2026). *SoK: Privacy Risks and Mitigations in Retrieval-Augmented Generation Systems*. arXiv:2601.03979. https://doi.org/10.48550/arXiv.2601.03979 ✅
7. Gill, G., Gupta, R., Lusson, D., Chandrashekar, A., & Nguyen, D. (2025). *From Search to Reasoning: A Five-Level RAG Capability Framework for Enterprise Data*. arXiv:2509.21324. https://doi.org/10.48550/arXiv.2509.21324 ✅
8. Deußer, T., Pillai, A., Bariviera, A. F., Bhardwaj, D., Sparrenberg, L., Berghaus, D., Bauckhage, C., & Sifa, R. (2026). *Automated Regulatory Compliance Question Answering in Financial Services with Domain-Adapted Retrieval-Augmented Generation*. arXiv:2609.30009. https://doi.org/10.48550/arXiv.2609.30009 ✅
9. Karakurt, E., & Akbulut, A. (2025). *Retrieval-Augmented Generation (RAG) and Large Language Models (LLMs) for Enterprise Knowledge Management and Document Automation: A Systematic Literature Review*. *Applied Sciences*, 16(1), 368. https://doi.org/10.3390/app16010368 ✅
10. OWASP Foundation. (2025). *OWASP Top 10 for LLM Applications* (LLM01: Prompt Injection). https://genai.owasp.org/llm-top-10/ ⚠️ *(verifikasi edisi/tahun)*

**Artikel dari jurnal terakreditasi SINTA 3+ (DOI terverifikasi):**

11. Salman, M. D., Rahmaddeni, Nasution, T., & Susanti. (2026). *Implementation of Retrieval-Augmented Generation Method on Large Language Model for Development of Campus Service and Information Chatbot*. *INOVTEK Polbeng — Seri Informatika*, 11(1), 298–309. https://doi.org/10.35314/3y9hy151 ✅
12. Lijaya, H., Ho, P., & Santoso, H. (2025). *Comparative Analysis of RAG-Based Open-Source LLMs for Indonesian Banking Customer Service Optimization Using Simulated Data*. *Jurnal SISFOKOM*, 14(3), 330–341. https://doi.org/10.32736/sisfokom.v14i3.2383 ✅
13. Murtiyoso, M., Tahyudin, I., & Berlilana, B. (2025). *A Systematic Review of Retrieval-Augmented Generation for Enhancing Domain-Specific Knowledge in Large Language Models*. *Sinkron*, 9(2), 969–977. https://doi.org/10.33395/sinkron.v9i2.14824 ✅
14. Hajar, M. R., Utami, E., & Hendi Muhammad, A. (2025). *A Systematic Literature Review of Retrieval-Augmented Generation: Methods, Applications, and Future Research Directions*. *Journal of Applied Computer Science and Technology (JACOST)*, 6(2), 115–128. https://doi.org/10.52158/jacost.v6i2.1170 ✅

**Artikel dari jurnal terindeks Scopus (DOI terverifikasi):**

15. Chen, L.-C., Pardeshi, M. S., Liao, Y.-X., & Pai, K.-C. (2025). *Application of retrieval-augmented generation for interactive industrial knowledge management via a large language model*. *Computer Standards & Interfaces*, 94, 103995. https://doi.org/10.1016/j.csi.2025.103995 ✅
16. Pasaribu, J., Yudistira, N., & Mahmudy, W. F. (2024). *Tabular Data Classification and Regression: XGBoost or Deep Learning with Retrieval-Augmented Generation*. *IEEE Access*. https://doi.org/10.1109/ACCESS.2024.3518205 ✅
17. Rangan, K., & Yin, Y. (2024). *A fine-tuning enhanced RAG system with quantized influence measure as AI judge*. *Scientific Reports*, 14, 27446. https://doi.org/10.1038/s41598-024-79110-x ✅
18. Amugongo, L. M., Mascheroni, P., Brooks, S., Doering, S., & Seidel, J. (2025). *Retrieval augmented generation for large language models in healthcare: A systematic review*. *PLOS Digital Health*, 4(6), e0000877. https://doi.org/10.1371/journal.pdig.0000877 ✅
19. Li, Z., Wang, Z., Wang, W., Hung, K., Xie, H., & Wang, F. L. (2025). *Retrieval-augmented generation for educational application: A systematic survey*. *Computers and Education: Artificial Intelligence*, 8, 100417. https://doi.org/10.1016/j.caeai.2025.100417 ✅
20. Gulyamov, S., dkk. (2026). *Prompt Injection Attacks in Large Language Models and AI Agent Systems: A Comprehensive Review of Vulnerabilities, Attack Vectors, and Defense Mechanisms*. *Information*, 17(1), 54. https://doi.org/10.3390/info17010054 ✅
21. Milani, A., Franzoni, V., & Florindi, E. (2026). *Indirect prompt injection in large language models*. *Neural Computing and Applications*, 38(13). https://doi.org/10.1007/s00521-026-12266-x ✅
22. Neha, F., Bhati, D., & Shukla, D. K. (2025). *Retrieval-Augmented Generation (RAG) in Healthcare: A Comprehensive Review*. *AI*, 6(9), 226. https://doi.org/10.3390/ai6090226 ✅

**Dataset publik (tautan terverifikasi, ≥ 1.000 data):**

- ID_Regulations (> 250.000 dokumen peraturan Indonesia) — https://huggingface.co/datasets/horelulus/ID_Regulations
- Indonesian_Legal_QA (8.078 pasang tanya-jawab hukum Indonesia) — https://huggingface.co/datasets/Azzindani/Indonesian_Legal_QA
- Legal_Contract_Syn (18.293 baris kontrak legal) — https://huggingface.co/datasets/Azzindani/Legal_Contract_Syn
- Indonesian Regulations Dataset (Kaggle) — https://www.kaggle.com/datasets/hermansugiharto/indonesian-regulations-dataset
- MultiEURLEX (65.000 UU Uni Eropa) — https://huggingface.co/datasets/coastalcph/multi_eurlex
- LEDGAR (± 80.000 ketentuan kontrak) — https://huggingface.co/datasets/coastalchp/ledgar
- MAUD (38.000+ anotasi klausul merger) — https://huggingface.co/datasets/theatticusproject/maud
- ObliQA (27.869 pertanyaan kepatuhan) — https://github.com/RegNLP/ObliQADataset
- Financial Product & Services Customer Complaints (1.473.407 baris) — https://www.kaggle.com/datasets/taeefnajib/bank-customer-complaints
- SEC EDGAR Filings 1993–2024 (Kaggle) — https://www.kaggle.com/datasets/joaobrasoliveira/securities-and-exchange-comission-sec-master

**Perkakas (tools):**

- Ollama — https://ollama.com
- ChromaDB — https://www.trychroma.com
- FAISS — https://github.com/facebookresearch/faiss
- LangChain — https://python.langchain.com
- RAGAS — https://docs.ragas.io
- Zotero — https://www.zotero.org

---

*README ini adalah living document. Perbarui bagian "Judul Terpilih", tabel dataset, dan tabel penelitian terdahulu setiap kali ada kemajuan bimbingan.*
