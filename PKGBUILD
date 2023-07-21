# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Maintainer: Mark Wagie <mark at manjaro dot org>

# Arch credits:
# Maintainer: Laurent Carlier <lordheavym@gmail.com>
# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>

pkgbase=lib32-mesa
pkgname=(
  'lib32-vulkan-mesa-layers'
  'lib32-opencl-mesa'
  'lib32-vulkan-intel'
  'lib32-vulkan-radeon'
  'lib32-vulkan-swrast'
  'lib32-vulkan-virtio'
  'lib32-libva-mesa-driver'
  'lib32-mesa-vdpau'
  'lib32-mesa'
)
pkgver=23.1.3
pkgrel=4
pkgdesc="An open-source implementation of the OpenGL specification (32-bit)"
url="https://www.mesa3d.org/"
arch=('x86_64')
license=('custom')
makedepends=(
  'lib32-clang'
  'lib32-expat'
  'lib32-libdrm'
  'lib32-libelf'
  'lib32-libglvnd'
  'lib32-libunwind'
  'lib32-libva'
  'lib32-libvdpau'
  'lib32-libx11'
  'lib32-libxdamage'
  'lib32-libxml2'
  'lib32-libxrandr'
  'lib32-libxshmfence'
  'lib32-libxxf86vm'
  'lib32-llvm'
  'lib32-lm_sensors'
  'lib32-systemd'
  'lib32-vulkan-icd-loader'
  'lib32-wayland'
  'lib32-zstd'

  # shared with lib32-mesa
  'clang'
  'cmake'
  'elfutils'
  'glslang'
  'libclc'
  'meson'
  'python-mako'
  'wayland-protocols'
  'xorgproto'

)
options=('lto')
source=(
  https://mesa.freedesktop.org/archive/mesa-${pkgver}.tar.xz{,.sig}
  mesa-staging-23.1-20230718.patch
  LICENSE
)
sha256sums=('2f6d7381bc10fbd2d6263ad1022785b8b511046c1a904162f8f7da18eea8aed9'
            'SKIP'
            '828d9ff14ec181fc368c76097047e999ef7170d13186c8f0db15283c7152ec6f'
            '7052ba73bb07ea78873a2431ee4e828f4e72bda7d176d07f770fa48373dec537')
b2sums=('99ce2a458c049b60cf13278d5e2e04d9eebefe04d5cbfcba7ff13421724bfd7877ec24086e513d249f1e7b1d537acea90e2ae53d71ef420213a5764ce61d8c4f'
        'SKIP'
        '66d2b2106c5020e54926c72b5d46a6e08f0ce4e439de19fb18e8954ee324ded1eecc5b1b16425ade439db52437a2f10070b65e53d9f882d6da8f5ae421c65ea1'
        '1ecf007b82260710a7bf5048f47dd5d600c168824c02c595af654632326536a6527fbe0738670ee7b921dd85a70425108e0f471ba85a8e1ca47d294ad74b4adb')
validpgpkeys=('8703B6700E7EE06D7A39B8D6EDAE37B02CEB490D'  # Emil Velikov <emil.l.velikov@gmail.com>
              '946D09B5E4C9845E63075FF1D961C596A7203456'  # Andres Gomez <tanty@igalia.com>
              'E3E8F480C52ADD73B278EE78E1ECBE07D7D70895'  # Juan Antonio Suárez Romero (Igalia, S.L.) <jasuarez@igalia.com>
              'A5CC9FEC93F2F837CB044912336909B6B25FADFA'  # Juan A. Suarez Romero <jasuarez@igalia.com>
              '71C4B75620BC75708B4BDB254C95FAAB3EB073EC'  # Dylan Baker <dylan@pnwbakers.com>
              '57551DE15B968F6341C248F68D8E31AFC32428A6') # Eric Engestrom <eric@engestrom.ch>

prepare() {
  cd mesa-$pkgver
  # lastest staging patches
  patch -p1 -i ../mesa-staging-23.1-20230718.patch
  # fix Chromium issues
  # https://gitlab.freedesktop.org/mesa/mesa/-/issues/9296
  # https://gitlab.manjaro.org/packages/extra/mesa/-/issues/7
  patch -p1 -i ../23c003b.patch
}

_libdir=usr/lib32

