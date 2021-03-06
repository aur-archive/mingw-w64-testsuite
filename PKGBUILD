pkgname=mingw-w64-testsuite
pkgver=0
pkgrel=13
license=('GPL')
arch=(any)
pkgdesc='package testsuite (mingw-w64)'
url="https://github.com/xantares/pkgtest"
depends=('mingw-w64-crt' 'mingw-w64-qwt' 'mingw-w64-boost' 'mingw-w64-confuse' 'mingw-w64-dlfcn' 'mingw-w64-libiconv' 'mingw-w64-zlib' 'mingw-w64-libzip' 'mingw-w64-fftw' 'mingw-w64-libgnurx' 'mingw-w64-libftdi' 'mingw-w64-lapack' 'mingw-w64-libxml2' 'mingw-w64-libsvm' 'mingw-w64-nlopt' 'mingw-w64-intel-tbb' 'mingw-w64-muparser' 'mingw-w64-qt4' 'mingw-w64-qscintilla' 'mingw-w64-qsint' 'mingw-w64-bullet' 'mingw-w64-freetype' 'mingw-w64-openssl' 'mingw-w64-box2d' 'mingw-w64-hdf5' 'mingw-w64-eigen' 'mingw-w64-expat' 'mingw-w64-libjpeg-turbo' 'mingw-w64-qt5-base')
makedepends=('xorg-server-xvfb' 'mingw-w64-cmake' 'wine' 'git')
options=('!buildflags' '!strip')
_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

build() {
  rm -rf pkgtest-$pkgver
  git clone https://github.com/xantares/pkgtest.git pkgtest-$pkgver
  cd pkgtest-$pkgver

  # run tests through wine
  sed -i "s|add_test ( t_\${PKGNAME}|add_test ( t_\${PKGNAME} wine|g" CMakeLists.txt

  for _arch in ${_architectures}; do
    unset LDFLAGS
    mkdir -p build-${_arch} && pushd build-${_arch}
    ${_arch}-cmake ..
    make
    popd
  done
}

check () {
  export DISPLAY=:99
  Xvfb -nolisten tcp -screen 0 1024x768x24 $DISPLAY &
  XVFB_PID=`echo $!`
  cd pkgtest-$pkgver
  for _arch in ${_architectures}; do
    pushd "build-${_arch}"
    cp /usr/${_arch}/bin/*.dll .
    ctest || cat Testing/Temporary/LastTest.log
    popd
  done
  kill $XVFB_PID || true  
}

package() {
  cd pkgtest-$pkgver
  for _arch in ${_architectures}; do
    pushd "build-${_arch}"
    make DESTDIR="$pkgdir" install
    ${_arch}-strip --strip-all "$pkgdir"/usr/${_arch}/bin/*.exe
    popd
  done
}

