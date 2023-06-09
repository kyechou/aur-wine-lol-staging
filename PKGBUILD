# Maintainer: moonshadow565 <moonshadow565@hotmail.com>
# Maintainer: Kuan-Yen Chou <kuanyenchou@gmail.com>

pkgname=wine-lol-staging
pkgver=8.11
_winever=8.11
pkgrel=1
pkgdesc='A compatibility layer for running Windows programs (staging branch) with LoL patches'
arch=('x86_64')
url='https://wiki.winehq.org/Wine-Staging'
license=('LGPL')
provides=('wine-lol')

options=('staticlibs' '!lto' '!strip')

source=("git+https://gitlab.winehq.org/wine/wine-staging.git#tag=v${_winever}"
        "git+https://gitlab.winehq.org/wine/wine.git#tag=wine-${_winever}"
        "0004-LoL-broken-client-update-fix.patch"
        "0005-LoL-client-slow-start-fix.patch"
        "0008-ntdll-nopguard-call_vectored_handlers.patch"
        "0009-kernel32-dont-create-console-when-not-cui.patch"
        )

sha256sums=('SKIP'
            'SKIP'
            '7607a84fd357a86bc8fb59d2cf002a3e471bd8ec78ecdb844b0b77b1ae6d11a0'
            '49dfbf7546c00958e2b426a61371eedf0119471e9998b354595d5c0ce6dab48b'
            '2075ddc417ddd11954f76be753c88e04db28f0b3937e60508f178630dd5763eb'
            'b19443ba1e01014ab478b03ac84797df2d481432798259371d94e4ba2e7b317c'
            )

depends=(
    'attr'                  'lib32-attr'
    'fontconfig'            'lib32-fontconfig'
    'libxcursor'            'lib32-libxcursor'
    'libxrandr'             'lib32-libxrandr'
    'libxi'                 'lib32-libxi'
    'gettext'               'lib32-gettext'
    'freetype2'             'lib32-freetype2'
    'gcc-libs'              'lib32-gcc-libs'
    'libpcap'               'lib32-libpcap'
    'desktop-file-utils'
)

makedepends=('git' 'autoconf' 'bison' 'perl' 'fontforge' 'flex' 'mingw-w64-gcc'
    'giflib'                'lib32-giflib'
    'gnutls'                'lib32-gnutls'
    'libxinerama'           'lib32-libxinerama'
    'libxcomposite'         'lib32-libxcomposite'
    'libxxf86vm'            'lib32-libxxf86vm'
    'v4l-utils'             'lib32-v4l-utils'
    'alsa-lib'              'lib32-alsa-lib'
    'libxcomposite'         'lib32-libxcomposite'
    'mesa'                  'lib32-mesa'
    'libgl'                 'lib32-libgl'
    'opencl-icd-loader'     'lib32-opencl-icd-loader'
    'libpulse'              'lib32-libpulse'
    'libva'                 'lib32-libva'
    'gtk3'                  'lib32-gtk3'
    'gst-plugins-base-libs' 'lib32-gst-plugins-base-libs'
    'vulkan-icd-loader'     'lib32-vulkan-icd-loader'
    'sdl2'                  'lib32-sdl2'
    'libcups'               'lib32-libcups'
    'sane'
    'libgphoto2'
    'ffmpeg'
    'samba'
    'opencl-headers'
    'vulkan-headers'
)

optdepends=(
    'giflib'                'lib32-giflib'
    'gnutls'                'lib32-gnutls'
    'v4l-utils'             'lib32-v4l-utils'
    'libpulse'              'lib32-libpulse'
    'alsa-plugins'          'lib32-alsa-plugins'
    'alsa-lib'              'lib32-alsa-lib'
    'libxcomposite'         'lib32-libxcomposite'
    'libxinerama'           'lib32-libxinerama'
    'opencl-icd-loader'     'lib32-opencl-icd-loader'
    'libva'                 'lib32-libva'
    'gtk3'                  'lib32-gtk3'
    'gst-plugins-base-libs' 'lib32-gst-plugins-base-libs'
    'vulkan-icd-loader'     'lib32-vulkan-icd-loader'
    'sdl2'                  'lib32-sdl2'
    'sane'
    'libgphoto2'
    'ffmpeg'
    'cups'
    'samba'
    'dosbox'
)

