# Maintainer: mickele <mimocciola at yahoo dot com>
# Contributor: Ilmari Repo <ilmari at gmail dot com> (librecad-svn PKGBUILD)
# Contributor: GazJ Gary James <garyjames82 at gmail dot com> (CADuntu PKGBUILD)

pkgname=librecad-git
pkgver=20120208
pkgrel=1
pkgdesc="A 2D CAD drawing tool based on the community edition of QCad."
arch=('i686' 'x86_64')
url="http://sourceforge.net/projects/librecad/"
license=('GPL')
depends=('qt' 'boost-libs')
makedepends=('git' 'boost')
provides=('librecad')
replaces=('librecad-svn' 'caduntu' 'caduntu-svn')
source=('librecad.desktop')
md5sums=('19dcb83a3fcdb3752439095b118ac6d3')

if [ -e .githash_${CARCH} ] ; then
	_gitphash=$(cat .githash_${CARCH})
else
	_gitphash=""
fi

_gitname="librecad"
_gitroot="git://github.com/LibreCAD/LibreCAD.git"

build() {
	if [ -d ${srcdir}/${_gitname}/.git ] ; then
		( cd ${srcdir}/${_gitname} && git pull origin )
		msg "The local files are updated."
	else
		( git clone --depth 1 ${_gitroot} ${_gitname} )
	fi
	msg "GIT checkout done or server timeout"

	cd ${_gitname}
	if [ "${_gitphash}" == $(git show | grep -m 1 commit | sed 's/commit //') ]; then
		msg "Git hash is the same as previous build"
		return 1
	fi

	msg "creating build directory"
	cd ${srcdir}
	[ -d ${_gitname}-build ] && rm -rf ${_gitname}-build
	cp -a ${_gitname} ${_gitname}-build

	msg "Starting make..."
	cd ${_gitname}-build
	# patch to solve an issue caused by gcc-4.6
	sed -e "s|LiteralMask<Value_t, n>::mask;|LiteralMask<Value_t, static_cast<unsigned int>(n)>::mask;|" \
		-e "s|SimpleSpaceMask<n>::mask;|SimpleSpaceMask<static_cast<unsigned int>(n)>::mask;|" \
		-i "fparser/fparser.cc"

	qmake librecad.pro
	make
}

package() {
	cd ${_gitname}-build
	install -D -m 755 unix/librecad $pkgdir/usr/bin/librecad
	install -D -m 644 ${srcdir}/librecad.desktop ${pkgdir}/usr/share/applications/librecad.desktop
	install -D -m 644 res/main/librecad.png ${pkgdir}/usr/share/pixmaps/librecad.png
	mkdir -p ${pkgdir}/usr/share/librecad/
	cp -r unix/resources/{library,patterns,fonts,qm} ${pkgdir}/usr/share/librecad/
	git show | grep -m 1 commit | sed 's/commit //' > ${startdir}/.githash_${CARCH}
}
