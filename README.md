# UAS Mini Project — Communication Protocol
## Order Monitoring Laptop (REST API + n8n Reliability Workflow)

**Nama** : Galih Agung Nurfadhilah
**NIM** : 25110500026
**Kelas** : Sains Data — Semester 2 / Kelas Professional
**Mata Kuliah** : Communication Protocol (ComP2)
**Dosen Pengampu** : Haikal Shiddiq, S.Kom., M.T.

---

## 1. Deskripsi Project

Project ini mensimulasikan sistem **monitoring pesanan dan transaksi pembelian laptop** pada skenario e-commerce. Studi kasus: pembeli bernama **Galih** membeli laptop **ASUS TUF A15 Ryzen 7** seharga **Rp14.500.000**, mulai dari pembuatan order, proses, pengiriman, hingga pembayaran via **ShopeePay**.

Selain REST API murni, project ini menambahkan **reliability layer** menggunakan **n8n webhook workflow**, yang menerima request, menormalisasi data, memanggil Reliability API, mengecek status code, lalu membentuk response sukses/gagal.

## 2. Protocol yang Digunakan

- **REST / HTTP** — untuk operasi CRUD pada resource `Orders` dan `Transactions`.
- **Webhook (via n8n)** — untuk orchestration dan reliability check sebelum response dikirim ke client.

## 3. Endpoint / Action

| No | Method | Endpoint | Fungsi |
|----|--------|----------|--------|
| 1 | GET | `/orders` | Melihat daftar pesanan |
| 2 | POST | `/orders` | Membuat pesanan baru |
| 3 | PUT | `/orders/{id}` | Update status pesanan (processing) |
| 4 | PATCH | `/orders/{id}` | Update sebagian status pesanan (shipped) |
| 5 | GET | `/transactions` | Melihat daftar transaksi |
| 6 | POST | `/transactions` | Membuat transaksi pembayaran |
| 7 | DELETE | `/transactions/{id}` | Menghapus transaksi |
| 8 | POST (Webhook) | `/webhook/reliability` | Trigger workflow n8n reliability check |

## 4. Alur CRUD (Demo Flow)

1. GET Orders → melihat daftar pesanan.
2. POST Order → Galih membeli ASUS TUF A15 Ryzen 7 (Rp14.500.000).
3. GET Orders → status pesanan: `created`.
4. PUT Order → penjual memproses pesanan → status `processing`.
5. GET Orders → konfirmasi status `processing`.
6. PATCH Order → pesanan dikirim → status `shipped`.
7. GET Orders → konfirmasi status `shipped`.
8. GET Transactions → melihat daftar transaksi.
9. POST Transaction → pembayaran ShopeePay berhasil → status `paid`.
10. GET Transactions → transaksi baru muncul.
11. DELETE Transaction → menghapus salah satu transaksi.
12. GET Transactions → memastikan transaksi sudah terhapus.

## 5. Reliability Workflow (n8n)

| Node | Status |
|------|--------|
| Webhook menerima request | ✅ |
| Normalize Request | ✅ |
| Call Reliability API | ✅ |
| Check Status Code < 400 | ✅ (True Branch) |
| Build Success Response | ✅ |
| Respond Success | ✅ |

## 6. Testing Scenario

**Success:**
- POST Order berhasil → `201 Created`
- POST Transaction (ShopeePay) berhasil → `200/201`, status `paid`

**Failure/Error:**
- GET Order dengan ID tidak ada → `404 Not Found`
- POST Order dengan body tidak lengkap/format salah → `400 Bad Request`

## 7. Observability

- Response body & status code tiap request (Postman)
- Execution history n8n (6 node di atas, lengkap dengan waktu eksekusi)
- Wireshark capture / traffic observation — lihat `evidence/wireshark-capture.png` (catatan limitation jika payload tidak terbaca karena HTTPS/TLS atau localhost)

## 8. Struktur Folder

```
uas-commprotocol-nama-nim/
├─ README.md
├─ docs/
│  ├─ architecture.png
│  ├─ data-flow.png
│  ├─ laporan-uas.pdf
│  └─ slides-uas.pdf
├─ postman/
│  └─ collection.json
├─ n8n/
│  └─ workflow.json
├─ app/
├─ evidence/
│  ├─ success-01.png
│  ├─ success-02.png
│  ├─ failure-01.png
│  ├─ failure-02.png
│  ├─ observability-log.png
│  └─ wireshark-capture.png
```

## 9. Keterbatasan (Limitation)

[Isi sesuai kondisi asli, misalnya: traffic diuji pada localhost/HTTP sehingga payload di Wireshark terlihat tanpa enkripsi TLS; reliability check baru diuji pada True Branch, False Branch belum diuji end-to-end, dll.]

## 10. Pernyataan Orisinalitas

Seluruh artefak, evidence, repository, laporan, slide, dan video demo pada project ini dibuat sendiri oleh [Nama Lengkap] — [NIM], tanpa menampilkan token, password, API key, credential, atau data pribadi.
