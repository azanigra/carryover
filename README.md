# carryover

Move your [Omarchy](https://omarchy.org) setup to a new machine: install Omarchy fresh, then carry over your home folder, your packages and your saved Wi-Fi networks.

*[Leer en español](README.es.md)*

> Unofficial. Not affiliated with the Omarchy project.

## Why not just clone the disk?

Disk encryption (LUKS), the bootloader and drivers depend on the hardware. A fresh Omarchy install sets all of that up correctly for the new machine; your home folder is what makes it *yours*.

Also worth knowing: `omarchy-snapshot` is **not** a backup. Its snapper snapshots live on the same disk and only roll *that* machine back in time.

## What it carries

| File in the backup | Contents | On restore |
| --- | --- | --- |
| `home.tar.zst` | Your whole home: documents, `~/.config`, theme, `~/.ssh`, `~/.gnupg`, keyring. Skips `~/.cache` and the trash | Restored, after saving the fresh `~/.config` aside |
| `packages-repo.txt` | Packages you installed from the official repos | Installed; ones no longer in the repos are listed and skipped |
| `packages-foreign.txt` | AUR packages and ones you built yourself | AUR ones installed with `yay`; local builds listed for you to rebuild |
| `system.tar.zst` | Saved Wi-Fi networks and the `/etc` files you modified | Wi-Fi restored; `/etc` only shown, never applied blindly |
| `info.txt`, `SHA256SUMS` | Host, user, date, Omarchy version; checksums | Used to warn about mismatches and refuse damaged backups |

It uses `tar` + `zstd` rather than plain `rsync`, so permissions, symlinks and ACLs survive even on an exFAT or NTFS USB disk.

## Get it

```bash
git clone https://github.com/azanigra/carryover.git
cd carryover
```

## 1. Back up the old machine

```bash
omarchy-update                                # so both machines can match versions
ls /run/media/$USER/                          # find your USB disk's name
./carryover backup /run/media/$USER/MY_DISK
cp carryover /run/media/$USER/MY_DISK/        # take carryover with you
```

Run it **without sudo**; it asks for your password only to read the Wi-Fi networks and `/etc`. It checks free space first. Unmount the disk before unplugging it.

## 2. Restore on the new machine

1. Install Omarchy from the ISO with **the same username** as before, then run `omarchy-update`.
2. Plug in the disk and run:

   ```bash
   cd /run/media/$USER/MY_DISK
   ./carryover restore carryover-<host>-<date>
   ```

3. carryover verifies the checksums and stops if anything is damaged. It warns if the username or Omarchy version differ.
4. It then walks through four steps, asking **y/N** before each: repo packages, AUR packages, home folder, Wi-Fi.
5. Reboot.

Restoring merges: files from the backup overwrite existing ones, but nothing else on the new machine is deleted.

## Limits

- **The backup is not encrypted.** It holds your SSH keys and keyring; treat the disk like your laptop, or use a LUKS-encrypted USB disk.
- It is a snapshot in time, not continuous backup. For that, look at restic or borg.
- Containers, VMs and databases stored outside your home are not included.
- Restoring a `~/.config` from a different Omarchy version can override newer defaults; update both machines first.

Messages follow your system language: Spanish if `LANG` starts with `es`, English otherwise.

## License

[MIT](LICENSE) © 2026 Pablo Barrera
