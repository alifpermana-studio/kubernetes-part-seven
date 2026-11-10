# Penggabungan Motion Graphic Kubernetes Part 7 (`Full.html` & `index.html`)

Penggabungan utuh seluruh 8 scene motion graphic edukasi **Kubernetes Part 7 (Troubleshooting Pod & Probes)** beserta **Outro Logo Reveal Alif Pustaka** ke dalam satu file **Vanilla HTML5 mandiri (`Full.html` & `index.html`)** beresolusi vertikal `1080×1920` (30 FPS) dengan penyelarasan **Dark Tech Theme (`K`)**, koreksi simetri koordinat terhadap sumbu tengah kanvas, jeda baca 2 detik antar-scene, dan fitur ekspor video MP4 (`Mediabunny` + `MediaRecorder` fallback) serta subtitle `.srt`.

> **User Review & Critical Decisions**
> - **Preservasi 100% Animasi Bawaan**: Seluruh bentuk objek, maskot koki/inspektur, kartu diagram, teks layar, *keyframe*, dan kurva *easing* asli dari Scene 1 hingga Scene 8 + Outro dipertahankan utuh di dalam *namespace* terisolasi (`renderScene1` .. `renderScene8`, `renderOutro`).
> - **Konversi Sinkron Scene 3 ke Canvas 2D**: Scene 3 yang awalnya menggunakan primitif string SVG (`R, O, P, L, T, chef, insp, ecg`) diterjemahkan langsung ke primitif HTML5 Canvas 2D (`scale(2, 2)`) agar *rendering* 30 FPS dan perekaman video MP4 berjalan mulus tanpa *flicker* atau ketergantungan DOM eksternal.
> - **Pemuatan Audio Hibrida**: Voiceover tiap scene dimuat langsung dari file audio lokal (`voiceover-scene-1.wav` s.d. `voiceover-scene-8.wav`, termasuk `voiceover-scene-6.mp3`) melalui Web Audio API dengan *fallback* otomatis agar tetap berjalan di server preview maupun saat dibuka mandiri.

---

## 1. Overview & Core Concept

Aplikasi ini menyatukan 8 partisi animasi penjelasan *Troubleshooting Kubernetes* dan 1 *Outro* identitas visual **Alif Pustaka** menjadi satu pengalaman pemutar video *motion graphic* interaktif berbasis `<canvas width="1080" height="1920">`:

- **Scene 1 (`renderScene1`)**: Pengenalan 4 status masalah utama Pod (`Pending`, `OOMKilled`, `CrashLoopBackOff`, dan `Evicted`) beserta simulasi *alert* pukul 03.00 pagi, antrean penjadwalan VPS, batas memori container, *exponential backoff*, dan tekanan disk node.
- **Scene 2 (`renderScene2`)**: Analogi 3 koki di dapur restoran untuk menjelaskan efek domino *overload* trafik dan pentingnya memutus rantai kegagalan sebelum seluruh Pod tumbang.
- **Scene 3 (`renderScene3`)**: Peran `livenessProbe`, `startupProbe`, dan `readinessProbe` melalui ilustrasi koki pingsan, inspektur kesehatan dengan grafik detak jantung (ECG), serta perbandingan ketiga *probe* berdampingan.
- **Scene 4 (`renderScene4`)**: Simulasi *Chaos Engineering* dan *Self-Healing* saat satu Pod dihapus paksa (`kubectl delete pod`) dan `ReplicaSet` memulihkan jumlah replika secara otomatis.
- **Scene 5 (`renderScene5`)**: Penjelasan teknis *CPU Throttling* (kuota `100m` pada jendela waktu `100ms` Linux CFS) dan kegagalan resolusi DNS internal (`CoreDNS` / `ndots:5`).
- **Scene 6 (`renderScene6`)**: Proses *Graceful Shutdown* (`SIGTERM`, `preStop` hook `sleep 5`, penghapusan endpoint `kube-proxy` secara paralel, hingga `SIGKILL`).
- **Scene 7 (`renderScene7`)**: Alur kerja sistematis *Troubleshooting* 4 langkah (`kubectl get pods`, `describe pod`, `logs --previous`, `top pod/node`) beserta pohon keputusan diagnostik.
- **Scene 8 (`renderScene8`)**: Rekapitulasi arsitektur pertahanan berlapis (*Defense-in-Depth*), ukuran *requests/limits* yang presisi, beban kerja *vector search*, kelas QoS (`Guaranteed`, `Burstable`, `BestEffort`), dan penutup seri.
- **Outro (`renderOutro`)**: Animasi penutup logo **Alif Pustaka** (10 *path* ikonik beranimasi *spring* + 11 *path* tipografi `"alifpustaka"`, partikel radial, gelombang cincin, teks *typewriter* `"Ruang Literasi, Rumah Inovasi"`, dan kapsul URL `https://alifpustaka.web.id`).

