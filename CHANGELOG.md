# Changelog

## 0.1.1 — 2026-11-07

- Fix: backup stopped silently on machines without iwd (only NetworkManager).
- Fix: backup no longer aborts when a file changes while it is copied (open browser, logs) or cannot be read; unreadable files are listed in `home-warnings.txt`.

## 0.1.0 — 2026-11-05

- First release: `carryover backup` and `carryover restore`.
- Carries home folder, repo and AUR package lists, saved Wi-Fi networks, and a reference copy of modified `/etc` files.
- Checksums, free-space check, version and username warnings, per-step confirmation.
- Messages in English and Spanish, following `LANG`.
