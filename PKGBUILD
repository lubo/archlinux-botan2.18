#!/hint/bash -e
#
# Based on https://aur.archlinux.org/packages/botan2.
#
# Maintainer: Ľubomír 'the-k' Kučera <lubomir.kucera.jr at gmail.com>

pkgname=botan2.18
pkgver=2.18.2
pkgrel=1
pkgdesc="Crypto library written in C++ (legacy version)"
arch=(x86_64)
url="https://botan.randombit.net/"
license=(
	BSD-2-Clause
)
depends=(
	glibc
)
makedepends=(
	python
)
source=(
	"https://botan.randombit.net/releases/Botan-${pkgver}.tar.xz"{,.asc}
	fix-oaep-side-channel.patch
	# CVE-2022-43705 isn't included because it breaks certificate revocation
	# checking in eidklient-native package.
	CVE-2024-34702_CVE-2024-39312.patch
	CVE-2024-34703.patch
	CVE-2024-50382.patch
	CVE-2024-50383.patch
)
sha256sums=(
	'541a3b13f1b9d30f977c6c1ae4c7bfdfda763cda6e44de807369dce79f42307e'
	'SKIP'
	'a5d1480af71b15308bb20ac9d984d2d2611b6ce93f17193b10b18cdace5d0eb4'
	'36dc2eca565c1a38a425502619019dff06e54647b7f9faf012c24c94ef16b012'
	'd71e66ac7a3a38ec5d104f1de99b93d27bf76ff7e5b54fc673e12a070a8dc448'
	'05357244c745bd9c3be6131a5afb835d6a3f51b592c801c3992ef05036c2c6da'
	'97df96aab5fb3632773b804e077171da48204e81776a945c69672e5c7b0d7396'
)
validpgpkeys=(
	621DAF6411E1851C4CF9A2E16211EBF1EFBADFBC
)

declare -gA _depends=(
	[libgcc]="libgcc_s.so"
	[libstdc++]="libstdc++.so"
)
makedepends+=(
	"${!_depends[@]}"
)

prepare() {
	cd "Botan-${pkgver}"

	# Revert to make program files authentication work in eidklient-native
	# package.
	patch -Rp1 -i ../fix-oaep-side-channel.patch

	find ../CVE-*.patch | sort -V | xargs -n 1 -t patch -p1 -i
}

build() {
	cd "Botan-${pkgver}"

	./configure.py \
		--build-targets=shared,tests \
		--no-install-python-module \
		--prefix=/usr \
		--with-os-feature=getrandom \
		--without-documentation \
		--without-pkg-config
	make
}

check() {
	cd "Botan-${pkgver}"

	LD_LIBRARY_PATH="${PWD}" ./botan-test
}

package() {
	# shellcheck disable=SC2206
	depends+=(${_depends[@]})

	: "${pkgdir:?}"

	DESTDIR="${pkgdir}" make -C "Botan-${pkgver}" install

	mv "${pkgdir}"/usr/include/botan-2{,.18}
	rm -r "${pkgdir:?}"/usr/{bin,lib/libbotan-2.so}

	install -Dm644 "Botan-${pkgver}/license.txt" \
		"${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}

: "${arch[@]}"
: "${depends[@]}"
: "${license[@]}"
: "${makedepends[@]}"
: "${pkgdesc}"
: "${pkgrel}"
: "${source[@]}"
: "${sha256sums[@]}"
: "${url}"
: "${validpgpkeys[@]}"
