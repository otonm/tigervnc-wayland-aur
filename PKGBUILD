pkgname=tigervnc-server-git
pkgver=1.1.90.3334.gcdca55c
pkgrel=1
pkgdesc="TigerVNC server (x0vncserver and w0vncserver Wayland VNC server) - git version"
arch=('x86_64')
url="https://tigervnc.org"
license=('GPL-2.0-or-later')

depends=(
    'glibc' 'pam' 'gnutls' 'libjpeg-turbo' 'pixman'
    'libx11' 'libxext' 'libxfixes' 'libxdamage'
    'libxrandr' 'libxtst' 'zlib' 'nettle'
    'glib2' 'pipewire' 'libxkbcommon' 'wayland'
)

makedepends=(
    'git' 'cmake' 'glib-networking'
    'wayland-protocols' 'util-linux-libs' 'gettext'
)

optdepends=(
    'pipewire: Required for w0vncserver screen capture'
    'xdg-desktop-portal: Required for w0vncserver portal access'
    'xdg-desktop-portal-gtk: Portal backend for GNOME'
    'xdg-desktop-portal-kde: Portal backend for KDE'
    'xdg-desktop-portal-wlr: Portal backend for wlroots compositors'
)

provides=('tigervnc-server' 'w0vncserver' 'x0vncserver' 'vncpasswd')
conflicts=('tigervnc' 'tigervnc-server')

backup=(
    etc/pam.d/tigervnc
    etc/tigervnc/vncserver-config-defaults
    etc/tigervnc/vncserver-config-mandatory
    etc/tigervnc/vncserver.users
)

source=(
    "git+https://github.com/TigerVNC/tigervnc.git"
    "w0vncserver.service"
)

sha256sums=('SKIP' 'SKIP')

pkgver() {
    cd "$srcdir/tigervnc"
    git describe --long --tags --abbrev=7 | sed 's/^v//;s/-/./g'
}

prepare() {
    cd "$srcdir/tigervnc"

    sed -i '/pam_selinux.so/d' unix/vncserver/tigervnc.pam
    sed -i '/SELinuxContext=/d' unix/vncserver/vncserver@.service.in
}

build() {
    cd "$srcdir/tigervnc"

    cmake -B build -G "Unix Makefiles" \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_INSTALL_SBINDIR=/usr/bin \
        -DCMAKE_INSTALL_LIBEXECDIR=/usr/bin \
        -DCMAKE_BUILD_TYPE=Release \
        -DBUILD_VIEWER=OFF \
        -DENABLE_WAYLAND=ON \
        -DENABLE_NLS=ON \
        -DENABLE_GNUTLS=ON \
        -DENABLE_NETTLE=ON

    cmake --build build
}

package() {
    cd "$srcdir/tigervnc"

    DESTDIR="$pkgdir" cmake --install build

    rm -f "$pkgdir/usr/bin/vncviewer" 2>/dev/null || true
    rm -rf "$pkgdir/usr/share/applications" 2>/dev/null || true

    install -Dm644 LICENCE.TXT "$pkgdir/usr/share/licenses/$pkgname/LICENSE"

    install -dm755 "$pkgdir/usr/share/man/man1"
    for manpage in doc/*.1; do
        [[ -f "$manpage" ]] && install -Dm644 "$manpage" "$pkgdir/usr/share/man/man1/"
    done

    install -Dm644 "$srcdir/w0vncserver.service" \
        "$pkgdir/usr/lib/systemd/user/w0vncserver.service"
}