---

## 2. User Experience & Visual Design

- **Kanonikal Dark Tech Theme (`K`)**:
  - Seluruh scene menggunakan latar belakang kanvas seragam melalui `drawThemeBg(g)` dengan warna dasar `#0e1626` (`K.bg`) dan dua lingkaran dekoratif halus `#13203a` (`K.bg2`) di sudut kanan atas `(980, 160, r=300)` serta kiri bawah `(60, 1800, r=360)`.
  - Seluruh permukaan kartu dan panel menggunakan `#172235` (`K.ink`) dan `#22314d` (`K.surf`) dengan teks utama `#F6F5EE` (`K.iv`), teks sekunder `#73819A` (`K.sl`), serta aksen semantik `#4667FF` (`K.cob`), `#16B99A` (`K.teal`), `#FF765E` (`K.cor`), `#FFC65C` (`K.amb`), `#A3A5F4` (`K.peri`), dan `#E5484D` (`K.red`).
  - Kontras teks dijaga ketat: semua teks yang sebelumnya berwarna gelap di atas latar terang diubah menjadi `#F6F5EE` (`K.iv`), sedangkan teks di dalam *badge* terang (`K.amb` atau `K.iv`) tetap menggunakan `#172235` (`K.ink`).
- **Perbaikan Simetri Sumbu Tengah (`x = 540` / `270` / `180`)**:
  - **Scene 1**: Penyeimbangan posisi grup server/VPS pada sub-scene `Pending` dan `Evicted` agar margin kiri dan kanan seimbang terhadap `x = 540`, perataan 3 kolom metrik pada `OOMKilled` (`x = 240, 540, 840`), serta penempatan blok grafik `CrashLoopBackOff` tepat di tengah kanvas dengan isi bar progres tetap tumbuh rata kiri.
  - **Scene 2**: Penyelarasan pusat *radial wipe* (`WC`) ke `(270, 480)`, perataan barisan 6 domino di sub-scene pembuka agar simetris terhadap `x = 270`, perataan garis cabang trafik *Service* ke Pod 2 dan Pod 3, serta penyeimbangan sakelar *circuit breaker* di sub-scene penutup.
  - **Scene 3**: Penyelarasan titik pusat transisi (`TP`) ke `(270, 480)`, penempatan grup pintu dapur, koki, inspektur, dan kartu komparasi 3 *probe* (`liveness`, `readiness`, `startup`) simetris terhadap `x = 270`.
  - **Scene 4**: Penyelarasan panah trafik masuk, titik antrean (*queue dots*), dan formasi 3 maupun 4 Pod tepat terhadap sumbu `x = 180` (skala `3×`), serta pusat transisi *radial wipe* di `(180, 320)`.
  - **Scene 5**: Penyeimbangan dua kartu jendela CFS (`100ms`) di bagian atas dan diagram resolusi `Pod -> CoreDNS` di bagian bawah agar simetris terhadap sumbu tengah panel (`x = 490` di dalam kontainer lebar `980`).
  - **Scene 6**: Penyelarasan pusat transisi *radial wipe* (`W`) ke `(540, 960)`, kanopi restoran, antrean 4 pelanggan, 3 kolom proses paralel (`210, 540, 870`), dan garis waktu `0..6s`.
  - **Scene 7**: Penyelarasan pusat transisi *radial wipe* (`CS`) ke `(540, 960)`, posisi 5 koki (`180, 360, 540, 720, 900` dengan titik pusat karakter tepat di `x`), 3 indikator *gauge* (`220, 540, 860`), dan pohon keputusan diagnostik.
  - **Scene 8**: Penyelarasan pusat transisi (`WP`) ke `(270, 480)`, lencana nomor urut lapisan pertahanan, 3 kotak limit Pod (`65, 205, 345`), gugus titik *vector search* di `(270, 520)`, serta *track* bar QoS di tengah kanvas.
- **Kapsul Subtitle Seragam (`drawSubtitleOverlay`)**:
  - Ditampilkan di bagian bawah kanvas (`x = 60`, `w = 960`, `r = 28`) dengan latar `#000000` (`alpha = 0.68`), garis tepi `#22314d` (`K.surf`), dan teks putih gading `#F6F5EE` (`K.iv`) rata tengah di `x = 540`.
