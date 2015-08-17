# Maintainer: Andzai <andzai1995@gmail.com>
# Contributors: Det, Damian Nowak

_pkgname=jdk
pkgname=oracle-jdk8
_major=8
_minor=25
_build=b17
pkgver=${_major}u${_minor}
pkgrel=2
pkgdesc="Oracle Java Development Kit (32-bit)"
arch=('i686' 'x86_64')
url=http://www.oracle.com/technetwork/java/javase/downloads/index.html
license=('custom')
if [$(uname -m) == i686]; then
	depends=('ca-certificates-java' 'desktop-file-utils' 'hicolor-icon-theme' 'java-environment-common' 
         	'java-runtime-common' 'glibc' 'libjpeg-turbo' 'lcms2' 'libxrender'
         	'libxtst' 'nss' 'shared-mime-info' 'xdg-utils')
	optdepends=('derby: for Oracle Apache Derby Java database (AUR)'
            	'eclipse: "Oracle Java Mission Control" plugins for Eclipse'
            	'alsa-lib: for basic sound support'
            	'gtk2: for Gtk+ look and feel (desktop)'
            	'ttf-font: fonts'
           	 'visualvm: for lightweight profiling capabilities')
	provides=("java-runtime=$_major" "java-runtime-headless=$_major" "java-web-start=$_major" "java-environment=$_major"
          		"java-runtime-jre=$_major" "java-runtime-headless-jre=$_major" "java-web-start-jre=$_major" "java-environment-jdk=$_major")
elif [$(uname -m) == x86_64]; then
	depends=('ca-certificates-java' 'desktop-file-utils' 'hicolor-icon-theme' 'java-environment-common' 
         	'java-runtime-common' 'lib32-glibc' 'lib32-libjpeg-turbo' 'lib32-lcms2' 'lib32-libxrender'
         	'lib32-libxtst' 'lib32-nss' 'shared-mime-info' 'xdg-utils')
	optdepends=('derby: for Oracle Apache Derby Java database (AUR)'
            'eclipse: "Oracle Java Mission Control" plugins for Eclipse'
            'lib32-alsa-lib: for basic sound support'
            'lib32-gtk2: for Gtk+ look and feel (desktop)'
            'ttf-font: fonts'
            'visualvm: for lightweight profiling capabilities')
	provides=("java-runtime=$_major" "java-runtime-headless=$_major" "java-web-start=$_major" "java-environment=$_major"
          	"java-runtime-jre=$_major" "java-runtime-headless-jre=$_major" "java-web-start-jre=$_major" "java-environment-jdk=$_major")
fi

# Variables
DLAGENTS=('http::/usr/bin/curl -LC - -b oraclelicense=a -O')
_arch=i386
_jname=${_pkgname}${_major}-32
_jvmdir=/usr/lib/jvm/java-$_major-$_pkgname-32

backup=("etc/java-$_jname/$_arch/server/Xusage.txt"
        "etc/java-$_jname/$_arch/jvm.cfg"
        "etc/java-$_jname/images/cursors/cursors.properties"
        "etc/java-$_jname/management/jmxremote.access"
        "etc/java-$_jname/management/jmxremote.password.template"
        "etc/java-$_jname/management/management.properties"
        "etc/java-$_jname/management/snmp.acl.template"
        "etc/java-$_jname/security/java.policy"
        "etc/java-$_jname/security/java.security"
        "etc/java-$_jname/security/javaws.policy"
        "etc/java-$_jname/calendars.properties"
        "etc/java-$_jname/content-types.properties"
        "etc/java-$_jname/flavormap.properties"
        "etc/java-$_jname/fontconfig.properties.src"
        "etc/java-$_jname/hijrah-config-umalqura.properties"
        "etc/java-$_jname/javafx.properties"
        "etc/java-$_jname/jvm.hprof.txt"
        "etc/java-$_jname/logging.properties"
        "etc/java-$_jname/net.properties"
        "etc/java-$_jname/psfont.properties.ja"
        "etc/java-$_jname/psfontj2d.properties"
        "etc/java-$_jname/sound.properties")
options=('!strip') # JDK debug-symbols
install=$pkgname.install
source=("http://download.oracle.com/otn-pub/java/jdk/$pkgver-$_build/$_pkgname-$pkgver-linux-i586.tar.gz"
        "jconsole-$_jname.desktop"
        "jmc-$_jname.desktop"
        "jvisualvm-$_jname.desktop"
        "policytool-$_jname.desktop")
