# Maintainer: Stephan Koglin-Fischer <stephan dot koglin-fischer at funzt dot dev>
pkgname=dashlane-cli-git
pkgver=v1.15.1.r0.g8368d87
pkgrel=37
pkgdesc="Dashlane CLI GitHub repository version bundled with asdf-vm to ensure using the correct node version."
arch=('x86_64')
url="https://github.com/Dashlane/dashlane-cli"
license=('MIT')
depends=('asdf-vm')
makedepends=('git')
source=("$pkgname::git+$url.git" '.tool-versions')
install=".INSTALL"
b2sums=('SKIP'
        '7d947ad3269a67f0987a277e10b082931ecce24330e82debd0d147f3ea24a0d9c04a91c0f711fce286a17a789c883bb626685bc08ac78040c28a5ca677a0cbf8')
# option strip is important for dcli to work properly and avoid
# 'Pkg: Error reading from file.' error in production
options=(!strip)

pkgver() {
  cd "$srcdir/$pkgname"
  git describe --long --tags --abbrev=7 | sed 's/^foo-//;s/\([^-]*-g\)/r\1/;s/-/./g'
}

check-for-asdf() {
  if ! command -v asdf &> /dev/null
  then
    echo "asdf could not be found"
    echo "Configuring asdf..."

    # Install asdf
    # for bash, fish, zsh - add more if needed
    if [[ $SHELL == *"bash"* ]]; then
      echo -e "\n. /opt/asdf-vm/asdf.sh" >> ~/.bashrc
      source ~/.bashrc
    elif [[ $SHELL == *"fish"* ]]; then
      echo -e "\nsource /opt/asdf-vm/asdf.fishh" >> ~/.config/fish/config.fish
      source ~/.config/fish/config.fish
    elif [[ $SHELL == *"zsh"* ]]; then
      echo -e "\n. /opt/asdf-vm/asdf.sh" >> ~/.zshrc
      source ~/.zshrc
    else
      echo "Unsupported shell. Please add asdf to your shell's initialization file manually."
      exit 1
    fi
  fi
}

prepare() {
  check-for-asdf

  # Install all plugins stated in .tool-versions
  awk '{print $1}' .tool-versions | xargs -n 1 asdf plugin-add
  asdf install

  cd "$srcdir/$pkgname"
  yarn install
}

build() {
  check-for-asdf
  
  cd "$srcdir/$pkgname"
  yarn run build
  # Build linux binary
  yarn pkg:linux
}

package() {
  check-for-asdf

  cd "$srcdir/$pkgname"

  # Install the tool-versions file
  install -Dm755 "$srcdir/.tool-versions" "$pkgdir/usr/share/dashlane-cli-git/.tool-versions"

  # Install the binary
  install -Dm755 "$srcdir/$pkgname/bundle/dcli-linux" "$pkgdir/usr/share/dashlane-cli-git/dcli"

  # Create a symlink to the binary
  mkdir -p "$pkgdir/usr/bin"
  cd "$pkgdir/usr/bin"
  ln -sf "./../share/dashlane-cli-git/dcli" "dcli"
}
