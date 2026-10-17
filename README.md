# kaggle-ssh

SSH langsung ke **notebook Kaggle GPU** — GPU gratis (P100/T4), host tetap via Portwarp, fallback pinggy. Pola sama kayak Frepees, bedanya `nvidia-smi` idup.

| | isi |
|---|---|
| GPU | Tesla P100 / T4 (Settings → Accelerator → GPU) |
| Kuota | **30 jam/minggu** (reset tiap Senin) |
| Sesi | max 12 jam, kena kill kalo idle → pakai cell keep-alive |
| Auth | user `root`, password acak `kgpu...` (muncul tiap run) |

## Pakai (3 menit)

1. Buka [kaggle.com/code](https://kaggle.com/code) → **New Notebook**.
2. **Settings → Accelerator: GPU**, **Internet: On**.
3. Upload `kaggle-ssh.ipynb` (Add Data → upload notebook) — atau bikin cell `%%bash` terus tempel isi `setup.sh`.
4. **Run cell pertama** → baca output:
   ```
   USER root
   PASS kgpu.............
   PINGGY_HOST ....pinggy-free.link
   PINGGY_PORT ....
   PORTWARP ....free.pwrp.cc:....   (kalo diisi, lihat bawah)
   ```
5. **Terminus (HP)** → New Host → `root` / `PASS` / host+port dari `PINGGY_*`.
6. **Run cell keep-alive** (cell ke-2) — biar sesi gak idle-timeout. Biarin jalan.
7. URL pinggy ganti tiap ±60 menit → **run cell refresh** (cell ke-3) buat URL baru, reconnect di Terminus.

## Portwarp (host tetap, opsional)

Bikin cell `%%bash` kecil sebelum setup, atau edit cell setup — isi:

```bash
export PW_CRED_B64=$(base64 -w0 ~/.portwarp/credentials.json)
```

(`base64 -w0` jalanin di Termux/HP tempat credentials.json Frepees ada.)

⚠️ **Plan gratis Portwarp cuma boleh1 tunnel.** Kalau Frepees VPS lagi jalan (hold 6 jam), tunnel-nya dipakai berdua → endpoint bakal saling rebutan. Pilih salah satu online:

- Frepees jalan → Kaggle cukup pakai pinggy (otomatis, gak perlu ngapa-ngapain).
- Mau Kaggle host tetap → cancel run Frepees dulu, baru isi `PW_CRED_B64`.

## Batasan (biar gak kaget)

- **Session limit12 jam** → keluaran "session disconnected", tinggal run ulang cell setup (password & URL baru tiap kali).
- **GPU quota30 jam/minggu** → [kaggle.com/settings](https://www.kaggle.com/settings) buat lihat sisa.
- Idle ~1 jam tanpa activity → putus. Cell keep-alive ngehindarin ini.
- Notebook = sandbox: file yang lo bikin di `/root` hilang pas sesi mati. Yang penting simpen di `/kaggle/input` atau push ke repo.
- Kaggle nge-blok beberapa port keluar? gak — ssh over443 (pinggy) jalan normal.

## Files

- `setup.sh` — isi cell `%%bash` (bisa juga: `bash setup.sh` di terminal Kaggle)
- `kaggle-ssh.ipynb` — notebook siap upload (setup + keep-alive + refresh)