prepare() {
    # restore the wine tree to its git origin state, without wine-staging patches
    echo '  -> Cleaning wine source code tree...'
    git -C wine reset --hard HEAD  # restore tracked files
    git -C wine clean -xdf         # delete untracked files

    # change back to the wine upstream commit that this version of wine-staging is based in
    echo '  -> Changing wine HEAD to the wine-staging base commit...'
    git -C wine config --local advice.detachedHead false
    git -C wine checkout wine-${_winever}

    # RCS Launcher Optional, not necessary since 64bit update as only thing useful there is missing fonts patch
    echo '  -> Applying wine-staging patches...'
    cd "${srcdir}/wine"
    "${srcdir}/wine-staging/staging/patchinstall.py" --all

    cd "${srcdir}/wine"

    # RCS Launcher, Something with resolving long paths and symlinks, needed confirmed 8.5
    echo 'Apply 0004-LoL-broken-client-update-fix'
    patch -Np1 < "${srcdir}/0004-LoL-broken-client-update-fix.patch"

    # LCU Launcher, Hack for league to start in reasonable time
    echo 'Apply 0005-LoL-client-slow-start-fix.patch'
    patch -Np1 < "${srcdir}/0005-LoL-client-slow-start-fix.patch"

    # Game, Add some nops around exception dispatch for pacman/stub.dll to be able to hook
    echo 'Apply 0008-ntdll-nopguard-call_vectored_handlers.patch'
    patch -Np1 < "${srcdir}/0008-ntdll-nopguard-call_vectored_handlers.patch"

    # Game Optional, only necessary when starting LoL.exe wine manually (e.g. when running .rofl files)
    echo 'Apply 0009-kernel32-dont-create-console-when-not-cui.patch'
    patch -Np1 < "${srcdir}/0009-kernel32-dont-create-console-when-not-cui.patch"

    # Clean up .orig files
    echo "Clean up .orig files"
    find ./ -name '*.orig' -delete
}

build() {
    cd "${srcdir}/wine"
    autoreconf -f

    printf '%s\n' '  -> Building wine-lol-staging-64...'
    mkdir -p "${srcdir}/build-64"
    cd "${srcdir}/build-64"
    ../wine/configure \
        --prefix='/opt/wine-lol-staging' \
        --libdir='/opt/wine-lol-staging/lib' \
        --with-x \
        --with-gstreamer \
        --with-xattr \
        --with-mingw \
        --enable-win64 \
        --disable-tests \
        --disable-winemenubuilder
    make -j"$(nproc)"

    printf '%s\n' '  -> Building wine-lol-staging-32...'
    mkdir -p "${srcdir}/build-32"
    cd "${srcdir}/build-32"
    export PKG_CONFIG_PATH='/usr/lib32/pkgconfig'
    ../wine/configure \
        --prefix='/opt/wine-lol-staging' \
        --libdir='/opt/wine-lol-staging/lib32' \
        --with-x \
        --with-gstreamer \
        --with-xattr \
        --with-mingw \
        --with-wine64="${srcdir}/build-64" \
        --disable-tests \
        --disable-winemenubuilder
    make -j"$(nproc)"
}

package() {
    printf '%s\n' '  -> Packaging wine-staging-32...'
    cd "${srcdir}/build-32"
    make prefix="${pkgdir}/opt/wine-lol-staging" \
         libdir="${pkgdir}/opt/wine-lol-staging/lib32" \
         dlldir="${pkgdir}/opt/wine-lol-staging/lib32/wine" \
         install

    printf '%s\n' '  -> Packaging wine-staging-64...'
    cd "${srcdir}/build-64"
    make prefix="${pkgdir}/opt/wine-lol-staging" \
         libdir="${pkgdir}/opt/wine-lol-staging/lib" \
         dlldir="${pkgdir}/opt/wine-lol-staging/lib/wine" \
         install

    find "$pkgdir"/opt/wine-lol-staging/lib32/wine -iname "*.a" -delete
    find "$pkgdir"/opt/wine-lol-staging/lib32/wine -iname "*.def" -delete
    find "$pkgdir"/opt/wine-lol-staging/lib/wine -iname "*.a" -delete
    find "$pkgdir"/opt/wine-lol-staging/lib/wine -iname "*.def" -delete
}
