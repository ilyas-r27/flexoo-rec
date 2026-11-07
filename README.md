# Flexoo Service Order Dashboard

Submission Mini Project Challenge Flexoo Software House untuk role **Backend Developer**.

**Nama:** Muhammad Ilyas Rusdi

## Deliverables

| Item | Link |
|---|---|
| Desain UI/UX dan prototype (Figma) | [Buka di Figma](https://www.figma.com/design/ZKSMqjwUjWQuZXv8HJhbIZ/Untitled?node-id=0-1&t=neaeDEz79viLKEpX-1) |
| Rancangan database (ERD) | [flexoo-erd.md](./flexoo-erd.md) |

## Tentang Project

Service Order Dashboard adalah halaman client portal Flexoo yang dipakai client untuk melihat kondisi engagement yang sedang berjalan dan memesan layanan baru dari Flexoo Software House.

Fitur utama di halaman ini:

- **Overview**: ringkasan sprint aktif, metrik teknis (latency, CI/CD, code coverage, target release), alokasi budget retainer, dan info lead squad.
- **Service Catalog**: daftar layanan Flexoo yang bisa difilter per kategori, lengkap dengan tech stack dan status layanan untuk client tersebut.
- **Service Order**: client bisa langsung mengajukan order dari dashboard, misalnya request squad baru, ekspansi squad, menambah layanan ke retainer, request scope, atau jadwal audit.

## Rancangan Backend

Sebagai pelamar Backend Developer, fokus saya ada di rancangan data yang mendukung desain tersebut. Detail lengkapnya ada di [flexoo-erd.md](./flexoo-erd.md), yang berisi:

- ERD lengkap dalam Mermaid (langsung tampil di GitHub)
- Penjelasan setiap tabel, kolom, dan constraint
- Daftar enum dan alur status order
- Mapping setiap komponen UI ke sumber datanya
- Contoh query untuk card di dashboard
- Index dan catatan keputusan desain

Database dirancang untuk PostgreSQL dan terbagi menjadi 5 modul:

| Modul | Isi |
|---|---|
| Identity & Client | user, client, anggota client, profil staff |
| Service Catalog | kategori, layanan, tech stack |
| Ordering | order layanan, riwayat status, quotation |
| Delivery | engagement, squad, project, sprint, user story, release, metrik, meeting |
| Billing | retainer, pemakaian budget, invoice, pembayaran |

## Keputusan Desain Utama

1. **Satu tabel untuk semua jenis order.** Enam tombol aksi di UI (Request New Squad, Request Squad Expansion, Add to Retainer, Request Scope, Schedule Audit, Provision Team) disimpan di tabel `service_orders` dan dibedakan lewat `order_type`, sehingga alur review dan approval cukup satu.
2. **Angka di dashboard dihitung dari data mentah.** Progress sprint, utilisasi budget, burn rate, dan jumlah engineer tidak disimpan sebagai kolom terpisah, jadi tidak ada risiko data tidak sinkron.
3. **Budget dicatat sebagai ledger.** Pemakaian budget retainer disimpan per periode di `retainer_usages` sehingga setiap angka bisa ditelusuri asalnya.
4. **Riwayat status order tercatat penuh.** Setiap perubahan status masuk ke `order_status_histories` untuk keperluan audit dan timeline di halaman detail order.
5. **Data terisolasi per client.** Semua data transaksi terhubung ke `client_id` sehingga satu client tidak bisa melihat data client lain.
