# macos-setup-ansible

Playbook Ansible untuk mereplikasi setup macOS (Apple Silicon): paket Homebrew, aplikasi, shell zsh, dan config aplikasi harian. Dijalankan langsung di Mac target, tanpa SSH atau inventory.

## Isi

| Bagian | Detail |
|---|---|
| Homebrew | 1 tap, 35 formula (CLI, database, tools dev), 30 cask (aplikasi dan font) |
| Shell | Oh My Zsh, tema Powerlevel10k, plugin `zsh-autosuggestions` dan `zsh-syntax-highlighting` |
| Dotfiles | `.zshrc`, `.zprofile`, `.p10k.zsh` |
| `~/.config` | Ghostty, Karabiner, LinearMouse, Zed (settings + keymap), btop, git ignore |

Daftar lengkap paket ada di `playbook.yml` (bagian `vars`).

## Pemakaian

Di Mac target:

```sh
# 1. Homebrew (lihat https://brew.sh), lalu:
brew install ansible

# 2. Ambil repo ini
git clone https://github.com/Tristanputra2119/macos-setup-ansible.git
cd macos-setup-ansible

# 3. Pasang collection yang dibutuhkan
ansible-galaxy collection install -r requirements.yml

# 4. Dry-run dulu, lalu jalankan sungguhan
ansible-playbook -K playbook.yml --check
ansible-playbook -K playbook.yml
```

`-K` meminta password sudo (beberapa cask butuh hak admin).

## Perilaku

- **Idempotent**: paket yang sudah terpasang dilewati, jadi aman dijalankan berulang.
- **Dotfiles tidak menimpa tanpa jejak**: file lama di-backup dengan akhiran timestamp. `~/.config` digabung, file lain tidak dihapus.
- **Oh My Zsh, tema, dan plugin** di-clone sekali dan tidak ditarik ulang saat playbook dijalankan lagi.

## Menyesuaikan

Edit `vars` di `playbook.yml`: hapus baris di `formulae` atau `casks` untuk melewati paket. Untuk mengganti dotfiles, edit file di `files/`.

## Tidak termasuk (sengaja)

- `.gitconfig` (identitas) dan config yang berisi token: `~/.config/{gh,rclone,opencode,cagent}`. Login ulang di Mac target.
- Aplikasi App Store.
- Setting sistem (`defaults write`), login akun, dan lisensi.

## Catatan setelah setup

- Batas charge baterai: `batt limit 80` (tanpa sudo setelah daemon terpasang), cek dengan `batt status`.
- Karabiner dan LinearMouse butuh izin Accessibility dan Input Monitoring. Setujui manual di System Settings.
- `.zshrc` memuat PATH ke tool yang tidak dipasang playbook (misalnya `~/.opencode/bin`). Entri itu tidak berbahaya, hanya menunjuk ke folder yang belum ada.
- Daftar paket adalah snapshot per 11 Okt 2026. Perbarui `playbook.yml` kalau paket berubah.
