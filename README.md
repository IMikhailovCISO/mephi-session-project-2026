# Сессионный проект «Безопасность GNU/Linux» — РЕД ОС 8

Студент: **M265269** | Дистрибутив: RED OS 8.0.3 (Standard Server Minimal), kernel 6.12 | Гипервизор: VirtualBox 7.2.22
Hostname: `mephi-2026.domain.local` | Сеть: NAT (DHCP, enp0s3)

| Раздел | Как выполнен | Артефакты |
|---|---|---|
| 0. Подготовка | VirtualBox, 2 диска (60 ГБ системный, 10 ГБ данные), минимальная установка без GUI | mephi-screenshot.png |
| 1. Установка/сеть | DHCP на enp0s3, hostname задан в установщике; ping 8.8.8.8 0% loss | ping.out |
| 2. Управление ПО | `dnf update`, `dnf install dnf-plugins-core nginx libcap-ng-utils acl`, tcpdump скачан `dnf download` и установлен через `rpm -Uvh` | dnf.out |
| 3. Файловые системы | `/dev/sdb` GPT, ext4 с меткой `MEPHI_WEB`, монтируется в `/mephi-web` через fstab (persist) | fstab, stat.out |
| 4. Сервисы | `systemctl enable --now nginx` (active, enabled) | journalctl.out |
| 5.1 DAC | группы team(5500)/curators(4444); `/data/mephi-2026` 2770 + setgid; ACL: default g::rwx, g:curators:r-x; user1 пишет, curator1 читает но не пишет, nobody — отказ | passwd, group, shadow |
| 5.2 Привилегии | setuid у tcpdump снят, доступ через `setcap cap_net_raw+ep`; запуск tcpdump под user1 успешен | getcap.out |
| 5.3 MAC/SELinux | SELinux Enforcing; `semanage fcontext -t httpd_sys_content_t '/mephi-web(/.*)?'` | getenforce.out |
| 6. Аутентификация | pam_access: `- : curator1 curator2 : ALL` (вход запрещён консоль и SSH); pwquality `minlen = 12`; `PASS_MAX_DAYS 90`, `chage -M 90` | pwquality.conf, passwd, shadow |
| 7. Тестирование | nginx root → `/mephi-web`; `curl http://localhost/` возвращает `Hello from Student: M265269`; reboot-контроль пройден | curl.out, mephi-screenshot.png |
| 8. GitHub | этот репозиторий | все файлы |
