# InfinityX Creek - Build via GitHub Actions (gratis, dari HP)

Setup ini buat build custom ROM InfinityX untuk device Creek (SM6225)
tanpa butuh PC/Linux sama sekali - semua proses build jalan di server
GitHub, kamu cuma perlu HP buat setup & pantau progress.

## Cara pakai

### 1. Bikin repo GitHub baru (harus PUBLIC biar build minutes gratis unlimited)
- Buka github.com dari browser HP
- New repository -> kasih nama misal `infinityx-creek-ci`
- Set **Public**

### 2. Upload 3 file dari folder ini ke repo tersebut
Struktur foldernya harus persis kayak gini di root repo:
```
.github/workflows/build.yml
local_manifests/roomservice.xml
README.md
```
Bisa upload lewat web GitHub (tombol "Add file" -> "Upload files") langsung dari HP,
gak perlu git command line.

### 3. WAJIB edit dulu sebelum build pertama
Buka `local_manifests/roomservice.xml`, edit langsung di GitHub (klik pensil/edit):
- Pastikan URL device tree & vendor tree bener (defaultnya nunjuk ke
  project-creek, cek dulu apakah itu yang masih aktif dipakai)
- **Ganti kernel tree** - ini masih placeholder, cari tau dulu repo kernel
  mana yang dipakai sekarang oleh tim Creek Lab
- Cek apakah butuh repo tambahan lain (misal common tree, extra hardware repo)

### 4. Jalanin build
- Buka tab **Actions** di repo kamu
- Pilih workflow **"Build InfinityX - Creek"**
- Klik **Run workflow**
- Isi:
  - DEVICE: `creek`
  - LUNCH_COMBO: sesuaikan sama nama lunch combo di device.mk kamu
    (misal `infinity_creek-userdebug`)
  - BUILD_TARGET: biasanya `bacon` (cek Makefile ROM buat mastiin)
  - CLEAN_BUILD: `false` (biarin false biar ccache kepake, kecuali ganti
    banyak hal besar)
- Klik tombol hijau **Run workflow**

### 5. Pantau & ambil hasil
- Klik run yang lagi jalan buat liat log real-time (bisa dari HP)
- Kalau sukses, hasil .zip ada di 2 tempat:
  - Tab **Actions** -> run tersebut -> bagian **Artifacts** (unduh manual)
  - Tab **Releases** di repo (otomatis ke-publish di sana juga)

## Batasan free tier yang perlu diinget
- Max 6 jam per job (`timeout-minutes: 350` di workflow ini)
- Kalau build kelamaan dan kepotong timeout, run ulang aja - ccache
  yang udah tersimpan bakal mempercepat build berikutnya
- Runner disk dibersihin dulu di step "Free up disk space" biar cukup
  buat AOSP, tapi kalau masih kurang, mungkin perlu kurangin target build
  (misal skip beberapa APK bawaan / GApps kalau ROM support opsi itu)

## Kalau build gagal
- Cek log di tab Actions, cari baris merah "FAILED" atau "error:"
- Kalau gagal pas `repo sync` -> biasanya salah URL/branch di roomservice.xml
- Kalau gagal pas compile -> biasanya device tree Creek belum
  disesuaikan sama struktur Makefile InfinityX (beda ROM base kadang
  beda konvensi), perlu di-patch manual
- Kalau ROM sukses dibuild tapi device bootloop pas diflash -> lanjut
  proses debug ramoops/dmesg seperti yang udah dibahas sebelumnya
