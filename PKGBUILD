# vim:set ft=sh:
# Maintainer: mickele <mimocciola at yahoo dot com>
# Contributor: BlackEagle <ike DOT devolder AT gmail DOT com>
# Contributor: Ilmari Repo <ilmari at gmail dot com> (librecad-svn PKGBUILD)
# Contributor: GazJ Gary James <garyjames82 at gmail dot com> (CADuntu PKGBUILD)

pkgname=librecad-git
_gitname="librecad"
pkgver=20160713.330203d
pkgrel=1
pkgdesc="A 2D CAD drawing tool based on the community edition of QCad."
arch=('i686' 'x86_64')
url="http://sourceforge.net/projects/librecad/"
license=('GPL')
depends=('qt5-base' 'qt5-svg' 'qt5-tools' 'libxcb' 'muparser' 'desktop-file-utils' 'hicolor-icon-theme')
makedepends=('boost' 'imagemagick' 'librsvg' 'git')
provides=('librecad')
replaces=('librecad-svn' 'caduntu' 'caduntu-svn')
source=("$_gitname::git://github.com/LibreCAD/LibreCAD.git")
md5sums=('SKIP')

pkgver() {
	cd "$srcdir/$_gitname"
	echo $(git log -1 --format="%ci" | sed 's/.*\([0-9]\{4\}\)-\([0-9]\{2\}\)-\([0-9]\{2\}\).*/\1\2\3/').$(git rev-parse --short HEAD)
}

build() {
	msg "Starting make..."
	cd $_gitname

    qmake-qt5 librecad.pro

    # fix include path... this is an issue with gcc 6.1.1 and qmake-qt5...
    make qmake_all
    sed -i '/INCPATH/s|-isystem /usr/include ||' librecad/src/Makefile

    make

    (
        cd plugins
        for _qtpro in $(find -name "*.pro"); do
            sed -e 's/\/src\/plugins/\/librecad\/src\/plugins/' -i $_qtpro
        done
        qmake-qt5
        make
    )
}

package() {
	cd $_gitname

    # executables
    install -D -m0755 unix/librecad "$pkgdir/usr/bin/librecad"
    install -D -m0755 unix/ttf2lff "$pkgdir/usr/bin/ttf2lff"

    # desktop file and man pages
    install -D -m0644 desktop/librecad.desktop "$pkgdir/usr/share/applications/librecad.desktop"
    install -D -m0644 desktop/librecad.1 "$pkgdir/usr/share/man/man1/librecad.1"

    # documentation
    install -D -m0644 librecad/support/doc/README "$pkgdir/usr/share/doc/librecad/index.README"
    install -D -m0644 librecad/support/doc/index.html "$pkgdir/usr/share/doc/librecad/index.html"
    install -D -m0644 librecad/support/doc/style.css "$pkgdir/usr/share/doc/librecad/style.css"
    install -D -m0644 librecad/support/doc/img/librecadlogo.png "$pkgdir/usr/share/doc/librecad/img/librecadlogo.png"

    # icons
    for SIZE in 16 24 32 48 64 96 128; do
        convert -scale $SIZE \
            desktop/graphics_icons_and_splash/Icon\ LibreCAD/Icon_Librecad.svg \
            $srcdir/librecad.png
        install -D -m0644 $srcdir/librecad.png "$pkgdir/usr/share/icons/hicolor/$SIZEx$SIZE/apps/librecad.png"
    done
    install -D -m0644 desktop/graphics_icons_and_splash/Icon\ LibreCAD/Icon_Librecad.svg "$pkgdir/usr/share/icons/hicolor/scalable/apps/librecad.svg"

	mkdir -p "$pkgdir/usr/share/librecad/"
	cp -r unix/resources/{library,patterns,fonts,qm} "$pkgdir/usr/share/librecad/"
	mkdir -p "$pkgdir/usr/lib/librecad/"
	cp -r unix/resources/plugins "$pkgdir/usr/lib/librecad/"
}
