<div align="center">

<img src="banner.png" alt="Mazkiplay_Nusantara banner" width="100%">

<a href="#-quick-start"><img src="https://img.shields.io/badge/STATUS-OPERASIONAL-00ffa3?style=for-the-badge&labelColor=050d0b" alt="status"></a>
<img src="https://img.shields.io/badge/VERSI-4.0.0-00c8ff?style=for-the-badge&labelColor=050d0b" alt="versi">
<img src="https://img.shields.io/badge/PLATFORM-ANDROID%2026%2B-3ddc84?style=for-the-badge&labelColor=050d0b" alt="platform">
<img src="https://img.shields.io/badge/TRANSPORT-MQTT%2FWSS-a06bff?style=for-the-badge&labelColor=050d0b" alt="transport">
<img src="https://img.shields.io/badge/LISENSI-MIT-ffb020?style=for-the-badge&labelColor=050d0b" alt="lisensi">

<img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=700&size=20&pause=900&color=00FFA3&center=true&vCenter=true&width=780&lines=Panel+kontrol+perangkat%2C+bukan+sekadar+dashboard.;Kamera+%C2%B7+Layar+%C2%B7+GPS+%C2%B7+Audio+%C2%B7+Dump+data.;Build+APK+payload+langsung+dari+browser.;Relay+MQTT+real-time+%E2%80%94+tanpa+server+sendiri." alt="typing">

<a href="#-quick-start">Mulai</a> ·
<a href="#-arsitektur">Arsitektur</a> ·
<a href="#-protokol">Protokol</a> ·
<a href="CHANGELOG.md">Changelog</a> ·
<a href="LICENSE">Lisensi</a>

</div>

---

> **Mazkiplay_Nusantara** adalah panel administrasi jarak jauh berbasis web untuk perangkat Android.
> Satu berkas `index.html`, tanpa backend, tanpa database — dan tetap bisa membangun payload APK
> yang mengumpulkan data nyata dari perangkat target.

<br>

## ✦ Ikhtisar

Proyek ini menggabungkan dua sisi yang biasanya terpisah: **panel operator** dan **pabrik payload**.

- Panel berjalan sepenuhnya di browser (GitHub Pages, HTTPS) dan memakai API perangkat secara langsung — kamera, mikrofon, perekam layar, geolokasi, baterai, jaringan.
- Payload APK dihasilkan oleh *builder* bawaan: nama, ikon, HTML tampilan, dan kode akses disuntikkan ke dalam project Gradle lengkap, lalu dikompilasi oleh **GitHub Actions**.
- Keduanya berbicara lewat **relay MQTT publik** dengan topik per-ruangan. Tidak ada angka karangan: setiap nilai yang tampil berasal dari perangkat.

<br>

## ✦ Kemampuan

| Modul | Panel (browser) | Payload (APK) |
|---|:---:|:---:|
| Kamera depan / belakang | ● | ● |
| Perekam audio | ● | ● |
| Cermin layar | ● | ○ |
| Pelacakan GPS | ● | ● |
| Kontak | ● ¹ | ● |
| SMS masuk | ○ | ● |
| Log panggilan | ○ | ● |
| Notifikasi | ○ | ● |
| Galeri (thumbnail) | ○ | ● |
| Info perangkat, baterai, jaringan | ● | ● |
| Keylogger | ● | ● |
| Getar · lampu · toast · buka URL | ● | ● |
| Kunci perangkat + layar kunci kustom | — | ● |
| Anti-uninstall (Device Admin) | — | ● |
| Sembunyikan ikon · autostart | — | ● |

`●` aktif · `○` hanya lewat payload · ¹ Chrome Android via Contact Picker API

<br>

## ✦ Arsitektur

```mermaid
flowchart LR
    A["Operator<br/>index.html"] -- "publish cmd" --> R(("Relay MQTT<br/>mzn/ROOM/DEV/down"))
    R -- "subscribe" --> B["Payload APK<br/>Agent service"]
    B -- "publish data" --> U(("mzn/ROOM/DEV/up"))
    U -- "subscribe" --> A
    B --- C["CameraX"]
    B --- D["ContentResolver"]
    B --- E["LocationManager"]
    B --- F["NotificationListener"]
    B --- G["DevicePolicyManager"]
