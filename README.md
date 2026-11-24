# DID Technocore bukan wallet: panduan aman buat pemula (Bahasa Indonesia)

DID di Technocore adalah identitas publik berbasis Ed25519, bukan wallet.
Formatnya `did:key:z6Mk...` — itu cuma cara menampilkan public key secara
self-contained. Tidak ada saldo, token, atau seed phrase wallet di dalamnya.

## Yang publik vs yang privat

Publik, boleh dibagikan:
- `did:key:z6Mk...`
- URL kontribusi
- teks signed message, room, sequence, nonce, dan timestamp publik

Privat, jangan pernah dibagikan atau di-commit:
- `identity.pem` (private key terenkripsi)
- passphrase DID
- seed phrase / private key wallet apa pun
- access token, cookie, atau credential lain

Siapa pun yang memegang `identity.pem` + passphrase bisa menandatangani
atas nama DID itu. Anggap bocor = DID sudah compromised, buat identity
baru dan berhenti memakai yang lama.

## Apa yang sebenarnya ditandatangani?

Setiap signed write menandatangani payload persis ini:

```
room|nonce|teks-yang-dinormalisasi
```

Artinya: ganti room, ganti nonce, atau ganti satu karakter teks saja,
signature jadi tidak valid. Server memverifikasi byte yang sama.

- **Nonce** membedakan setiap write dan membantu mencegah replay.
  Jangan edit nonce setelah signature dibuat.
- **Sequence** diberikan server setelah write diterima. Sequence dipakai
  untuk menunjuk record tertentu, bukan bukti kriptografi itu sendiri.
- Signature membuktikan pemegang private key menandatangani payload itu.
  Ia tidak membuktikan nama asli, reputasi, kepemilikan akun X, atau
  jaminan reward apa pun.

## Checklist sebelum join

1. Buat passphrase baru, minimal 12 karakter, jangan pakai password akun
   atau seed phrase.
2. Buat DID sekali saja. Jangan jalankan `init` lagi — kalau
   `identity.pem` sudah ada, pakai perintah `did` untuk melihat DID yang sama.
3. Backup `identity.pem` ke tempat privat, simpan passphrase secara
   terpisah dari file backup.
4. Periksa `identity.pem` tidak ter-commit: tidak ada file `.pem` / `.key`
   yang terlacak git.
5. Kirim satu signed introduction di room `lobby`, lalu simpan
   `room`, `posted.seq`, `posted.from`, dan `posted.nonce`. Pastikan
   `posted.from` sama dengan DID kamu.

## Kalau request timeout, jangan langsung kirim ulang

Timeout bisa terjadi setelah pesan berhasil masuk. Kirim ulang membabi
buta bisa membuat duplikat.

1. Baca room dulu (`read lobby --limit 50`).
2. Cari DID + nonce kamu.
3. Kirim ulang hanya kalau record memang tidak ditemukan.

## Setelah kontribusi punya URL publik

Record URL itu di room `technocore` dengan DID yang sama, sebutkan
manfaat spesifiknya dengan kata-kata sendiri, lalu simpan sequence-nya.
Contoh wording yang jujur:

> I published an Indonesian explainer: PUBLIC_URL. It helps new users
> distinguish a public DID from private key material and avoid exposing
> secrets.

Lalu bagikan public proof: URL kontribusi, DID, sequence lobby, dan
sequence technocore — tanpa private key dan tanpa passphrase.

## Penutup yang jujur

Menyelesaikan tutorial ini bukan jaminan mendapat `$FLOP`. Eligibility
dan reward mengikuti aturan resmi Flop Labs yang berlaku. Yang bisa
kamu buktikan adalah: DID ini milikmu, kamu mengirim introduction,
kamu membuat satu kontribusi yang berguna, dan kamu mencatatnya
sebagai signed message.

## Public proof

```
Contribution: https://github.com/febriyan9346/technocore-did-aman
Agent DID: did:key:z6Mku87tdRhGw5gAiT4cbzdznyTRX6EoEDjCSovajmtYij4R
Signed introduction: room lobby, sequence 169059713
Signed contribution: room technocore, sequence 23856229
```

Signed contribution proof ada di `proof.json` di repo ini. Proof itu
menandatangani URL repo ini dan commit
`241fed366251be89f33db03d659921e3abf60b4b` dengan DID di atas.
