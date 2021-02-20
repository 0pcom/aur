# Maintainer: Moses Narrow <moe_narrow@use.startmail.com>
_pkgname=privateness
pkgname=${_pkgname}
_githuborg=NESS-Network
pkgdesc="NESS Core and Wallet. privateness.network"
pkgver='autogenerated'
#pkgver='autogenerated'
pkgrel=1
#pkgrel=1
arch=('x86_64' 'aarch64' 'armv8' 'armv7' 'armv7l' 'armv7h' 'armv6h' 'armhf' 'armel' 'arm')
_pkggopath="github.com/${_githuborg}/${_pkgname}"
url="https://${_pkggopath}"
makedepends=('git' 'go' 'musl' 'kernel-headers-musl')
source=("git+${url}.git" ##branch=${BRANCH:-develop}"
"ness-wallet.sh")
sha256sums=('SKIP'
            '23babd4af4ebdff1fb914161bcec54cfa9307a5f26fc74ae388f5b131335efb2')
#validpgpkeys=('DE08F924EEE93832DABC642CA8DC761B1C0C0CFC')  # Moses Narrow <moe_narrow@use.startmail.com>

#tar -czvf privateness-scripts.tar.gz privateness-scripts
#updpkgsums

pkgver() {
		cd "${srcdir}/${pkgname}"
		local date=$(git log -1 --format="%cd" --date=short | sed s/-//g)
		local count=$(git rev-list --count HEAD)
		local commit=$(git rev-parse --short HEAD)
		echo "$date.${count}_$commit"
	}

	prepare() {
		#verify PKGBUILD signature
		#gpg --verify ${srcdir}/PKGBUILD.sig ../PKGBUILD
		mkdir -p ${srcdir}/go/src/github.com/${_githuborg}/ ${srcdir}/go/bin
		ln -rTsf ${srcdir}/${_pkgname} ${srcdir}/go/src/${_pkggopath}
	}

build() {
	export GOPATH=${srcdir}/go
	export GOBIN=${GOPATH}/bin
  export CC=musl-gcc
  export CGO_ENABLED=1
	_cmddir=${srcdir}/go/src/${_pkggopath}/cmd

  _buildbins address_gen
  _buildbins cipher-testdata
  _buildbins monitor-peers
  _buildbins newcoin
  _buildbins privateness
  _buildbins privateness-cli
	#binary transparency
	cd $GOBIN
	_msg2 'binary sha256sums'
	sha256sum $(ls)
}

_buildbins() {

_binname=$1
_msg2 "building ${_binname} binary"
#SPEED UP TESTING OF BUILDS
if [[ ! -f ${GOBIN}/${_binname} ]] ; then
	cd ${_cmddir}/${_binname}
  go build -trimpath --ldflags '-linkmode external -extldflags "-static" -buildid=' -o $GOBIN/ .
fi
}

package() {
	#create directory trees
	_nesssrcdir=${srcdir}/${_pkgname}
	_nesspath=${pkgdir}/opt/${_pkgname}
	_nessgobin=${_nesspath}/bin
	_nessguidir=${_nesspath}/src/gui
	mkdir -p ${pkgdir}/usr/bin
	mkdir -p ${_nessgobin}
	mkdir -p ${_nessguidir}
	#install binaries & symlink to /usr/bin
	_msg2 'installing binaries'
	_nessbin="${srcdir}"/go/bin
	#collect the binaries & install
	_nessbins=$( ls "$_nessbin")
	for i in $_nessbins; do
		install -Dm755 ${srcdir}/go/bin/${i} ${_nessgobin}/${i}
		ln -rTsf ${_nessgobin}/$i ${pkgdir}/usr/bin/${i}
		chmod 755 ${pkgdir}/usr/bin/${i}
	done
  _msg2 'installing gui sources'
	#install the web dir (UI)
	cp -r ${_nesssrcdir}/src/gui/static ${_nessguidir}
  _msg2 'installing scripts'
	#install the scripts
	#_nessscripts=$( ls --ignore=*.service ${srcdir}/${_pkgname}-scripts/ )
	#for i in $_nessscripts; do
		install -Dm755 ${srcdir}/ness-wallet.sh ${_nessgobin}/ness-wallet
		ln -rTsf ${_nessgobin}/ness-wallet ${pkgdir}/usr/bin/ness-wallet
		chmod 755 ${pkgdir}/usr/bin/ness-wallet
	#done
  #_msg2 'installing systemd services'
  #install the system.d service
  #  install -Dm644 ${srcdir}/${_pkgname}-scripts/${_pkgname}-node.service ${pkgdir}/usr/lib/systemd/system/${_pkgname}-node.service
  _msg2 'correcting symlink names'
	#correct symlink names
	cd ${pkgdir}/usr/bin/
  mv newcoin privateness-newcoin
  mv address_gen privateness-address-gen
  mv cipher-testdata privateness-cipher-testdata
  mv monitor-peers privateness-monitor-peers
  _msg2 'available binaries and scripts in /usr/bin :'
  ls
}

_msg2() {
(( QUIET )) && return
local mesg=$1; shift
printf "${BLUE}  ->${ALL_OFF}${BOLD} ${mesg}${ALL_OFF}\n" "$@"
}
