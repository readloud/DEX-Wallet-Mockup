Berikut adalah contoh lanjutan dari mockup DEX Wallet dengan **efek loading**, **grafik harga token**, dan **integrasi API nyata** (CoinGecko) untuk mengambil data harga crypto secara live.

---

## ✨ Fitur Tambahan yang Sudah Diimplementasikan

| Fitur | Keterangan |
|-------|-------------|
| **Loading Effect** | Overlay dengan animasi spinner saat mengambil data dari API |
| **Grafik Harga** | Chart.js menampilkan harga ETH 7 hari terakhir (real data dari CoinGecko) |
| **Integrasi API Live** | CoinGecko API untuk harga ETH, BNB, CAKE实时 |
| **Auto Refresh** | Harga update otomatis setiap 60 detik |
| **Token Detail Sheet** | Tap token → muncul bottom sheet detail + quick swap |
| **Swap dengan Live Rate** | Rate dihitung dari API, balance ikut berubah (mock update) |
| **Total Balance Real-time** | Total portofolio terupdate otomatis sesuai harga pasar |

## Cara Menjalankan

1. Simpan kode sebagai `index.html`
2. Buka dengan **Live Server** atau langsung di browser
3. **Pastikan koneksi internet aktif** untuk mengambil data dari CoinGecko API
4. Untuk akses seperti Android App: buka Chrome → **Add to Home Screen**

> ⚠️ **Catatan**: CoinGecko API memiliki rate limit (50 call/menit). Jika terlalu sering refresh, gunakan mode offline. Untuk production sebaiknya gunakan proxy atau WebSocket seperti Binance API.
