# Maintainer: David Runge <dvzrv@archlinux.org>
# Maintainer: Caleb Maclennan <caleb@alerque.com>
# Maintainer: Robin Candau <antiz@archlinux.org>

pkgname=niri
pkgver=26.04
pkgrel=1
pkgdesc="A scrollable-tiling Wayland compositor"
arch=(x86_64)
url="https://github.com/ccyybn/niri"
license=(GPL-3.0-or-later)
depends=(
  cairo
  glib2
  glibc
  libdisplay-info
  libgcc
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
provides=(wayland-compositor)
source=("$pkgname::git+$url.git#branch=$pkgver")
sha512sums=('SKIP')
b2sums=('SKIP')

prepare() {
  cd "$pkgname"
  cargo fetch --locked --target "$(rustc --print host-tuple)"
}

build() {
  cd "$pkgname"
  export NIRI_BUILD_COMMIT="$(git rev-parse --short=7 HEAD)"
  CFLAGS+=(' -ffat-lto-objects')
  cargo build --frozen --release --features default

  # generate shell completions
  for shell in bash fish zsh; do
    cargo run --frozen --release --bin niri -- \
      completions "$shell" > "$shell-completions"
  done
}

check() {
  cd "$pkgname"
  export XDG_RUNTIME_DIR="$(mktemp -d)"
  export RAYON_NUM_THREADS=1  # required so we can build in environments with _many_ threads
  cargo test --all --exclude niri-visual-tests --frozen
}

package() {
  cd "$pkgname"
  install -vDm 755 {target/release/$pkgname,resources/$pkgname-session} -t "$pkgdir/usr/bin/"
  install -vDm 644 resources/$pkgname{.service,-shutdown.target} -t "$pkgdir/usr/lib/systemd/user/"
  install -vDm 644 resources/$pkgname.desktop -t "$pkgdir/usr/share/wayland-sessions/"
  install -vDm 644 resources/$pkgname-portals.conf -t "$pkgdir/usr/share/xdg-desktop-portal/"
  install -vDm 644 resources/default-config.kdl README.md -t "$pkgdir/usr/share/doc/$pkgname/"
  # shell auto-completions
  install -vDm 644 bash-completions "$pkgdir/usr/share/bash-completion/completions/niri"
  install -vDm 644 fish-completions "$pkgdir/usr/share/fish/vendor_completions.d/niri.fish"
  install -vDm 644 zsh-completions "$pkgdir/usr/share/zsh/site-functions/_niri"
}
