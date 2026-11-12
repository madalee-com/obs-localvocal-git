# Maintainer: Your Name <your@email.com>
pkgname=obs-localvocal-git
pkgver=r1234.a1b2c3d
pkgrel=1
pkgdesc="OBS Studio plugin for real-time Whisper transcription and translation"
arch=("$CARCH")
url='https://github.com/royshil/obs-localvocal'
license=('GPL-2.0-or-later')
depends=(
	'obs-studio'
	'qt6-base'
	'curl'
	'icu'
	'openblas'
	'vulkan-icd-loader'
	'ocl-icd'
)
makedepends=(
	'cmake>=3.28'
	'ninja'
	'ccache'
	'pkgconf'
	'qt6-svg'
	'simde'
	'patchelf'
	'git'
)
source=("obs-localvocal-git::git+https://github.com/royshil/obs-localvocal.git")
sha256sums=('SKIP')

prepare() {
	cd "${pkgname}"
	# CMake 4.x no longer accepts nested parentheses for grouping in if().
	# Rewrite the problematic condition in defaults.cmake to a two-level if().
	perl -i -0pe 's/if\(\(CMAKE_VERSION VERSION_GREATER_EQUAL 3\.25\.0 OR NOT CMAKE_CROSSCOMPILING\) AND NOT \(\$ENV\{ACCELERATION\} STREQUAL amd\)\)\n  set\(CPACK_DEBIAN_DEBUGINFO_PACKAGE ON\)\nendif\(\)/set(_acc "$ENV{ACCELERATION}")\nif(CMAKE_VERSION VERSION_GREATER_EQUAL 3.25.0 OR NOT CMAKE_CROSSCOMPILING)\n  if(NOT _acc STREQUAL amd)\n    set(CPACK_DEBIAN_DEBUGINFO_PACKAGE ON)\n  endif()\nendif()/s' cmake/linux/defaults.cmake
	# BuildWhispercppHelpers.cmake also uses nested parens in if()/elseif() — strip them (STREQUAL binds tighter than OR).
	perl -i -pe '
	  s/if\(\(COMPONENT STREQUAL "Whisper"\) OR \(COMPONENT STREQUAL "Whispercpp::Whisper"\)\)/if(COMPONENT STREQUAL "Whisper" OR COMPONENT STREQUAL "Whispercpp::Whisper")/;
	  s/elseif\(\(COMPONENT STREQUAL "Whisper_1"\) OR \(COMPONENT STREQUAL "Whispercpp::Whisper_1"\)\)/elseif(COMPONENT STREQUAL "Whisper_1" OR COMPONENT STREQUAL "Whispercpp::Whisper_1")/;
	  s/elseif\(\(COMPONENT STREQUAL "GGML"\) OR \(COMPONENT STREQUAL "Whispercpp::GGML"\)\)/elseif(COMPONENT STREQUAL "GGML" OR COMPONENT STREQUAL "Whispercpp::GGML")/;
	  s/elseif\(\(COMPONENT STREQUAL "WhisperCoreML"\) OR \(COMPONENT STREQUAL "Whispercpp::WhisperCoreML"\)\)/elseif(COMPONENT STREQUAL "WhisperCoreML" OR COMPONENT STREQUAL "Whispercpp::WhisperCoreML")/;
	' cmake/BuildWhispercppHelpers.cmake
}

build() {
	cd "${pkgname}"
	# Use the project's linux-x86_64 preset (Ninja, RelWithDebInfo, system curl/icu).
	cmake -B build_x86_64 --preset linux-x86_64 \
		-DCMAKE_INSTALL_PREFIX=/usr \
		-DCMAKE_INSTALL_LIBDIR=lib
	cmake --build build_x86_64
}

package() {
	cd "${pkgname}"
	# Install to $pkgdir/usr using the preset's install prefix (/usr).
	# Export DESTDIR before cmake --install (the -- "DESTDIR=..." form is rejected by this cmake).
	export DESTDIR="${pkgdir}"
	cmake --install build_x86_64
}
