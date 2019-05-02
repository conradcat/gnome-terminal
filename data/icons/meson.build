# Copyright © 2018, 2019 Iñigo Martínez
# Copyright © 2019 Christian Persch
#
# This programme is free software; you can redistribute it and/or modify it
# under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3 of the License, or (at your
# option) any later version.
#
# This programme is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
# See the GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this library.  If not, see <https://www.gnu.org/licenses/>.

icondir = gt_datadir / 'icons' / 'hicolor'

install_data(
  'hicolor_apps_scalable_org.gnome.Terminal.svg',
  install_dir: icondir / 'scalable' /'apps',
  rename: 'org.gnome.Terminal.svg',
)

install_data(
  'hicolor_apps_symbolic_org.gnome.Terminal-symbolic.svg',
  install_dir: icondir / 'symbolic' / 'apps',
  rename: 'org.gnome.Terminal-symbolic.svg',
)

meson.add_install_script(
  'meson_updateiconcache.py',
  icondir,
)
