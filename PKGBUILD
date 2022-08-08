# Maintainer: gee
# contributors: yochananmarqos, bpierre, PedroHLC, rodrigo21, Matt Coffin <mcoffin13@gmail.com>
pkgname=vkbasalt
pkgver=0.3.2.6
pkgrel=2
pkgdesc='A Vulkan post-processing layer. Some of the effects are CAS, FXAA, SMAA, deband.'
arch=('x86_64')
url='https://github.com/DadSchoorse/vkBasalt'
license=('zlib')
makedepends=('meson' 'ninja' 'glslang' 'libx11' 'spirv-headers' 'vulkan-headers' 'gawk' 'git' 'patch' 'sed')
depends=('gcc-libs' 'glslang' 'libx11')
# source=("${url}/releases/download/v${pkgver}/vkBasalt-${pkgver}.tar.gz")
# sha256sums=('bf71e34d5d3fea677bc5ab95c07fd5eb052369c399d839789331614b90957593')
source=("${pkgname}::git+https://github.com/DadSchoorse/vkBasalt.git#tag=v${pkgver}")
b2sums=('SKIP')
install=vkbasalt.install
options=(debug !strip)

prepare() {
	cd "$srcdir/$pkgname"
	local _srcfile
	for _srcfile in "${source[@]}"; do
		grep -E '\.patch$' <<< "$_srcfile" || continue
		patch -p1 < "$srcdir"/"$_srcfile"
	done
	# sed -i 's|/path/to/reshade-shaders/Textures|/opt/reshade/textures|g' \
	# 	"config/vkBasalt.conf"
	# sed -i 's|/path/to/reshade-shaders/Shaders|/opt/reshade/shaders|g' \
	# 	"config/vkBasalt.conf"
	sed -E -i 's/\/path\/to\/reshade-shaders\/((Textures)|(Shaders))/\/opt\/reshade\/\L\1/g' config/vkBasalt.conf
}

_arch_meson_buildtype() {
	# local _buildtype
        # if [ $(echo "${options[@]}" | tr ' ' '\n' | awk '{ if ($1 == "debug") { print $0; } }' | wc -l) -gt 0 ]; then
        #         echo -n debug
        # else
        #         # printf '%s' 'release'
        #         echo -n release
        # fi

	local _option
	for _option in "${options[@]}"; do
		if [ "$_option" == "debug" ]; then
			echo -n 'debug'
			return 0
		fi
	done
	echo -n 'release'
}

build() {
	cd "$srcdir/$pkgname"
	arch-meson \
		--buildtype=`_arch_meson_buildtype` \
		build
	ninja -C build
}

package() {
	optdepends=('reshade-shaders-git')
	cd "$srcdir/$pkgname"

	DESTDIR="$pkgdir" ninja -C build install
	install -dm 755 "${pkgdir}/opt/vkBasalt/lib"
	mv "${pkgdir}/usr/lib/libvkbasalt.so" "${pkgdir}/opt/vkBasalt/lib/"
	[ ! -e "$pkgdir/usr/lib" ] || rmdir "$pkgdir/usr/lib"
	sed -i 's|libvkbasalt.so|/opt/vkBasalt/lib/libvkbasalt.so|g' "${pkgdir}/usr/share/vulkan/implicit_layer.d/vkBasalt.json"
	install -Dm 644 config/vkBasalt.conf "${pkgdir}/usr/share/vkBasalt/vkBasalt.conf.example"
	install -Dm 644 -t "${pkgdir}/usr/share/licenses/vkBasalt" LICENSE
}