- **Kontrol Pemutar Lengkap**:
  - Tombol `Play/Pause`, `Reset`, *seekbar* interaktif dengan indikator waktu `MM:SS / MM:SS`, tombol loncat cepat (`Scene 1`..`Scene 8` + `Outro`), *checkbox* pengatur kanal (`Narasi`, `Musik`, `Efek`, `Subtitle`), tombol unduh `.srt` gabungan, dan tombol `Download Video (MP4)`.

---

## 3. Key Product Decisions & Trade-Offs

- **Jeda Baca 2 Detik per Scene (`SCENE_DELAY = 2.0`)**:
  - Durasi total setiap scene dihitung sebagai `DURS[i] = VO_DURS[i] + 2.0` detik, sedangkan Outro berdurasi `6.0` detik (`4.0s` animasi + `2.0s` tahan visual).
  - Selama jeda 2 detik di akhir tiap scene, waktu struktural animasi di-*clamp* pada `tClamped = Math.min(localT, VO_DURS[i] - 0.001)` agar kartu dan teks kesimpulan tetap diam dan nyaman dibaca, sementara animasi *ambient* (partikel, efek bernapas karakter, gelombang latar) dan musik tetap mengalir hidup.
  - Cue subtitle terakhir pada setiap scene diperpanjang `+1.8` detik ke dalam masa jeda baca.
- **Arsitektur Ekspor Video Ganda (`Mediabunny` + `MediaRecorder`)**:
  - Menggunakan modul `Mediabunny` (`CanvasSource` + `AudioBufferSource` H.264/AAC) untuk merender MP4 30 FPS secara deterministik, dilengkapi *fallback* otomatis ke `MediaRecorder` real-time jika lingkungan browser membatasi WebCodecs.

---

## 4. Technical Architecture & Data Strategy

```
┌────────────────────────────────────────────────────────────────────────────┐
│                   Full.html / index.html (Single-File App)                 │
├────────────────────────────────────────────────────────────────────────────┤
│  1. Master Timeline & State Engine (30 FPS / 1080×1920)                    │
│     ├── VO_DURS[0..7] (Durasi asli voiceover Scene 1..8)                   │
│     ├── SCENE_DELAY = 2.0s  ──►  DURS[i] = VO_DURS[i] + 2.0s               │
│     ├── D9 (Outro) = 6.0s   ──►  STARTS[0..8], TOTAL_DUR                   │
│     └── Global t ──► Active Scene Index (idx) & localT = t - STARTS[idx]   │
├────────────────────────────────────────────────────────────────────────────┤
│  2. Namespaced Canvas 2D Renderers & Theme Layer                           │
│     ├── drawThemeBg(g)          : Latar #0e1626 + 2 lingkaran #13203a      │
│     ├── renderScene1..8(...)    : tClamped untuk struktur, localT ambient  │
│     ├── renderOutro(g, t, D9)   : Path2D Logo + Wordmark Alif Pustaka      │
│     └── drawSubtitleOverlay(g)  : Kapsul subtitle bawah + word-wrap        │
├────────────────────────────────────────────────────────────────────────────┤
│  3. Web Audio API Synthesizer, Voiceover Loader & Exporter                 │
│     ├── Fetch voiceover-scene-1..8 (.wav/.mp3) + AudioBuffer decode        │
│     ├── Procedural BGM Loop + Scene SFX (whoosh, pop, alert, chime)        │
│     └── Exporter: Mediabunny MP4 Muxer / MediaRecorder + .SRT Generator    │
└────────────────────────────────────────────────────────────────────────────┘
```

- **Sinkronisasi Timeline Global**:
  - Array `STARTS` menyimpan titik awal kumulatif dari Scene 1 hingga Outro berdasarkan `DURS`.
  - Ketika pengguna menggeser *seekbar* atau menekan tombol `Scene 1`..`Outro`, pemutar menghitung ulang `activeScene`, menghentikan sumber audio sebelumnya, dan memulai pemutaran `AudioBufferSourceNode` pada *offset* `localT` yang tepat (jika `localT < VO_DURS[i]`).
- **Penyediaan Halaman Utama Preview (`index.html`)**:
  - Selain disimpan sebagai `/Full.html`, isi yang sama disediakan pada `/index.html` agar server preview port 3000 langsung menampilkan pemutar utuh tanpa perlu navigasi manual.
