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
# along with this programme.  If not, see <https://www.gnu.org/licenses/>.

project(
  'gnome-terminal',
  ['c'],
  version: '3.33.0',
  license: ['GPL-3.0-or-later'],
  default_options: [
    'buildtype=release',
    'c_std=gnu11',
    'warning_level=' + (meson.version().version_compare('>= 0.50.0') ? '0' : '1'),
    'b_ndebug=false',
  ],
  meson_version: '>= 0.49.0',
)

# Naming

gt_name       = 'gnome-terminal'
gt_namespace  = 'Terminal'
gt_iface_name = 'org.gnome.' + gt_namespace

# Requirements

vte_req_version               = '0.57.0'

gtk3_req_version              = '3.12.0'
gtk4_req_version              = '4.0.0'

dconf_req_version             = '0.14.0'
gio_req_version               = '2.34.0'
glib_req_version              = '2.42.0'
gsettings_schemas_req_version = '0.1.0'
pango_req_version             = '1.0'
pcre2_req_version             = '10.21'

vte_gtk3_api_version          = '2.91'
vte_gtk4_api_version          = '3.91'

# Versioning

gt_version = meson.project_version()
version_array = gt_version.split('.')
gt_major_version = version_array[0].to_int()
gt_minor_version = version_array[1].to_int()
gt_micro_version = version_array[2].to_int()

# i18n

gt_gettext_domain = gt_name

# Directories

gt_datadir = get_option('datadir')
gt_libdir = get_option('libdir')
gt_libexecdir = get_option('libexecdir')
gt_localedir = get_option('localedir')
gt_prefix = get_option('prefix')

gt_pkgdatadir = gt_datadir / gt_name

# Debug

enable_debug = get_option('debugg') or get_option('debug') or get_option('buildtype').contains('debug')

# Meson modules

gnome = import('gnome')
pkg = import('pkgconfig')

i18n = import('i18n')
podir = meson.current_source_dir() / 'po'

# Compilers

cc = meson.get_compiler('c')

top_inc = include_directories('.')

# Start config.h

config_h = configuration_data()

config_h.set_quoted('GETTEXT_PACKAGE', gt_gettext_domain)
config_h.set_quoted('VERSION', gt_version)
config_h.set('ENABLE_DEBUG', enable_debug)

# FIXME AC_USE_SYSTEM_EXTENSIONS also supported non-gnu systems
config_h.set10('_GNU_SOURCE', true)

# Compiler

# Meson has a misfeature where it allows the user to override the -std option
# for the C/C++ compiler. Disallow that.

assert(get_option('c_std') == 'gnu11', 'cannot override C std version')

# Asserts must not be disabled

assert(get_option('b_ndebug') == 'false', 'assertions may not be disabled')

# Compiler flags

compiler_flags_common = [
  '-Wall',
  '-Wcast-align',
  '-Wendif-labels',
  '-Werror=implicit-function-declaration',
  '-Werror=init-self',
  '-Werror=missing-include-dirs',
  '-Werror=missing-prototypes',
  '-Werror=pointer-arith',
  '-Wextra',
  '-Wfloat-equal',
  '-Wformat-signedness',
  '-Winvalid-pch',
  '-Wlogical-op',
  '-Wmissing-declarations',
  '-Wmissing-format-attribute',
  '-Wmissing-include-dirs',
  '-Wmissing-noreturn',
  '-Wno-missing-field-initializers',
  '-Wno-switch-enum',
  '-Wno-unused-parameter',
  '-Wpacked',
  '-Wshadow',
  '-Wsign-compare',
  '-Wstrict-aliasing=2',
  '-Wundef',
  '-Wuninitialized',
  '-Wunsafe-loop-optimizations',
  '-Wvla',
  '-Wwrite-strings',
  '-fdiagnostics-show-option',
  '-fno-common',
  '-fstack-protector',
  '-fstack-protector-strong',
  '-fvisibility=hidden',
]

if enable_debug
  compiler_flags_common += [
    '-ggdb3',
  ]
endif

