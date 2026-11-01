# LightDM GTK Greeter
**LightDM GTK Greeter** is a greeter that has moderate requirements (GTK).

This project is one of many greeters for [LightDM](https://github.com/canonical/lightdm).

## About LightDM

LightDM is a cross-desktop display manager. A display manager is a daemon that:

- Runs display servers (e.g. X) where necessary.
- Runs greeters to allow users to pick which user account and session type to use.
- Allows greeters to perform authentication using PAM.
- Runs session processes once authentication is complete.
- Provides remote graphical login options.

Key features of LightDM are:
- Cross-desktop - supports different desktop technologies.
- Supports different display technologies (X, Mir, Wayland ...).
- Lightweight - low memory usage and fast performance.
- Guest sessions.
- Supports remote login (incoming - XDMCP, VNC, outgoing - XDMCP, pluggable).
- Comprehensive test suite.

## Configuration

LightDM configuration is provided by the following files:

```
/usr/share/lightdm/lightdm.conf.d/*.conf
/etc/lightdm/lightdm.conf.d/*.conf
/etc/lightdm/lightdm.conf
```

**LightDM GTK Greeter uses `lightdm-gtk-greeter.conf` for its configuration.**

System provided configuration should be stored in `/usr/share/lightdm/lightdm.conf.d/`. System administrators can override this configuration by adding files to `/etc/lightdm/lightdm.conf.d/` and `/etc/lightdm/lightdm.conf`. Files are read in the above order and combined together to make the LightDM configuration.

For example, if a sysadmin wanted to override the system configured default session (provided in `/usr/share/lightdm/lightdm.conf.d`) they should make a file `/etc/lightdm/lightdm.conf.d/50-myconfig.conf` with the following:

```
[Seat:*]
user-session=mysession
```

Configuration is in keyfile format. For most installations you will want to change the keys in the `[Seat:*]` section as this applies to all seats on the system (normally just one). A configuration file showing all the possible keys is provided in [`data/lightdm.conf`](https://github.com/Canonical/lightdm/blob/master/data/lightdm.conf).

## Questions

Questions about LightDM and LightDM GTK Greeter should be asked on the [mailing list](https://lists.freedesktop.org/mailman/listinfo/lightdm). All questions are welcome.

[Stack Overflow](https://stackoverflow.com/search?q=lightdm) and [Ask Ubuntu](https://askubuntu.com/search?q=lightdm) are good sites for frequently asked questions.

## Links
 - [Homepage](https://github.com/xubuntu/lightdm-gtk-greeter)
 - [Releases](https://github.com/xubuntu/lightdm-gtk-greeter/releases)
 - [Bug Reports](https://github.com/xubuntu/lightdm-gtk-greeter/issues)
 - [Translations](https://explore.transifex.com/xubuntu/lightdm-gtk-greeter/)


## Corporate Fluent fork

This fork adds a Fluent-inspired login layout, external GTK CSS, successful-login
identity caching (including non-enumerated AD users), and an explicit Other user
action. See [the refactoring map, configuration and test guide](docs/fluent-greeter.md)
for deployment settings and the precise scope of `suppress-session-warnings`.

## Compiling

No Linux Mint XFCE, instale as dependências:

```bash
sudo apt update
sudo apt install -y \
  build-essential autoconf automake libtool autopoint gettext \
  git2cl gobject-introspection intltool \
  libgtk-3-dev liblightdm-gobject-1-dev libx11-dev \
  pkg-config xfce4-dev-tools \
  libayatana-indicator3-dev libayatana-ido3-dev \
  python3 xvfb xauth xdotool dbus-x11 \
  adwaita-icon-theme librsvg2-common
```

Clone sua branch e compile:

```bash
git clone --branch work https://github.com/rafaelfrancapc/monolito-gtk-greeter.git
cd monolito-gtk-greeter

NOCONFIGURE=1 ./autogen.sh
mkdir build
cd build
../configure --prefix=/usr --sysconfdir=/etc --with-libindicator=ayatana
make -j"$(nproc)"
make check
```

Para validar o pacote completo:

```bash
make distcheck DISTCHECK_CONFIGURE_FLAGS="--with-libindicator=ayatana"
```

O binário compilado estará em:

```bash
build/src/lightdm-gtk-greeter
```

Para instalar localmente após testar:

```bash
sudo make install
sudo install -Dm644 ../data/gtk.css /etc/lightdm/gtk.css
```

Use esta configuração em `/etc/lightdm/lightdm-gtk-greeter.conf`:

```ini
[greeter]
background=/usr/share/backgrounds/linuxmint-wallpapers/jpanchal_curves.jpg
user-background=false
css-file=/etc/lightdm/gtk.css
remember-last-user=true
show-user-list=false
suppress-session-warnings=false
clock-format=%H:%M
date-format=%A, %e %B
position=50%,center 60%,center
panel-position=bottom
```

Antes de trocar o greeter em uso, mantenha uma sessão de terminal aberta. Em Mint XFCE, confirme o arquivo de configuração do LightDM, normalmente em `/etc/lightdm/lightdm.conf` ou `/etc/lightdm/lightdm.conf.d/`, e ajuste:

```ini
[Seat:*]
greeter-session=lightdm-gtk-greeter
```

Depois reinicie o display manager:

```bash
sudo systemctl restart lightdm
```

Isso encerra sua sessão gráfica atual.

### Pacote Debian

Para gerar um pacote instalável sem ferramentas de compilação nas máquinas de
destino, instale as dependências apenas na máquina de build e execute:

```bash
sudo apt install build-essential devscripts debhelper
sudo apt build-dep .
dpkg-buildpackage -us -uc -b
```

O `.deb` será criado no diretório pai. Nas máquinas Mint de destino basta:

```bash
sudo apt install ../lightdm-gtk-greeter_2.0.9+monolito1-1_amd64.deb
```

Veja [docs/fluent-greeter.md](docs/fluent-greeter.md) para compatibilidade,
publicação pelo GitHub Actions e atualização do pacote.
