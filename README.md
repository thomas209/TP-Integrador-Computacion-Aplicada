# TP Integrador grupal — Computación Aplicada (UP, 2º cuatrimestre 2026)

## Integrantes
- Thomas Caronia
- _(completar)_
- _(completar)_
- _(completar)_
- _(completar)_

## Contenido del repositorio
| Archivo | Qué es |
|---|---|
| `root.tar.gz` | Directorio `/root` (home de root: index.php, logo.png, db.sql, material) |
| `etc.tar.gz` | Directorio `/etc` (red estática, fstab, Apache, MariaDB) |
| `opt.tar.gz` | Directorio `/opt` (script `/opt/scripts/backup_full.sh`) |
| `www_dir.tar.gz` | Partición `/www_dir` (sitio web: index.php + logo.png) |
| `backup_dir.tar.gz` | Partición `/backup_dir` (backups generados por el script) |
| `var.tar.gz.part-00` … `var.tar.gz.part-03` | Directorio `/var` comprimido y dividido en partes de 9 MB |

Para reconstruir `/var`:
```bash
cat var.tar.gz.part-* > var.tar.gz
tar -xzf var.tar.gz
```

## Resumen de lo realizado
1. **Entorno:** VM Debian 11 importada; contraseña de root blanqueada desde GRUB (`init=/bin/bash` + `passwd`) y cambiada a `palermo`.
2. **Web y base de datos:** Apache 2.4 + PHP 7.4 (`libapache2-mod-php`, `php-mysql`); MariaDB 10.5 con `db.sql` cargado (base `ingenieria`, usuario `lcars`).
3. **Red:** IP estática `192.168.0.200/24`, gateway `192.168.0.1`, en `/etc/network/interfaces` (address / netmask / gateway).
4. **Almacenamiento:** disco nuevo de 10 GB con 2 particiones primarias tipo 83: `/www_dir` (3 GB) y `/backup_dir` (6 GB), ext4, montaje automático por UUID en `/etc/fstab`. Apache apunta a `/www_dir` (`000-default.conf` + bloque `<Directory /www_dir/>` en `apache2.conf`).
5. **Backup:** `/opt/scripts/backup_full.sh ORIGEN DESTINO` (opción `-help`, valida que origen y destino estén disponibles/montados, genera `<nombre>_bkp_YYYYMMDD.tar.gz`). Cron de root:
   - `0 0 * * *` → backup de `/var/log` en `/backup_dir`
   - `0 23 * * 1,3,5` → backup de `/www_dir` en `/backup_dir`
