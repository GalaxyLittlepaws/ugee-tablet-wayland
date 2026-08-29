# Maintainer: GalaxyLittlepaws <ADD-YOUR-EMAIL@example.com>
pkgname=ugee-tablet-wayland
pkgver=4.3.4
_pkgver_sub1=20241217
_pkgver_sub2=241031
pkgrel=1
pkgdesc='UGEE tablet driver 4.x with a monitor-detection fix for Wayland/XWayland sessions'
url='https://www.ugee.com.cn/download/'
source=("https://download.ugee.com.cn/upload/download/$_pkgver_sub1/ugeeTablet-$pkgver-$_pkgver_sub2.tar.gz")
arch=('x86_64')
license=('custom: commercial')
depends=('libx11' 'libxrandr' 'libxinerama' 'libxi' 'libxtst' 'libglvnd' 'libusb' 'xorg-xwayland')
conflicts=('ugee-tablet')
provides=('ugee-tablet')
install="PKGBUILD"
sha256sums=('83c51feb0d0a1e68e7f443665a68c8dab1fb41c72ec993c045ee465f839dd2ff')

prepare()
{
    local bin="$srcdir/ugeeTablet-$pkgver-$_pkgver_sub2/App/usr/lib/ugeeTablet/ugeeTabletDriver"
    # The closed-source ugeeTabletDriver finds screens via XRandR but only accepts
    # an output whose name comes after "Virtual" (e.g. "Virtual1") or that carries
    # an EDID identity.  Under Wayland/XWayland outputs are named "DP-1"/"HDMI-A-1"
    # (which sort before "Virtual") and expose no EDID, so every monitor was skipped.
    # NOP the jle (the "name <= Virtual -> EDID-only" rule) in enum_srceen_info so
    # every active output is enumerated directly.
    if ! printf '\x0f\x8e\x05\x01\x00\x00' | cmp -s - <(dd if="$bin" bs=1 skip=$((0x1048d)) count=6 2>/dev/null); then
        printf 'error: unexpected bytes in ugeeTabletDriver at file offset 0x1048d; patch may be stale\n' >&2
        return 1
    fi
    printf '\x90\x90\x90\x90\x90\x90' | dd of="$bin" bs=1 seek=$((0x1048d)) conv=notrunc status=none
}

package()
{
    install -d "$pkgdir/usr"
    install -d "$pkgdir/usr/lib"
    install -d "$pkgdir/etc"

    cp -r "$srcdir/ugeeTablet-$pkgver-$_pkgver_sub2/App/usr" "$pkgdir"
    cp -r "$srcdir/ugeeTablet-$pkgver-$_pkgver_sub2/App/lib" "$pkgdir/usr"
    cp -r "$srcdir/ugeeTablet-$pkgver-$_pkgver_sub2/App/etc" "$pkgdir"
}

post_install()
{
    echo -en ":: \033[1m\033[33mA reboot is required after the installation!\033[0m\n"
}

post_upgrade()
{
    post_install
}
