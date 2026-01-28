# Maintainer: Your Name <your@email.com>
pkgname=tigervnc-server
pkgver=1.16.80
pkgrel=1
pkgdesc="TigerVNC server (x0vncserver and w0vncserver Wayland VNC server)"
arch=('x86_64')
url="https://tigervnc.org"
license=('GPL-2.0-or-later')
depends=(
    'glibc'
    'pam'
    'gnutls'
    'libjpeg-turbo'
    'pixman'
    'libx11'
    'libxext'
    'libxfixes'
    'libxdamage'
    'libxrandr'
    'libxtst'
    'zlib'
    'nettle'
    'glib2'
    'pipewire'
    'libxkbcommon'
    'wayland'
)
makedepends=(
    'cmake'
    'glib-networking'
    'wayland-protocols'
    'util-linux-libs'
    'gettext'
)
optdepends=(
    'pipewire: Required for w0vncserver screen capture'
    'xdg-desktop-portal: Required for w0vncserver portal access'
    'xdg-desktop-portal-gtk: Portal backend for GNOME'
    'xdg-desktop-portal-kde: Portal backend for KDE'
    'xdg-desktop-portal-wlr: Portal backend for wlroots compositors'
)
provides=('w0vncserver' 'x0vncserver' 'vncpasswd')
conflicts=('tigervnc')
backup=(etc/pam.d/tigervnc
        etc/tigervnc/vncserver-config-defaults
        etc/tigervnc/vncserver-config-mandatory
        etc/tigervnc/vncserver.users)
source=("tigervnc-${pkgver}::git+file://${startdir}/.."
        "w0vncserver.service")
sha256sums=('SKIP'
            'SKIP')

prepare() {
    cd "${srcdir}/tigervnc-${pkgver}"

    # Remove SELinux support (not used on Arch Linux)
    sed -i '/pam_selinux.so/d' unix/vncserver/tigervnc.pam
    sed -i '/SELinuxContext=/d' unix/vncserver/vncserver@.service.in
}

build() {
    cd "${srcdir}/tigervnc-${pkgver}"

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
    cd "${srcdir}/tigervnc-${pkgver}"

    DESTDIR="${pkgdir}" cmake --install build

    # Remove viewer components if any were built
    rm -f "${pkgdir}/usr/bin/vncviewer" 2>/dev/null || true
    rm -rf "${pkgdir}/usr/share/applications" 2>/dev/null || true

    # Install license
    install -Dm644 LICENCE.TXT "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

    # Install man pages
    install -dm755 "${pkgdir}/usr/share/man/man1"
    for manpage in doc/*.1; do
        [[ -f "$manpage" ]] && install -Dm644 "$manpage" "${pkgdir}/usr/share/man/man1/"
    done

    # Install systemd user service for w0vncserver
    install -Dm644 "${srcdir}/w0vncserver.service" \
        "${pkgdir}/usr/lib/systemd/user/w0vncserver.service"
}