# These are currently needed but the code should be fixed instead
compiler_flags_common_undesirable = [
  '-fno-strict-aliasing'
]

compiler_flags_c_only = [
  '-Waggregate-return',
  '-Wimplicit',
  '-Wimplicit-fallthrough=3',
  '-Wnested-externs',
  '-Wold-style-definition',
  '-Wstrict-prototypes',
]

global_cflags = cc.get_supported_arguments(compiler_flags_common +
                                           compiler_flags_common_undesirable +
                                           compiler_flags_c_only)

# Meson problem: GCC only accepts the latter 2 options of the 3 below
# if the first is *also* passed, which doesn't work with get_supported_arguments()
# above. So just add these unconditionally, since all compilers we support
# accept these flags.

compiler_flags_format_warnings = [
  '-Werror=format=2',
  '-Werror=format-nonliteral',
  '-Werror=format-security',
]

global_cflags += compiler_flags_format_warnings

# ... and now make these flags the default

add_project_arguments(global_cflags, language: 'c')

# Dependencies

dconf_dep     = dependency('dconf',      version: '>=' + dconf_req_version)
gio_dep       = dependency('gio-2.0',    version: '>=' + gio_req_version)
glib_dep      = dependency('glib-2.0',   version: '>=' + glib_req_version)
gobject_dep   = dependency('gobject-2.0')
gsettings_dep = dependency('gsettings-desktop-schemas', version: '>=' + gsettings_schemas_req_version)
pango_dep     = dependency('pango',      version: '>=' + pango_req_version)
pcre2_dep     = dependency('libpcre2-8', version: '>=' + pcre2_req_version)
pthreads_dep  = dependency('threads')
uuid_dep      = dependency('uuid')
zlib_dep      = dependency('zlib')

if get_option('gtk3')
  gdk3_dep     = dependency('gdk-3.0')
  gtk3_dep     = dependency('gtk+-3.0', version: '>=' + gtk3_req_version)
  vte_gtk3_dep = dependency('vte-' + vte_gtk3_api_version, version: '>=' + vte_req_version)

  gdk3_targets = gdk3_dep.get_pkgconfig_variable('targets')
  if gdk3_targets.contains('x11')
    x11_dep = dependency('x11')
  else
    x11_dep = dependency('', required: false)
  endif
else
  gtk3_dep     = dependency('', required: false)
  vte_gtk3_dep = dependency('', required: false)
  x11_dep      = dependency('', required: false)
endif

if get_option('gtk4')
  gtk4_dep     = dependency('gtk+-4.0', version: '>=' + gtk4_req_version)
  vte_gtk4_dep = dependency('vte-' + vte_gtk4_api_version, version: '>=' + vte_req_version)
else
  gtk4_dep     = dependency('', required: false)
  vte_gtk4_dep = dependency('', required: false)
endif

libm_dep = cc.find_library('m')

# Dependency directories

dbus_dep = dependency('dbus-1')
dbus_servicedir = dbus_dep.get_pkgconfig_variable(
  'session_bus_services_dir',
  define_variable: ['datadir', gt_prefix / gt_datadir],
)
dbus_interfacedir = dbus_dep.get_pkgconfig_variable('interfaces_dir')

gio_schemadir = gio_dep.get_pkgconfig_variable(
  'schemasdir',
  define_variable: ['datadir', gt_prefix / gt_datadir],
  default: gt_prefix / gt_datadir / 'glib-2.0' / 'schemas',
)

# It is correct for this to be in ${prefix}/lib, even on systems where that
# does not match ${libdir}. This is what systemd uses on such platforms.
systemd_userunitdir = gt_prefix / 'lib' / 'systemd' / 'user'

# Shell search provider option

if get_option('search_provider')
  gs_searchproviderdir = gt_datadir / 'gnome-shell' / 'search-providers'

  config_h.set('ENABLE_SEARCH_PROVIDER', true)
endif

# Nautilus extension option

if get_option('nautilus_extension')
  assert(get_option('gtk3'), 'nautilus extension requires gtk3')

  nautilus_dep = dependency('libnautilus-extension', version: '>= 3.0.0')
  nautilus_extensiondir = nautilus_dep.get_pkgconfig_variable(
    'extensiondir',
    define_variable: ['libdir', gt_prefix / gt_libdir],
  )
