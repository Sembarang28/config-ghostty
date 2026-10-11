# Ghostty Config

Ghostty terminal configuration with one-click SSH to multiple servers via keyboard shortcuts.

## Appearance

| Option | Value |
| -------- | ------- |
| Theme | Catppuccin Mocha |
| Background opacity | 65% + blur |
| Font size | 14 |
| `option` key | Alt (macOS) |

## Prerequisites

Install `sshpass` for non-interactive password authentication:

```bash
brew install sshpass
```

## Setup

### 1. Create the `.env` file

Copy from the example:

```bash
cp .env.example .env
```

Edit `.env` with your server details:

```bash
# Server with password auth
S1_TYPE=password
S1_USER=ubuntu
S1_HOST=your.server.ip
S1_PORT=22
S1_PASS=your_password

# Server with SSH key auth
S2_TYPE=key
S2_USER=ubuntu
S2_HOST=your.server.ip
S2_PORT=22
S2_KEY=$HOME/.ssh/id_ed25519   # optional, defaults to ~/.ssh/id_ed25519
```

> `.env` is not committed to git as it contains credentials.

### 2. (Optional) Store passwords in macOS Keychain

As an alternative to storing plaintext passwords in `.env`, use Keychain:

```bash
security add-generic-password -a <S1_USER> -s ghostty-ssh-server1 -w
```

If `S1_PASS` is not set in `.env`, the config will automatically fall back to Keychain using the service name `ghostty-ssh-server1`.

## SSH Keyboard Shortcuts

Each shortcut opens a new tab and immediately SSHes into the target server — no typing required. If `S_TYPE=password`, the password is sent automatically via `sshpass`. If `S_TYPE=key`, SSH uses the specified private key.

Shortcuts and server list are configured locally and not committed to this repo.

## Adding a New Server

1. Add variables to `.env`:

   ```bash
   S5_TYPE=password
   S5_USER=ubuntu
   S5_HOST=<server-ip>
   S5_PORT=22
   S5_PASS=your_password
   ```

2. Add a keybind to `config` (local section, not committed):

   ```
   keybind = cmd+shift+5=new_tab
   keybind = cmd+shift+5=text:\x15bash -c 'set -a; . $HOME/.config/ghostty/.env; set +a; if [ "$S5_TYPE" = password ]; then SSHPASS=${S5_PASS:-$(security find-generic-password -a $S5_USER -s ghostty-ssh-server1 -w)} exec sshpass -e ssh -o StrictHostKeyChecking=accept-new -p $S5_PORT $S5_USER@$S5_HOST; else exec ssh -i ${S5_KEY:-$HOME/.ssh/id_ed25519} -p $S5_PORT $S5_USER@$S5_HOST; fi'\n
   ```

## File Structure

```
~/.config/ghostty/
├── config          # Main Ghostty configuration
├── .env            # Server credentials (not committed)
└── .env.example    # Template for .env
```
