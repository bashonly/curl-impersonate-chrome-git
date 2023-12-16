# Maintainer: bashonly <bashonly@protonmail.com>

pkgname=curl-impersonate-chrome-git
pkgver=r252.845db62
pkgrel=1
pkgdesc='CLI tool & library for transferring data with URLs, compiled with BoringSSL'
arch=('x86_64' 'i686' 'aarch64' 'armv7h')
url='https://github.com/lwthiker/curl-impersonate'
license=('MIT')
depends=(
  'bash'
  'ca-certificates'
  'glibc'
  'libidn2'
  'libldap'
  'libpsl'
  'zlib'
  'zstd'
)
makedepends=(
  'cmake'
  'curl'
  'git'
  'go'
  'ninja'
  'unzip'
)
provides=(
  'curl-impersonate-chrome'
  'libcurl-impersonate-chrome'
  'libcurl-impersonate-chrome.so'
)
conflicts=(
  'curl-impersonate-chrome'
  'curl-impersonate-bin'
  'libcurl-impersonate-bin'
)
_curl_version=8.1.1
_boringssl_commit=3a667d10e94186fd503966f5638e134fe9fb4080
source=(
  "curl-impersonate::git+${url}.git"
  "curl-${_curl_version}.tar.xz::https://curl.se/download/curl-${_curl_version}.tar.xz"
  "curl-${_curl_version}.tar.xz.asc::https://curl.se/download/curl-${_curl_version}.tar.xz.asc"
  "brotli-1.0.9.tar.gz::https://github.com/google/brotli/archive/refs/tags/v1.0.9.tar.gz"
  "nghttp2-1.56.0.tar.bz2::https://github.com/nghttp2/nghttp2/releases/download/v1.56.0/nghttp2-1.56.0.tar.bz2"
  "boringssl-${_boringssl_commit}.zip::https://github.com/google/boringssl/archive/${_boringssl_commit}.zip"
  "boringssl-cmakelists.patch"
)
noextract=(
  "curl-${_curl_version}.tar.xz"
  "brotli-1.0.9.tar.gz"
  "nghttp2-1.56.0.tar.bz2"
  "boringssl-${_boringssl_commit}.zip"
)
# Import key with: curl https://daniel.haxx.se/mykey.asc | gpg --import
validpgpkeys=('27EDEAF22F3ABCEB50DB9A125CC908FDB71E12C2')  # Daniel Stenberg <daniel@haxx.se>
sha256sums=(
  'SKIP'
  '08a948e061929645597c1ef7194e07b308b22084ff03fa7400b465e6c05149e5'  # curl
  '783be14d644d54e19be7a703c1781511f310024cce6bba4f363069cc5b6fa10e'  # curl signature
  'f9e8d81d0405ba66d181529af42a3354f838c939095ff99930da6aa9cdf6fe46'  # brotli
  # https://github.com/nghttp2/nghttp2/releases/download/v1.56.0/checksums.txt
  '2f5dcdbf577a2df513a2464645484cc35d2e4d0733653d63193ae51db41def41'  # nghttp2
  '1ec0c891dd71e481f8f5f505d3b74969a6cc20cb10ca05be348ec69de50ba40f'  # boringssl
  '3f68e07f09cfaeef13a64b7f9e4549a09d9324ce874d4baba6e166d9159681ff'  # boringssl-cmakelists.patch
)

prepare() {
  git -C curl-impersonate clean -dfx
  cp boringssl-cmakelists.patch -t curl-impersonate/chrome/patches
  mkdir -p curl-impersonate/build
  cp -t curl-impersonate/build "${noextract[@]}"
  cd curl-impersonate/build
  mv "boringssl-${_boringssl_commit}.zip" boringssl.zip
}

pkgver() {
  cd curl-impersonate
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build () {
  cd curl-impersonate/build
  ../configure --prefix="/usr"
  make chrome-build
}

package () {
  cd curl-impersonate/build
  ../configure --prefix="${pkgdir}/usr"
  sed -i "s|prefix = /usr|prefix = ${pkgdir//|/\\|}/usr|" "curl-${_curl_version}"/{,lib/,src/}Makefile
  mkdir -p "${pkgdir}/usr"
  make chrome-install
  install -Dm644 ../LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}"
}