endif

# Appstream data

appdatadir = gt_prefix / gt_datadir / 'metainfo'

i18n.merge_file(
  'org.gnome.Terminal.metainfo.xml',
  input: 'org.gnome.Terminal.metainfo.xml' + '.in',
  output: '@BASENAME@',
  po_dir: podir,
  install: true,
  install_dir: appdatadir,
)

if get_option('nautilus_extension')
  appdata = gt_iface_name + '.Nautilus.metainfo.xml'

  i18n.merge_file(
    'org.gnome.Terminal.Nautilus.metainfo.xml',
    input: 'org.gnome.Terminal.Nautilus.metainfo.xml' + '.in',
    output: '@BASENAME@',
#    output: 'org.gnome.Terminal.Nautilus.metainfo.xml',
    po_dir: podir,
    install: true,
    install_dir: appdatadir,
)
endif

# Desktop file

desktopdatadir = gt_prefix / gt_datadir / 'applications'

desktop_conf = {
  'VERSION': gt_version
}

desktop_in = configure_file(
  input: 'org.gnome.Terminal.desktop.in.in',
  output: '@BASENAME@',
  configuration: desktop_conf,
  install: false,
)

i18n.merge_file(
  'org.gnome.Terminal.desktop',
  input: desktop_in,
  output: '@BASENAME@',
  type: 'desktop',
  po_dir: podir,
  install: true,
  install_dir: desktopdatadir,
)

meson.add_install_script(
  'meson_desktopfile.py',
  desktopdatadir,
  'org.gnome.Terminal.desktop',
)

# Write config.h

configure_file(
  output: 'config.h',
  configuration: config_h,
)

# Subdirs

subdir('src')
subdir('data/icons')
subdir('po')

if get_option('docs')
  subdir('help')
endif

# Simple compat Makefile

makefile_conf = configuration_data()
makefile_conf.set('srcdir', meson.current_source_dir())
makefile_conf.set('builddir', meson.current_build_dir())

configure_file(
  input: 'Makefile.meson',
  output: '@BASENAME@',
  configuration: makefile_conf,
)

# .gitignore everything in the build directory

configure_file(
  output: '.gitignore',
  command: ['echo', '**/**',],
  capture: true,
  install: false,
)

# Summary

output = '\n'
output += 'Configuration for GNOME Terminal:\n\n'
output += '  Version:                ' + gt_version + '\n'
output += '\n'
output += '  C compiler:             ' + cc.get_id() + '\n\n'
output += '  Coverage:               ' + get_option('b_coverage').to_string() + '\n'
output += '  Documentation:          ' + get_option('docs').to_string() + '\n'
output += '  Debug:                  ' + enable_debug.to_string() + '\n'
output += '  GTK+ 3.0:               ' + get_option('gtk3').to_string() + '\n'
output += '  GTK+ 4.0:               ' + get_option('gtk4').to_string() + '\n\n'
output += '  Nautilus Extension:     ' + get_option('nautilus_extension').to_string() + '\n'
output += '  Search Provider:        ' + get_option('search_provider').to_string() + '\n'
output += '\n'
output += '  Prefix:                 ' + get_option('prefix') + '\n'
output += '  Appstream data dir:     ' + appdatadir + '\n'
output += '  D-BUS interfaces dir:   ' + dbus_interfacedir + '\n'
output += '  D-BUS services dir:     ' + dbus_servicedir + '\n'
output += '  Desktop data dir:       ' + desktopdatadir + '\n'
output += '  Schemas dir:            ' + gio_schemadir + '\n'
output += '  Systemd user unit dir:  ' + dbus_servicedir + '\n'
if get_option('nautilus_extension')
  output += '  Nautilus extension dir: ' + nautilus_extensiondir + '\n'
endif
if get_option('search_provider')
  output += '  Search provider dir:    ' + gs_searchproviderdir + '\n'
endif
message(output)

# Done
