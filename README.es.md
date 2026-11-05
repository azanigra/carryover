# carryover

Lleva tu [Omarchy](https://omarchy.org) a una máquina nueva: instala Omarchy desde cero y luego trae tu carpeta personal, tus paquetes y tus redes Wi-Fi guardadas.

*[Read in English](README.md)*

> Proyecto no oficial. No está afiliado al proyecto Omarchy.

## ¿Por qué no clonar el disco?

El cifrado (LUKS), el arranque y los controladores dependen del hardware. Una instalación limpia de Omarchy configura todo eso bien para la máquina nueva; tu carpeta personal es lo que la hace *tuya*.

Un malentendido común: `omarchy-snapshot` **no** es un respaldo. Sus instantáneas de snapper viven en el mismo disco y solo regresan en el tiempo a *esa* máquina.

## Qué se lleva

| Archivo del respaldo | Contenido | Al restaurar |
| --- | --- | --- |
| `home.tar.zst` | Todo tu home: documentos, `~/.config`, tema, `~/.ssh`, `~/.gnupg`, llavero. Excluye `~/.cache` y la papelera | Se restaura, tras guardar aparte el `~/.config` recién instalado |
| `packages-repo.txt` | Paquetes que instalaste de los repositorios oficiales | Se instalan; los que ya no existen se listan y se omiten |
| `packages-foreign.txt` | Paquetes de AUR y los que compilaste tú | Los de AUR se instalan con `yay`; los locales se listan para reconstruir |
| `system.tar.zst` | Redes Wi-Fi guardadas y archivos de `/etc` que modificaste | Wi-Fi se restaura; `/etc` solo se muestra, nunca se aplica a ciegas |
| `info.txt`, `SHA256SUMS` | Equipo, usuario, fecha, versión de Omarchy; sumas | Para avisar de diferencias y rechazar respaldos dañados |

Usa `tar` + `zstd` en vez de `rsync` directo, así los permisos, enlaces y ACL sobreviven aunque el USB esté en exFAT o NTFS.

## Descárgalo

```bash
git clone https://github.com/azanigra/carryover.git
cd carryover
```

## 1. Respalda la máquina vieja

```bash
omarchy-update                                # para que ambas máquinas coincidan en versión
ls /run/media/$USER/                          # busca el nombre de tu disco USB
./carryover backup /run/media/$USER/MI_DISCO
cp carryover /run/media/$USER/MI_DISCO/       # llévate carryover contigo
```

Córrelo **sin sudo**; solo pide tu contraseña para leer las redes Wi-Fi y `/etc`. Primero revisa que haya espacio. Desmonta el disco antes de desconectarlo.

## 2. Restaura en la máquina nueva

1. Instala Omarchy desde el ISO con **el mismo nombre de usuario** que antes y corre `omarchy-update`.
2. Conecta el disco y corre:

   ```bash
   cd /run/media/$USER/MI_DISCO
   ./carryover restore carryover-<equipo>-<fecha>
   ```

3. carryover verifica las sumas y se detiene si algo está dañado. Te avisa si el usuario o la versión de Omarchy no coinciden.
4. Luego recorre cuatro pasos y pregunta **s/N** antes de cada uno: paquetes de repositorios, paquetes de AUR, carpeta personal y Wi-Fi.
5. Reinicia.

La restauración mezcla: los archivos del respaldo reemplazan a los existentes, pero no se borra nada más de la máquina nueva.

## Límites

- **El respaldo no está cifrado.** Lleva tus llaves SSH y tu llavero; cuida el disco como cuidarías tu laptop, o usa un USB cifrado con LUKS.
- Es una foto de un momento, no un respaldo continuo. Para eso existen restic o borg.
- No incluye contenedores, máquinas virtuales ni bases de datos guardados fuera de tu home.
- Restaurar un `~/.config` de otra versión de Omarchy puede pisar configuraciones nuevas; actualiza ambas máquinas antes.

Los mensajes siguen el idioma del sistema: español si `LANG` empieza con `es`, inglés en otro caso.

## Licencia

[MIT](LICENSE) © 2026 Pablo Barrera
