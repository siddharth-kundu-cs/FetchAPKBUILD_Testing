# Maintainer: CleanStart <packages-admin@cleanstart.com>

pkgname=prometheus-alertmanager
pkgver=0.33.0
pkgrel=0
pkgdesc="Prometheus Alertmanager"
url="https://github.com/prometheus/alertmanager"
#riscv64: aws dependency fails to build
arch="all !riscv64"
license="Apache-2.0"
gitcommitid="5d3ceb55bf3775ea152dcdf3803bbbb2b4afed54"
install="$pkgname.pre-install"
makedepends="go1.26"
source="
	$pkgname-$pkgver.tar.gz::https://github.com/prometheus/alertmanager/archive/v$pkgver.tar.gz
	alertmanager.confd
	alertmanager.initd
"
subpackages="$pkgname-openrc"
options="!check" # timing-sensitive upstream tests

# secfixes:
#   0.32.0-r2:
#     - CVE-2026-39841
#     - CVE-2026-46798
#     - CVE-2026-25784
#     - CVE-2026-29031
#   0.32.0-r1:
#     - CVE-2026-39835
#     - CVE-2026-46598
#     - CVE-2026-25681
#     - CVE-2026-27136
#     - CVE-2026-42502
#     - CVE-2026-42506
#     - CVE-2026-25680
#     - CVE-2026-39821
#     - CVE-2026-39828
#     - CVE-2026-39827
#     - CVE-2026-39829
#     - CVE-2026-46597
#     - CVE-2026-39830
#     - CVE-2026-39831
#     - CVE-2026-39832
#     - CVE-2026-39833
#     - CVE-2026-39834
#     - CVE-2026-42508
#     - CVE-2026-46595
#   0.32.0-r0:
#     - CVE-2026-39882
#     - CVE-2026-39883
#   0.26.0-r0:
#     - CVE-2023-40577

export GOCACHE="${GOCACHE:-"$srcdir/go-cache"}"
export GOTMPDIR="${GOTMPDIR:-"$srcdir"}"
export GOMODCACHE="${GOMODCACHE:-"$srcdir/go"}"
export GOROOT="/usr/lib/go1.26/"
export PATH="$GOROOT/bin:$PATH"
builddir="$srcdir/alertmanager-$pkgver"

prepare() {
	default_prepare

	mkdir -p "$builddir"/ui/app/dist
	touch "$builddir"/ui/app/dist/placeholder

	cd "$builddir"

	go get golang.org/x/sys@latest
	go get golang.org/x/net@latest
	go get golang.org/x/crypto@latest
	go get go.opentelemetry.io/otel/sdk@v1.43.0
	go get go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp@v1.43.0

	go mod vendor
}

build() {
	cd "$builddir"
	for cmd in amtool alertmanager
	do
		go build \
			-trimpath \
			-mod=vendor \
			-ldflags "-extldflags \"$LDFLAGS\" \
				-X github.com/prometheus/common/version.Version=$pkgver \
				-X github.com/prometheus/common/version.Revision=$pkgrel \
				-X github.com/prometheus/common/version.Branch=Alpine \
				-X github.com/prometheus/common/version.BuildUser=Alpine \
				-X github.com/prometheus/common/version.BuildDate=2020-01-08" \
			./cmd/$cmd
	done
}

check() {
	go test ./...
}

package() {
	install -Dm755 alertmanager "$pkgdir"/usr/bin/alertmanager
	install -Dm755 amtool "$pkgdir"/usr/bin/amtool

	install -Dm755 "$srcdir"/alertmanager.initd \
		"$pkgdir"/etc/init.d/alertmanager
	install -Dm644 "$srcdir"/alertmanager.confd \
		"$pkgdir"/etc/conf.d/alertmanager
	install -dm644 "$pkgdir"/var/lib/alertmanager/data

	install -Dm644 examples/ha/alertmanager.yml \
		"$pkgdir"/etc/alertmanager/alertmanager.yml
}

sha512sums="
8aaebfe0b9b499ad8bcc738d970b5a736e63b385e532fb30f79696d9174f7276c4503bc447c4a925523d5238af48a88d7842254e2deadab15d7f957d18b408ba  prometheus-alertmanager-0.33.0.tar.gz
58420cf10ed51ec389d21ffdd5b4a0e588f0dc78b1069e32d0db1e0215f64c1c980d8f539ae902839f2f9342090b50ce1db756839f3676ee18b77548ce8f99c8  alertmanager.confd
783636612f4521a042e890b3c53fa8c859574a533f540f01bbbb2b12d28b7998c69592e4c5f4d8868d32401ed93ae92ab1fa03129cc9a741d1221cd76eb4fb6b  alertmanager.initd
"
