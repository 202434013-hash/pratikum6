# Praktikum Minggu 6 — Search, Sorting, dan Pagination

## Query Contract

Endpoint yang digunakan:

`GET /api/books`

| Parameter | Ketentuan | Default |
|---|---|---|
| `search` | Mencari berdasarkan `title` atau `isbn`, berupa string maksimal 100 karakter | Tidak ada |
| `sort` | `id`, `title`, `isbn`, atau `created_at` | `id` |
| `direction` | `asc` atau `desc` | `asc` |
| `page` | Integer minimal 1 | 1 |
| `per_page` | Integer 1–50 | 5 |

Pencarian dilakukan pada kolom judul buku (`title`) atau ISBN (`isbn`). Parameter sorting digunakan untuk menentukan kolom dan arah pengurutan. Pagination membatasi jumlah data yang ditampilkan dalam satu halaman.

## Verification

Pengujian dilakukan menggunakan Postman dengan server Laravel aktif di `http://127.0.0.1:8000`.

| Pengujian | Request | Hasil yang diharapkan |
|---|---|---|
| Pagination | `GET /api/books?page=1&per_page=2` | 200 OK |
| Search ditemukan | `GET /api/books?search=Working` | 200 OK, data ditemukan jika cocok dengan database |
| Search kosong | `GET /api/books?search=kata-yang-tidak-ada-12345` | 200 OK, data kosong |
| Sorting ascending | `GET /api/books?sort=title&direction=asc` | 200 OK, judul A–Z |
| Sorting descending | `GET /api/books?sort=title&direction=desc` | 200 OK, judul Z–A |
| Invalid sort | `GET /api/books?sort=password` | 422 Unprocessable Entity |
| Invalid per_page | `GET /api/books?per_page=51` | 422 Unprocessable Entity |

Catatan: hasil aktual setiap pengujian perlu diperiksa melalui status HTTP dan respons JSON di Postman. Status yang dicantumkan di atas merupakan hasil yang diharapkan, bukan klaim bahwa seluruh pengujian sudah dilakukan.

## Response Structure

Respons daftar buku menggunakan `BookResource::collection()` dengan pagination Laravel.

- **`data`**: berisi kumpulan data buku yang dikembalikan, seperti `id`, `title`, `isbn`, `available`, dan `created_at`, sesuai konfigurasi `BookResource`.
- **`links`**: berisi tautan navigasi halaman, seperti `first`, `last`, `prev`, dan `next`.
- **`meta`**: berisi informasi pagination, seperti `current_page`, `last_page`, `per_page`, dan jumlah data.

Struktur ini membantu klien API membaca data buku sekaligus mengetahui posisi halaman dan navigasi ke halaman lainnya.

## Decision

Sorting menggunakan allowlist, yaitu hanya mengizinkan kolom `id`, `title`, `isbn`, dan `created_at`.

Allowlist mencegah pengguna meminta pengurutan berdasarkan kolom yang tidak diizinkan. Parameter `direction` juga dibatasi pada `asc` dan `desc`. Dengan demikian, input pengguna divalidasi sebelum diteruskan ke query database.

Jika parameter `sort`, `direction`, atau `per_page` melanggar aturan validasi, Laravel mengembalikan respons `422 Unprocessable Entity` untuk request yang menggunakan `Accept: application/json`.

## AI Usage

- **Alat:** ChatGPT dan Antigravity IDE.
- **Prompt:** Meminta bantuan mengimplementasikan search, sorting, pagination, validasi parameter, dan menyusun dokumentasi praktikum Minggu 6.
- **Hasil yang digunakan:** Rekomendasi implementasi `index()` pada `BookController`, contoh request Postman, dan kerangka dokumentasi query contract serta verification.
- **Cara memeriksa:** Memeriksa kode controller, memastikan route `GET /api/books` terdaftar, menjalankan Laravel, lalu menguji request di Postman. Hasil diverifikasi melalui status HTTP, data JSON, urutan judul, dan metadata pagination.

Dokumentasi ini perlu diperbarui apabila hasil pengujian aktual atau implementasi aplikasi berbeda dari ketentuan di atas.