build() {
  local meson_options=(
    --libdir=/$_libdir
    -D android-libbacktrace=disabled
    -D b_ndebug=true
    -D dri3=enabled
    -D egl=enabled
    -D gallium-drivers=r300,r600,radeonsi,nouveau,virgl,svga,swrast,i915,iris,crocus,zink
    -D gallium-extra-hud=true
    -D gallium-nine=true
    -D gallium-omx=disabled
    -D gallium-opencl=icd
    -D gallium-rusticl=false
    -D gallium-va=enabled
    -D gallium-vdpau=enabled
    -D gallium-xa=enabled
    -D gbm=enabled
    -D gles1=disabled
    -D gles2=enabled
    -D glvnd=true
    -D glx=dri
    -D intel-clc=disabled
    -D libunwind=enabled
    -D llvm=enabled
    -D lmsensors=enabled
    -D microsoft-clc=disabled
    -D osmesa=true
    -D platforms=x11,wayland
    -D rust_std=2021
    -D shared-glapi=enabled
    -D valgrind=disabled
    -D vulkan-drivers=amd,intel,intel_hasvk,swrast,virtio-experimental
    -D vulkan-layers=device-select,intel-nullhw,overlay
  )

  export CC="gcc -m32"
  export CXX="g++ -m32"
  export PKG_CONFIG="i686-pc-linux-gnu-pkg-config"
  export LLVM_CONFIG="llvm-config32"

  arch-meson mesa-$pkgver build "${meson_options[@]}"
  meson configure --no-pager build # Print config
  meson compile -C build

  # fake installation to be seperated into packages
  # outside of fakeroot but mesa doesn't need to chown/mod
  DESTDIR="${srcdir}/fakeinstall" meson install -C build
}

_install() {
  local src f dir
  for src; do
    f="${src#fakeinstall/}"
    dir="${pkgdir}/${f%/*}"
    install -m755 -d "${dir}"
    mv -v "${src}" "${dir}/"
  done
}

