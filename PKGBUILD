# Maintainer: David Runge <dvzrv@archlinux.org>

pkgname=niri
pkgver=25.05
pkgrel=1
pkgdesc="A scrollable-tiling Wayland compositor"
arch=(x86_64)
url="https://github.com/YaLTeR/niri"
license=(GPL-3.0-or-later)
depends=(
  cairo
  gcc-libs
  glib2
  glibc
  libdisplay-info
  libinput
  libpipewire
  libxkbcommon
  mesa
  pango
  pixman
  seatd
  systemd-libs
  xdg-desktop-portal-impl
)
makedepends=(
  clang
  git
  rust
)
optdepends=(
  'alacritty: a suggested GPU-accelerated terminal emulator'
  'bash: for niri-session script'
  'fuzzel: a suggested Wayland application launcher'
  'mako: a suggested Wayland notification daemon'
  'org.freedesktop.secrets: for apps to rely on secrets portal'
  'swaybg: a suggested Wayland wallpaper tool'
  'swaylock: a suggested Wayland screen locker'
  'waybar: a suggested Wayland customizable desktop bar'
  'xwayland-satellite: for running X11 apps in XWayland'
  'xdg-desktop-portal-gtk: a suggested XDG desktop portal'
  'xdg-desktop-portal-gnome: a XDG desktop portal required for screencasting'
)
# NOTE: linking issues with LTO enabled
options=(!lto)
source=($pkgname-$pkgver.tar.gz::$url/archive/refs/tags/v$pkgver.tar.gz)
sha512sums=('4609a218bfe1650e0b59517b9cd2a8139385098be0900c8e467629a6462415d2f660c2cb2dcdfdcb5ed687dda1e1d9081c0713d7d2875622d3ba07dea5c40a14')
b2sums=('0e4759954d196c05148336eec77c86dc4b2e035f9f1cae58d7bd1c5ebb1911cfc0cc1482352a59d83227cba5f25713ba3589eb2a14e25897d311e1fb92e38eb5')

prepare() {
  cd $pkgname-$pkgver
  export RUSTUP_TOOLCHAIN=stable
  cargo fetch --locked --target "$(rustc -vV | sed -n 's/host: //p')"
}

build() {
  cd $pkgname-$pkgver
  export NIRI_BUILD_COMMIT="$(zcat ../$pkgname-$pkgver.tar.gz | git get-tar-commit-id | cut -c1-7)"
  export RUSTUP_TOOLCHAIN=stable
  export CARGO_TARGET_DIR=target
  cargo build --frozen --release --features default
}

check() {
  cd "$pkgname-$pkgver"
  export RUSTUP_TOOLCHAIN=stable
  export XDG_RUNTIME_DIR="$(mktemp -d)"
  export RAYON_NUM_THREADS=1  # required so we can build in environments with _many_ threads
  cargo test --all --exclude niri-visual-tests --frozen
}

package() {
  cd $pkgname-$pkgver
  install -vDm 755 {target/release/$pkgname,resources/$pkgname-session} -t "$pkgdir/usr/bin/"
  install -vDm 644 resources/$pkgname{.service,-shutdown.target} -t "$pkgdir/usr/lib/systemd/user/"
  install -vDm 644 resources/$pkgname.desktop -t "$pkgdir/usr/share/wayland-sessions/"
  install -vDm 644 resources/$pkgname-portals.conf -t "$pkgdir/usr/share/xdg-desktop-portal/"
  install -vDm 644 resources/default-config.kdl README.md -t "$pkgdir/usr/share/doc/$pkgname/"
}
