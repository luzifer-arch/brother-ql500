# Maintainer: Karol Babioch <karol@babioch.de>
# Inspired by package brother-dcp130c

pkgname='brother-ql500'
pkgver=1.0.1r0
pkgrel=3
pkgdesc='LPR and CUPS driver for Brother QL-500 label printer'
url='http://solutions.brother.com/linux/en_us/'
arch=('i686' 'x86_64')
license=('custom')
depends=('cups')
if [ "$CARCH" = 'x86_64' ]; then
	depends+=('lib32-glibc')
fi
conflicts=('brother-ql500-cupswrapper' 'brother-ql500-cupswrapperinch' 'brother-ql500-lpr')
provides=('brother-ql500-cupswrapper' 'brother-ql500-cupswrapperinch' 'brother-ql500-lpr')
source=(
	"https://download.brother.com/welcome/dlfp002161/ql500lpr-${pkgver/r/-}.i386.rpm"
	"https://download.brother.com/welcome/dlfp002163/ql500cupswrapper-${pkgver/r/-}.i386.rpm"
	'LICENSE')
sha256sums=('446604f41024395c6f2104e1b5ed3a175f498aa5aa0db9af2341bbb56988b991'
            '8993b585d69c5173b0c9e8db774b07e82dd4f38b4a6be98795119b6655433d89'
            'cdd1955a9996bc246ba54e84f0a5ccbfdf6623962b668188762389aa79ef9811')

prepare() {
	#  do not install in '/usr/local'
	if [ -d $srcdir/usr/local/Brother ]; then
		install -d $srcdir/usr/share
		mv $srcdir/usr/local/Brother/ $srcdir/usr/share/brother
		rm -rf $srcdir/usr/local
		sed -i 's|/usr/local/Brother|/usr/share/brother|g' $(grep -lr '/usr/local/Brother' ./)
	fi

	# setup cups directories
	install -d "$srcdir/usr/share/cups/model"
	install -d "$srcdir/usr/lib/cups/filter"

	#  go to the cupswrapper directory and find the source file from wich to generate a ppd- and wrapper-file
	cd $(find . -type d -name 'cupswrapper')
	if [ -f cupswrapper* ]; then
		_wrapper_source=$(ls cupswrapper*)
		sed -i '/^\/etc\/init.d\/cups/d' $_wrapper_source
		sed -i '/^sleep/d' $_wrapper_source
		sed -i '/^echo lpadmin/d' $_wrapper_source
		sed -i '/^lpadmin/d' $_wrapper_source
		sed -i 's|/usr|$srcdir/usr|g' $_wrapper_source
		sed -i 's|/opt|$srcdir/opt|g' $_wrapper_source
		sed -i 's|/model/Brother|/model|g' $_wrapper_source
		sed -i 's|lpinfo|echo|g' $_wrapper_source
		export srcdir=$srcdir
		./$_wrapper_source
		sed -i 's|$srcdir||' $srcdir/usr/lib/cups/filter/*lpdwrapper*
		sed -i "s|$srcdir||" $srcdir/usr/lib/cups/filter/*lpdwrapper*
		rm $_wrapper_source
	fi

	#  /etc/printcap is managed by cups
	rm $(find $srcdir -type f -name 'setupPrintcap*')
}

package() {
	cd "$srcdir"

	cp -R usr $pkgdir
	if [ -d opt ]; then cp -R opt $pkgdir; fi

	install -Dm0644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
