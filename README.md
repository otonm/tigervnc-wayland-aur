# tigervnc-server (AUR)

AUR package for TigerVNC server components with Wayland support.

## Description

This package provides TigerVNC server binaries for Arch Linux:

- **x0vncserver** - VNC server for X11 displays
- **w0vncserver** - VNC server for Wayland compositors (via PipeWire/XDG portals)
- **vncpasswd** - Password utility for VNC authentication

## Installation

### From AUR

```bash
# Using yay
yay -S tigervnc-server

# Using paru
paru -S tigervnc-server
```

### Manual Build

```bash
git clone https://aur.archlinux.org/tigervnc-server.git
cd tigervnc-server
makepkg -si
```

## Usage

### x0vncserver (X11)

```bash
x0vncserver -display :0 -passwordfile ~/.vnc/passwd
```

### w0vncserver (Wayland)

```bash
w0vncserver -SecurityTypes None
```

Or use the included systemd user service:

```bash
systemctl --user enable --now w0vncserver.service
```

## Dependencies

### Required

- glibc, pam, gnutls, libjpeg-turbo, pixman
- libx11, libxext, libxfixes, libxdamage, libxrandr, libxtst
- zlib, nettle, glib2, pipewire, libxkbcommon, wayland

### Optional (for w0vncserver)

- **pipewire** - Screen capture
- **xdg-desktop-portal** - Portal access
- **xdg-desktop-portal-gtk** - GNOME portal backend
- **xdg-desktop-portal-kde** - KDE portal backend
- **xdg-desktop-portal-wlr** - wlroots portal backend

## Configuration

Configuration files are located in `/etc/tigervnc/`:

- `vncserver-config-defaults`
- `vncserver-config-mandatory`
- `vncserver.users`

## Notes

- This package conflicts with the official `tigervnc` package
- The viewer component is not included (server-only package)
- w0vncserver requires a running PipeWire session and appropriate portal backend for your desktop environment

## License

GPL-2.0-or-later

## Links

- [TigerVNC Official Website](https://tigervnc.org)
- [TigerVNC GitHub](https://github.com/TigerVNC/tigervnc)
