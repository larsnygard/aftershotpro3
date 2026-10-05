# Maintainer: Lars Christian Nygård <lars@snart.com>
pkgname=aftershotpro3
pkgver=3.7.0.446
pkgrel=3
pkgdesc="Corel AfterShot Pro 3 - professional RAW workflow and conversion"
url="https://www.aftershotpro.com/"
arch=('x86_64')
license=('LicenseRef-Corel-AfterShot-Pro')

depends=(
    'glibc'
    'qt5-base'
    'qt5-declarative'
    'qt5-svg'
    'qt5-webchannel'
    'qt5-tools'
    'libxslt'
    'gstreamer'
    'gst-plugins-base-libs'
    'hyphen'
    'woff2'
)


optdepends=(
    'ocl-icd: OpenCL support'
    'opencl-nvidia: NVIDIA OpenCL support'
)

source=(
    "AfterShotPro3-system-QT.deb::https://dwnld.aftershotpro.com/trials/3/AfterShotPro3-system-QT.deb"
    "license.txt"
    "libqt5webkit5.deb::https://ftp.debian.org/debian/pool/main/q/qtwebkit-opensource-src/libqt5webkit5_5.212.0~alpha4-11_amd64.deb"
    "libicu67.deb::https://ftp.debian.org/debian/pool/main/i/icu/libicu67_67.1-7_amd64.deb"
    "libqt5positioning5.deb::https://ftp.debian.org/debian/pool/main/q/qtlocation-opensource-src/libqt5positioning5_5.15.2+dfsg-2_amd64.deb"
    "libqt5sensors5.deb::https://ftp.debian.org/debian/pool/main/q/qtsensors-opensource-src/libqt5sensors5_5.15.2-2_amd64.deb"
    "libjpeg62-turbo.deb::https://ftp.debian.org/debian/pool/main/libj/libjpeg-turbo/libjpeg62-turbo_2.0.6-4_amd64.deb"
    "libwebp6.deb::https://ftp.debian.org/debian/pool/main/libw/libwebp/libwebp6_0.6.1-2.1+deb11u2_amd64.deb"
    "libxml2.deb::https://ftp.debian.org/debian/pool/main/libx/libxml2/libxml2_2.9.10+dfsg-6.7+deb11u4_amd64.deb"
)

noextract=(
    'AfterShotPro3-system-QT.deb'
    'libqt5webkit5.deb'
    'libicu67.deb'
    'libqt5positioning5.deb'
    'libqt5sensors5.deb'
    'libjpeg62-turbo.deb'
    'libwebp6.deb'
    'libxml2.deb'
)


sha256sums=(
    '16d58e00b1db9bd17f915496641dc40e6feb48a3e69a5e2ea7f65c97ab733f2e'
    'b8cd7eb56eea010b9c8b592b3be4c95eed9f7b4b385209a54f9cfa9a33a634e3'
    '6ea36a63e079a38afffa539072f19e31a9b9acdb1f68c6d97ca2a1fd18ab301a'
    '2bf5c46254f527865bfd6368e1120908755fa57d83634bd7d316c9b3cfd57303'
    '84c5a91586c03b8d69358154617b1db443139ae8749196c9a4d70da734c4f1fd'
    '31a9e179950b99ce8d8549d1acff69543dfb5abba9bb7767532a91f59c946494'
    '28de780a1605cf501c3a4ebf3e588f5110e814b208548748ab064100c32202ea'
    '8abc2b1ca77a458bbbcdeb6af5d85316260977370fa2518d017222b3584d9653'
    'b29ea9026561ef0019a57b8b192bf08f725976cd1dddd3fc6bcf876840350989'
)


_extract_deb() {
    local deb="$1"
    local dest="$2"
    local member

    mkdir -p "$dest"

    member="$(ar t "$deb" | grep '^data\.tar' | head -n1)"

    if [[ -z "$member" ]]; then
        echo "ERROR: Could not find data.tar.* in $deb"
        return 1
    fi

    echo "  -> extracting $member"
    ar p "$deb" "$member" | bsdtar -xf - -C "$dest"
}