md5sums=('b5b16247f66643727d9b6d4bc7c5efda'
         '18cb6fcd9475c01072b34c040b0001f4'
         '055870679721821382196431f24da3be'
         '8f36b78fcafd8d92aaba3d9235d945c7'
         '1c370a3585188afc963d6de097bf7a2e')

package() {
  msg2 "Creating required dirs"
  cd ${_pkgname}1.${_major}.0_${_minor}
  install -d "$pkgdir"/{usr/{lib{32/mozilla/plugins,/jvm/java-$_major-$_pkgname-32/bin},share/licenses/java$_major-$_pkgname-32},etc/.java/.systemPrefs}

  msg2 "Preparing"
  # Link duplicate binaries from jre/
  for i in $(ls jre/bin/); do
    ln -sf $_jvmdir/jre/bin/$i bin/$i
  done

  # Link NPAPI plugin
  ln -sf $_jvmdir/jre/lib/$_arch/libnpjp2.so "$pkgdir"/usr/lib32/mozilla/plugins/libnpjp2-$_pkgname$_major.so

  # Replace JKS keystore with 'ca-certificates-java'
  ln -sf /etc/ssl/certs/java/cacerts jre/lib/security/cacerts

  # Suffix .desktops, icons and MIME packages
  for i in $(find jre/lib/desktop/ -type f); do
    rename -- "." "-$_jname." $i
  done

  # Suffix man pages
  rename -- ".1" "-$_jname.1" man/{,ja_JP.UTF-8/}man1/*

  # Fix .desktop paths
  sed -e "s,Exec=,&$_jvmdir/jre/bin/," \
      -e "s/\.png/-$_jname/" \
      -i jre/lib/desktop/applications/*

  msg2 "Removing redundancies"
  rm -r db/ jre/lib/fontconfig.*.{bfc,properties.src} jre/plugin/ jre/{COPYRIGHT,LICENSE,README,*.txt} lib/visualvm/ man/ja # lib/missioncontrol

  msg2 "Moving stuff in place"
  # .desktops + icons
  mv jre/lib/desktop/* "$pkgdir"/usr/share/
  install -m644 "$srcdir"/*.desktop "$pkgdir"/usr/share/applications/

  # Move/link configs: /usr/lib/jvm/java-$_jname/jre/lib -> /etc
  for new_etc in ${backup[@]}; do
    old_usr=jre/lib/${new_etc#*$_jname/}
    install -Dm644 $old_usr "$pkgdir"/$new_etc
    ln -sf /$new_etc $old_usr
  done

  # Man pages
  mv man/ja_JP.UTF-8/ man/ja/
  mv man/ "$pkgdir"/usr/share/

  # Licenses
  mv COPYRIGHT LICENSE *.txt "$pkgdir"/usr/share/licenses/java$_major-$_pkgname-32/
  ln -sf /usr/share/licenses/java$_major-$_pkgname-32/ "$pkgdir"/usr/share/licenses/$pkgname

  # Do the move
  mv * "$pkgdir"/$_jvmdir

  msg2 "Enabling copy+paste in unsigned applets"
  # Copy/paste from system clipboard to unsigned Java applets has been disabled since 6u24:
  # - https://blogs.oracle.com/kyle/entry/copy_and_paste_in_java
  # - http://slightlyrandombrokenthoughts.blogspot.com/2011/03/oracle-java-applet-clipboard-injection.html
  _line=$(awk '/permission/{a=NR}; END{print a}' "$pkgdir"/etc/java-$_jname/security/java.policy)
  sed "$_line a\\\\n \
        // (AUR) Allow unsigned applets to read system clipboard, see:\n \
        // - https://blogs.oracle.com/kyle/entry/copy_and_paste_in_java\n \
        // - http://slightlyrandombrokenthoughts.blogspot.com/2011/03/oracle-java-applet-clipboard-injection.html\n \
        permission java.awt.AWTPermission \"accessClipboard\";" \
     -i "$pkgdir"/etc/java-$_jname/security/java.policy
}

# TODO package 'mission control' on its own, and add optional dependency here
# (WARNING: it's a full Eclipse + plugin)
