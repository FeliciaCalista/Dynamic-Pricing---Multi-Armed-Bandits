# Dynamic Pricing with Multi-Armed Bandits

Proyek ini mensimulasikan **dynamic pricing** untuk satu produk menggunakan beberapa algoritma **Multi-Armed Bandit** yang diimplementasikan dari nol.  
Fokus utama proyek adalah memahami **bagaimana agent belajar**, **bagaimana asumsi environment memengaruhi performa**, dan **mengapa algoritma tertentu unggul pada kondisi tertentu**.

---

## Persiapan Lingkungan (Environment)

Simulasi pasar terdiri dari:
- Satu produk
- Beberapa pilihan harga (arms)
- Pelanggan dengan perilaku probabilistik

### Data yang disimpan
- Daftar harga yang dapat dipilih agent
- Probabilitas pelanggan membeli pada harga tertentu

### Mekanisme `pull`
Setiap kali agent memilih harga:
- Pelanggan bisa membeli (purchase)
- Atau tidak membeli  
Peristiwa ini bersifat stokastik (acak)

---

## Jenis Environment

### 1. Dynamic Pricing Environment (Sederhana)

**Karakteristik:**
- Setiap arm memiliki peluang beli **konstan**
- Lingkungan **statis** (tidak berubah)
- Tidak ada sensitivitas harga
- Reward = harga
- Tidak ada noise pasar

Environment ini cocok untuk memahami konsep dasar **eksplorasi vs eksploitasi**.

---

### 2. Realistic Dynamic Pricing Environment

**Karakteristik:**
- Probabilitas beli **dinamis**
- Ditentukan oleh selisih harga dan *willingness to pay*
- Menggunakan fungsi sigmoid
- Lingkungan **non-stationary** (musiman / tren)
- Ada sensitivitas harga
- Reward = profit (harga − biaya)
- Ada noise pasar

Environment ini lebih mendekati **kondisi pasar nyata** dan menuntut agent untuk terus beradaptasi.

---

## Kerangka Agent

Semua agent mewarisi antarmuka yang sama:
- `select_arm()` → memilih harga
- `update(arm, reward)` → belajar dari hasil

Dengan desain ini, setiap agent:
- Menghadapi environment yang sama
- Dibandingkan secara adil
- Berbeda hanya pada strategi belajarnya

---

## Agent yang Digunakan

### Greedy Agent
- Tidak melakukan eksplorasi
- Selalu memilih arm dengan estimasi reward tertinggi
- Menyimpan:
  - Jumlah pemilihan tiap arm
  - Estimasi rata-rata reward

Kuat di environment statis, lemah di environment dinamis.

---

### ε-Greedy Agent
- Eksplorasi dengan probabilitas ε
- Eksploitasi (greedy) dengan probabilitas 1 − ε
- Lebih robust dibanding Greedy
- Namun eksplorasinya acak dan tidak efisien

---

### UCB (Upper Confidence Bound)
- Berdasarkan prinsip *Optimism Under Uncertainty*
- Arm yang jarang dicoba dianggap berpotensi baik
- Menambahkan bonus eksplorasi:
  - Arm jarang dicoba → bonus besar
  - Arm sering dicoba → bonus kecil
- Menjamin semua arm dicoba minimal satu kali

UCB tidak memilih arm terbaik saat ini, tetapi arm yang **mungkin terbaik**.

---

### Gradient Bandit (Policy-Based)
- Tidak belajar value, tetapi **policy**
- Mengukur apakah reward saat ini:
  - Lebih baik dari rata-rata
  - Atau lebih buruk dari rata-rata
- Preferensi arm diperbarui berdasarkan perbandingan tersebut
- Softmax digunakan untuk mengubah preferensi menjadi probabilitas

Agent ini belajar membentuk kebijakan secara langsung, mirip proses pengambilan keputusan manusia.

---

## Simulasi

Setiap langkah simulasi:
1. Agent memilih arm (`select_arm`)
2. Environment memberikan reward (`pull`)
3. Agent memperbarui strategi (`update`)
4. Data dicatat

### Data yang direkam
- `rewards` → reward tiap langkah
- `actions` → arm yang dipilih
- `cumulative_rewards` → total reward kumulatif

Cumulative reward digunakan sebagai metrik utama performa agent.

---

## Hasil Eksperimen

### Dynamic Pricing Environment (Statis)

**Urutan performa:**
Greedy ≈ UCB > Gradient Bandit > Epsilon-Greedy

**Pembahasan:**
- Greedy kebetulan menemukan arm terbaik di awal dan terus mengeksploitasinya
- UCB, setelah ketidakpastian menurun, juga mengunci arm terbaik
- Gradient Bandit tetap eksploratif sehingga tertinggal di awal
- Epsilon-Greedy sengaja melakukan eksplorasi meski sudah tahu arm terbaik

---

### Realistic Dynamic Pricing Environment (Non-Stationary)

**Urutan performa:**
Gradient Bandit > Epsilon-Greedy > UCB > Greedy

**Pembahasan:**
- Gradient Bandit mampu menyesuaikan distribusi harga mengikuti perubahan pasar
- Epsilon-Greedy tetap adaptif, namun eksplorasinya acak
- UCB lambat beradaptasi karena asumsi stasioner
- Greedy gagal total karena tidak pernah eksplorasi

---

## Insight Utama

> **Algoritma terbaik sangat bergantung pada sifat environment**

- Environment statis → eksploitasi agresif unggul
- Environment dinamis → eksplorasi adaptif lebih penting
- Policy-based methods unggul pada dynamic pricing nyata

---

## Pengembangan Lanjutan

- Contextual Bandits (waktu, segmen pelanggan)
- Regret analysis
- Sliding-window UCB
- Thompson Sampling
- Full Reinforcement Learning (state-based pricing)

