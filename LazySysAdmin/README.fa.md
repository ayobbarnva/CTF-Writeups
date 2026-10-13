<div align="center">

# LazySysAdmin: 1

### گزارش حل چالش CTF از نوع Boot-to-Root

**شناسایی سرویس‌های لینوکس و ارتقای سطح دسترسی**

[English](README.md) | [فارسی](README.fa.md)

![Platform](https://img.shields.io/badge/Platform-VulnHub-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner--Intermediate-orange)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

</div>

---

## معرفی

LazySysAdmin: 1 یک چالش CTF از نوع Boot-to-Root و مناسب افراد مبتدی است که در وب‌سایت VulnHub منتشر شده است.

این چالش یک سرور لینوکسی را شبیه‌سازی می‌کند که به دلیل پیکربندی و مدیریت ضعیف سیستم، دارای آسیب‌پذیری‌های امنیتی است. هدف، شناسایی سرویس‌ها، کشف اطلاعات افشاشده، شناسایی آسیب‌پذیری‌ها، به‌دست‌آوردن دسترسی اولیه به سیستم و در نهایت دستیابی به سطح دسترسی root است.

## دانلود ماشین آزمایشگاهی

می‌توانید ماشین مجازی LazySysAdmin: 1 را از VulnHub دانلود کنید.

- **صفحه چالش:** [LazySysAdmin: 1 — VulnHub](https://www.vulnhub.com/entry/lazysysadmin-1,205/)
- **دانلود مستقیم:** [Lazysysadmin.zip](https://download.vulnhub.com/lazysysadmin/Lazysysadmin.zip)

**نرم‌افزار مجازی‌سازی:** VirtualBox یا VMware

**سطح دشواری:** مبتدی تا متوسط

## شناسایی شبکه

با استفاده از ابزار `netdiscover` دو دستگاه فعال را در شبکه محلی شناسایی کردیم:

- `192.168.43.220` — سیستم Kali Linux ما.
- `192.168.43.166` — ماشین هدف LazySysAdmin.

آدرس IP ماشین هدف برای ادامه بررسی‌ها `192.168.43.166` شناسایی شد.

![نتایج Netdiscover](screenshots/one.png)

## شناسایی سرویس SMB

از Nmap به همراه گزینه `-sV` برای شناسایی سرویس‌ها و نسخه‌های در حال اجرای آن‌ها روی ماشین هدف استفاده کردیم.

اسکن چند پورت باز را نشان داد که پورت‌های `139` و `445` نیز در میان آن‌ها بودند. این پورت‌ها معمولاً با سرویس SMB (Server Message Block) مرتبط هستند.

SMB یک پروتکل شبکه برای اشتراک‌گذاری فایل‌ها و منابع میان دستگاه‌هاست. تصمیم گرفتیم این سرویس را بررسی کنیم تا مشخص شود آیا منابع اشتراکی بدون احراز هویت قابل دسترسی هستند یا خیر.

اسکن همچنین سرویس‌های دیگری مانند SSH، HTTP، MySQL و IRC را شناسایی کرد که می‌توانستند در مراحل بعدی بررسی شوند.

دستور استفاده‌شده:

```bash
nmap -sV 192.168.43.166
