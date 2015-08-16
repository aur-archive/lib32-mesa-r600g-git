# Contributor : Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>
# Maintainer: Lone_Wolf <lonewolf@xs4all.nl>

pkgname=lib32-mesa-r600g-git
pkgver=20121212
pkgrel=1
_realver=9.1
pkgdesc="LIB32 Mesa R300 & R600 - git version.If you live in the US, you should delete --enable-texture-float \ line."
arch=('x86_64')
depends=('lib32-libxt' 'lib32-libxxf86vm' 'lib32-libxdamage' 'lib32-libffi' 'lib32-libxv' 'gcc-multilib' 'xorg-server' 'lib32-libdrm' 'mesa-r600g-git' 'lib32-systemd')
makedepends=('pkgconfig' 'python2' 'talloc' 'libxml2' 'imake' 'git' 'glproto' 'dri2proto' 'lib32-llvm-amdgpu-git' 'lib32-libxvmc' 'xorg-server-devel' 'resourceproto')
optdepends=('lib32-libtxc_dxtn: S3TC support'
            'lib32-mesa-demos: glxinfo and glxgears')
provides=(lib32-mesa=${_realver} lib32-libgl=${_realver} lib32-ati-dri=${_realver} lib32-libglapi=${_realver} lib32-libgles=${_realver} lib32-libgbm=${_realver})
conflicts=('xf86-video-ati<6.9.0-6' lib32-mesa lib32-libgl lib32-ati-dri lib32-libglapi lib32-libegl lib32-libgles lib32-libgbm)
url="http://mesa3d.sourceforge.net"
license=(custom)
source=(LICENSE)
md5sums=('5c65a0fe315dd347e09b1f2826a1df5a')

_gitroot='git://anongit.freedesktop.org/git/mesa/mesa'
_gitname='mesa'
options=('!makeflags')

build() {
  msg 'Connecting to git.freedesktop.org GIT server....'
  if [ -d ${srcdir}/${_gitname} ] ; then
    cd ${_gitname} && git pull origin
  else
    git clone --depth=1 ${_gitroot} ${_gitname}
  fi
  msg 'GIT checkout done or server timeout'
  msg 'Starting make...'

  cd "${srcdir}"

  # Cleanup and prepare the build dir
  [ -d $_gitname-build ] && rm -rf $_gitname-build
  mkdir "$_gitname-build"
  cd "$srcdir/$_gitname" && ls -A | grep -v .git | xargs -d '\n' cp -r -t ../$_gitname-build # do not copy over the .git folder
  export CC="gcc -m32"
  export CXX="g++ -m32"
  export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"
  # for our llvm-config for 32 bit
  export LLVM_CONFIG=/usr/bin/llvm-config32
  cd "$srcdir/$_gitname-build"
    autoreconf -vfi
    ./autogen.sh --prefix=/usr \
    --with-dri-driverdir=/usr/lib32/xorg/modules/dri \
    --with-gallium-drivers=r300,r600,swrast \
    --with-dri-drivers=swrast \
    --enable-texture-float \
    --enable-glx-tls \
    --enable-shared-glapi \
    --enable-gbm \
    --enable-gallium-gbm \
    --enable-gles1 \
    --enable-gles2 \
    --enable-xvmc \
    --enable-gallium-g3dvl \
    --enable-32-bit \
    --libdir=/usr/lib32 \
    --enable-osmesa \
    --enable-xorg \
    --enable-gallium-egl \
    --enable-r600-llvm-compiler \
    --enable-xa
# left out compile flags
# default = auto 
#  --enable-64-bit         build 64-bit libraries [default=auto]
#  --enable-vdpau          enable vdpau library [default=auto]
#  --disable-driglx-direct enable direct rendering in GLX and EGL for DRI [default=auto]
#  --enable-va             enable va library [default=auto]

#
# default = enabled/yes
#  --enable-shared[=PKGS]       build shared libraries [default=yes]
#  --enable-fast-install[=PKGS] optimize for fast installation [default=yes]
#  --disable-asm                disable assembly usage [default=enabled on supported plaforms]
#  --disable-pic                compile PIC objects [default=enabled for shared builds on supported platforms]
#  --enable-dri                 enable DRI modules [default=enabled]
#  --enable-glx                 enable GLX library [default=auto]
#  --disable-egl                disable EGL library [default=enabled]
#  --enable-gallium-llvm        build gallium LLVM support [default=enabled on x86/x86_64]
#
#
# default = disabled/no
#  --enable-debug          use debug compiler flags and macros [default=disabled]
#  --enable-mangling       enable mangled symbols and library name [default=disabled]
#  --enable-selinux        Build SELinux-aware Mesa [default=disabled]
#  --disable-opengl        disable support for standard OpenGL API [default=no]
#  --enable-opencl         enable OpenCL library [default=no]
#  --enable-xlib-glx       make GLX library Xlib-based instead of DRI-based [default=disable]
#  --enable-gallium-egl    enable optional EGL state tracker (not required for
#                          EGL support in Gallium with OpenGL and OpenGL ES)
#                          (default=disable]
#  --enable-gallium-tests  Enable optional Gallium tests) [default=disable]
#  --enable-gallium-g3dvl  build gallium g3dvl [default=disabled]
#
  make
}

package() {

# lib32-Mesa
  cd "${srcdir}/$_gitname-build" 
  make DESTDIR="${pkgdir}" install
  rm -f "${pkgdir}/usr/lib32/libGL.so"*
  rm -f "${pkgdir}/usr/lib32/libGLESv"*
  rm -f "${pkgdir}/usr/lib32/libEGL"*
  rm -rf "${pkgdir}/usr/lib32/egl"
  rm -rf "${pkgdir}/usr/lib32/xorg"
  rm -f "${pkgdir}/usr/include/GL/glew.h"
  rm -f "${pkgdir}/usr/include/GL/glxew.h"
  rm -f "${pkgdir}/usr/include/GL/wglew.h"
  rm -f "${pkgdir}/usr/include/GL/glut.h"
  rm -rf ${pkgdir}/usr/include/KHR
# lib32-libgl
  install -m755 -d "${pkgdir}/usr/lib32/xorg/modules/extensions"
  bin/minstall lib32/libGL.so* "${pkgdir}/usr/lib32/"
  bin/minstall lib32/libdricore*.so* "${pkgdir}/usr/lib32/"
#  bin/minstall lib32/libglsl.so* "${pkgdir}/usr/lib32/"
  make -C "${srcdir}/$_gitname-build/src/gallium/targets/dri-swrast" DESTDIR="${pkgdir}" install
  ln -s libglx.xorg "${pkgdir}/usr/lib32/xorg/modules/extensions/libglx.so"
  rm -rf "${pkgdir}"/usr/{include,share,bin}
# lib32-libegl
  make -C src/gallium/targets/egl-static DESTDIR="${pkgdir}" install
  bin/minstall lib32/libEGL.so* "${pkgdir}/usr/lib32/"
  bin/minstall lib32/egl/* "${pkgdir}/usr/lib32/egl/"
  bin/minstall src/egl/main/egl.pc "${pkgdir}/usr/lib32/pkgconfig/"
# lib32-ati-dri
  cd "${srcdir}/$_gitname-build/src/mesa/drivers/dri"
  make -C ${srcdir}/$_gitname-build/src/gallium/targets/dri-r300 DESTDIR="${pkgdir}" install
  make -C ${srcdir}/$_gitname-build/src/gallium/targets/dri-r600 DESTDIR="${pkgdir}" install
#license
  install -m755 -d "${pkgdir}/usr/share/licenses/${pkgname}"
  install -m755 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/"
}