prepare() {
    rm -rf "${srcdir}/aftershot-root" "${srcdir}/legacy-root"
    mkdir -p "${srcdir}/aftershot-root" "${srcdir}/legacy-root"

    echo "Extracting AfterShot..."
    _extract_deb "${srcdir}/AfterShotPro3-system-QT.deb" \
                 "${srcdir}/aftershot-root"

    local deb
    for deb in \
        libqt5webkit5.deb \
        libicu67.deb \
        libqt5positioning5.deb \
        libqt5sensors5.deb \
        libjpeg62-turbo.deb \
        libwebp6.deb \
        libxml2.deb
    do
        echo "Extracting ${deb}..."
        _extract_deb "${srcdir}/${deb}" "${srcdir}/legacy-root"
    done
}

package() {
    local root="${srcdir}/aftershot-root"
    local legacy="${srcdir}/legacy-root/usr/lib/x86_64-linux-gnu"
    local dest="${pkgdir}/opt/AfterShot3(64-bit)"
    local legacylib="${dest}/legacy-lib"

    #
    # AfterShot
    #
    install -d "${pkgdir}/opt"
    cp -a "${root}/opt/AfterShot3(64-bit)" "${pkgdir}/opt/"

    #
    # Private compatibility runtime
    #
    #
    # AfterShot Pro 3 requires several obsolete SONAMEs no longer
    # provided by Arch Linux. Keep the Debian 11 compatibility
    # libraries private to AfterShot rather than installing them
    # into the system library path.
    #
    install -d "${legacylib}"

    cp -a "${legacy}/libQt5WebKit.so.5"* "${legacylib}/"
    cp -a "${legacy}/libQt5WebKitWidgets.so.5"* "${legacylib}/"

    cp -a "${legacy}/libQt5Positioning.so.5"* "${legacylib}/"
    cp -a "${legacy}/libQt5Sensors.so.5"* "${legacylib}/"

    cp -a "${legacy}/libicuuc.so.67"* "${legacylib}/"
    cp -a "${legacy}/libicui18n.so.67"* "${legacylib}/"
    cp -a "${legacy}/libicudata.so.67"* "${legacylib}/"

    cp -a "${legacy}/libjpeg.so.62"* "${legacylib}/"
    cp -a "${legacy}/libwebp.so.6"* "${legacylib}/"
    cp -a "${legacy}/libxml2.so.2"* "${legacylib}/"

    #
    # Launcher
    #
    install -d "${pkgdir}/usr/bin"

    cat > "${pkgdir}/usr/bin/AfterShot3X64" <<'EOF'
#!/bin/bash

INSTALL_PATH="/opt/AfterShot3(64-bit)"
LEGACY_LIB="${INSTALL_PATH}/legacy-lib"

# Native Qt Wayland causes corrupted/flashing image rendering in
# AfterShot Pro 3. Use XCB/XWayland instead.
export QT_QPA_PLATFORM=xcb

export LD_LIBRARY_PATH="${INSTALL_PATH}/lib:${LEGACY_LIB}${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}"

cd "${INSTALL_PATH}/bin" || exit 1
exec ./AfterShot "$@"
EOF

    chmod 755 "${pkgdir}/usr/bin/AfterShot3X64"

    #
    # Desktop integration
    #
    install -d "${pkgdir}/usr/share/applications"
    install -d "${pkgdir}/usr/share/pixmaps"
    install -d "${pkgdir}/usr/share/mime/packages"

    cp -a "${root}/usr/share/applications/." \
          "${pkgdir}/usr/share/applications/"

    cp -a "${root}/usr/share/pixmaps/." \
          "${pkgdir}/usr/share/pixmaps/"

    cp -a "${root}/usr/share/mime/packages/." \
          "${pkgdir}/usr/share/mime/packages/"

    #
    # License
    #
    install -Dm644 "${srcdir}/license.txt" \
        "${pkgdir}/usr/share/licenses/${pkgname}/license.txt"
}