package_lib32-vulkan-mesa-layers() {
  pkgdesc="Mesa's Vulkan layers (32-bit)"
  depends=(
    'lib32-libdrm'
    'lib32-libxcb'
    'lib32-wayland'

    'vulkan-mesa-layers'
  )
  conflicts=('lib32-vulkan-mesa-layer')
  replaces=('lib32-vulkan-mesa-layer')

  rm -rv fakeinstall/usr/share/vulkan/explicit_layer.d
  rm -rv fakeinstall/usr/share/vulkan/implicit_layer.d
  _install fakeinstall/$_libdir/libVkLayer_*.so
  rm -v fakeinstall/usr/bin/mesa-overlay-control.py

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

package_lib32-opencl-mesa() {
  pkgdesc="OpenCL support for AMD/ATI Radeon mesa drivers (32-bit)"
  depends=(
    'lib32-clang'
    'lib32-expat'
    'lib32-libdrm'
    'lib32-libelf'
    'lib32-zstd'

    'libclc'
    'spirv-llvm-translator'
    'opencl-mesa'
  )
  optdepends=('opencl-headers: headers necessary for OpenCL development')
  provides=('lib32-opencl-driver')

  rm -rv fakeinstall/etc/OpenCL
  _install fakeinstall/$_libdir/lib*OpenCL*
  _install fakeinstall/$_libdir/gallium-pipe

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

package_lib32-vulkan-intel() {
  pkgdesc="Intel's Vulkan mesa driver (32-bit)"
  depends=(
    'lib32-libdrm'
    'lib32-libx11'
    'lib32-libxshmfence'
    'lib32-systemd'
    'lib32-wayland'
    'lib32-zstd'
  )
  optdepends=('lib32-vulkan-mesa-layers: additional vulkan layers')
  provides=('lib32-vulkan-driver')

  _install fakeinstall/usr/share/vulkan/icd.d/intel_*.json
  _install fakeinstall/$_libdir/libvulkan_intel*.so

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

package_lib32-vulkan-radeon() {
  pkgdesc="Radeon's Vulkan mesa driver (32-bit)"
  depends=(
    'lib32-libdrm'
    'lib32-libelf'
    'lib32-libx11'
    'lib32-libxshmfence'
    'lib32-llvm-libs'
    'lib32-systemd'
    'lib32-wayland'
    'lib32-zstd'

    'vulkan-radeon'
  )
  optdepends=('lib32-vulkan-mesa-layers: additional vulkan layers')
  provides=('lib32-vulkan-driver')

  rm -v fakeinstall/usr/share/drirc.d/00-radv-defaults.conf
  _install fakeinstall/usr/share/vulkan/icd.d/radeon_icd*.json
  _install fakeinstall/$_libdir/libvulkan_radeon.so

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

package_lib32-vulkan-swrast() {
  pkgdesc="Vulkan software rasteriser driver (32-bit)"
  depends=(
    'lib32-libdrm'
    'lib32-libunwind'
    'lib32-libx11'
    'lib32-libxshmfence'
    'lib32-llvm-libs'
    'lib32-systemd'
    'lib32-wayland'
    'lib32-zstd'
  )
  optdepends=('lib32-vulkan-mesa-layers: additional vulkan layers')
  conflicts=('lib32-vulkan-mesa')
  replaces=('lib32-vulkan-mesa')
  provides=('lib32-vulkan-driver')

  _install fakeinstall/usr/share/vulkan/icd.d/lvp_icd*.json
  _install fakeinstall/$_libdir/libvulkan_lvp.so

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

package_lib32-vulkan-virtio() {
  pkgdesc="Venus Vulkan mesa driver for Virtual Machines (32-bit)"
  depends=(
    'lib32-libdrm'
    'lib32-libx11'
    'lib32-libxshmfence'
    'lib32-systemd'
    'lib32-wayland'
    'lib32-zstd'
  )
  optdepends=('lib32-vulkan-mesa-layers: additional vulkan layers')
  provides=('lib32-vulkan-driver')

  _install fakeinstall/usr/share/vulkan/icd.d/virtio_icd*.json
  _install fakeinstall/$_libdir/libvulkan_virtio.so

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

package_lib32-libva-mesa-driver() {
  pkgdesc="VA-API drivers (32-bit)"
  depends=(
    'lib32-expat'
    'lib32-libdrm'
    'lib32-libelf'
    'lib32-libx11'
    'lib32-libxshmfence'
    'lib32-llvm-libs'
    'lib32-zstd'
  )
  provides=('lib32-libva-driver')

  _install fakeinstall/$_libdir/dri/*_drv_video.so

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

package_lib32-mesa-vdpau() {
  pkgdesc="VDPAU drivers (32-bit)"
  depends=(
    'lib32-expat'
    'lib32-libdrm'
    'lib32-libelf'
    'lib32-libx11'
    'lib32-libxshmfence'
    'lib32-llvm-libs'
    'lib32-zstd'
  )
  provides=('lib32-vdpau-driver')

  _install fakeinstall/$_libdir/vdpau

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

package_lib32-mesa() {
  depends=(
    'lib32-libdrm'
    'lib32-libelf'
    'lib32-libglvnd'
    'lib32-libunwind'
    'lib32-libxdamage'
    'lib32-libxshmfence'
    'lib32-libxxf86vm'
    'lib32-llvm-libs'
    'lib32-lm_sensors'
    'lib32-vulkan-icd-loader'
    'lib32-wayland'
    'lib32-zstd'

    'mesa'
  )
  optdepends=(
    'lib32-libva-mesa-driver: for accelerated video playback'
    'lib32-mesa-vdpau: for accelerated video playback'
    'opengl-man-pages: for the OpenGL API man pages'
  )
  provides=(
    'lib32-mesa-libgl'
    'lib32-opengl-driver'
  )
  conflicts=('lib32-mesa-libgl')
  replaces=('lib32-mesa-libgl')

  rm -v fakeinstall/usr/share/drirc.d/00-mesa-defaults.conf
  rm -v fakeinstall/usr/share/glvnd/egl_vendor.d/50_mesa.json

  # ati-dri, nouveau-dri, intel-dri, svga-dri, swrast, swr
  _install fakeinstall/$_libdir/dri/*_dri.so

  _install fakeinstall/$_libdir/d3d
  _install fakeinstall/$_libdir/lib{gbm,glapi}.so*
  _install fakeinstall/$_libdir/libOSMesa.so*
  _install fakeinstall/$_libdir/libxatracker.so*

  rm -rv fakeinstall/usr/include
  _install fakeinstall/$_libdir/pkgconfig

  # libglvnd support
  _install fakeinstall/$_libdir/libGLX_mesa.so*
  _install fakeinstall/$_libdir/libEGL_mesa.so*

  # indirect rendering
  ln -sr "$pkgdir"/$_libdir/libGLX_{mesa,indirect}.so.0

  # make sure there are no files left to install
  find fakeinstall -depth -print0 | xargs -0 rmdir

  install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" LICENSE
}

