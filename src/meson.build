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

# Sources

debug_sources = files(
  'terminal-debug.c',
  'terminal-debug.h',
)

enum_headers = files(
  'terminal-enums.h'
)

enum_sources = gnome.mkenums(
  'terminal-type-builtins.h',
  sources: enum_headers,
  c_template: 'terminal-type-builtins.c.template',
  h_template: 'terminal-type-builtins.h.template',
  install_header: false,
)

gdbus_data = files(
  'org.gnome.Terminal.xml',
)

gdbus_sources = gnome.gdbus_codegen(
  'terminal-gdbus-generated',
  sources: gdbus_data,
  interface_prefix: gt_iface_name,
  namespace: gt_namespace,
  object_manager: true,
)

i18n_sources = files(
  'terminal-intl.h',
  'terminal-i18n.c',
  'terminal-i18n.h',
)

marshal_sources = gnome.genmarshal(
  'terminal-marshal',
  sources: 'terminal-marshal.list',
  prefix: '_terminal_marshal',
  internal: true,
)

regex_sources = files(
  'terminal-regex.c',
  'terminal-regex.h',
)

resource_data = files(
  'preferences.ui',
  'search-popover.ui',
  'terminal-headerbar.ui',
  'terminal-headermenu.ui',
  'terminal-notebook-menu.ui',
  'terminal-window.ui',
  'terminal.about',
  'terminal.common.css',
)

mnemonics_conf = {
  'WITH_MNEMONIC_START': '',
  'WITH_MNEMONIC_END': '',
  'WITHOUT_MNEMONIC_START': '<!--',
  'WITHOUT_MNEMONIC_END': '-->',
}

resource_data += configure_file(
  input: 'terminal-menubar.ui.in',
  output: 'terminal-menubar-with-mnemonics.ui',
  configuration: mnemonics_conf,
)

mnemonics_conf = {
  'WITH_MNEMONIC_START': '<!--',
  'WITH_MNEMONIC_END': '-->',
  'WITHOUT_MNEMONIC_START': '',
  'WITHOUT_MNEMONIC_END': '',
}

resource_data += configure_file(
  input: 'terminal-menubar.ui.in',
  output: 'terminal-menubar-without-mnemonics.ui',
  configuration: mnemonics_conf,
)

resource_prefix = 'terminal'

resource_sources = gnome.compile_resources(
  'terminal-resources',
  'terminal.gresource.xml',
  c_name: 'terminal',
  dependencies: resource_data,
  export: true,
)

settings_sources = files(
  'terminal-profiles-list.c',
  'terminal-profiles-list.h',
  'terminal-schemas.h',
  'terminal-settings-list.c',
  'terminal-settings-list.h',
)

version_conf = {
  'TERMINAL_MAJOR_VERSION': gt_major_version,
  'TERMINAL_MINOR_VERSION': gt_minor_version,
  'TERMINAL_MICRO_VERSION': gt_micro_version,
}

version_sources = [
  configure_file(
    input: 'terminal-version.h.in',
    output: '@BASENAME@',
    configuration: version_conf,
  )
]

# Server

server_sources = debug_sources + enum_sources + gdbus_sources + i18n_sources + marshal_sources + settings_sources + regex_sources + resource_sources + version_sources + files(
  'eggshell.c',
  'eggshell.h',
  'profile-editor.c',
  'profile-editor.h',
  'server.c',
  'terminal-accels.c',
  'terminal-accels.h',
  'terminal-app.c',
  'terminal-app.h',
  'terminal-defines.h',
  'terminal-enums.h',
  'terminal-encoding.c',
  'terminal-encoding.h',
  'terminal-gdbus.c',
  'terminal-gdbus.h',
  'terminal-headerbar.c',
  'terminal-headerbar.h',
  'terminal-icon-button.h',
  'terminal-icon-button.c',
  'terminal-info-bar.c',
  'terminal-info-bar.h',
  'terminal-libgsystem.h',
  'terminal-mdi-container.c',
  'terminal-mdi-container.h',
  'terminal-menu-button.h',
  'terminal-menu-button.c',
  'terminal-notebook.c',
  'terminal-notebook.h',
  'terminal-pcre2.h',
  'terminal-prefs.c',
  'terminal-prefs.h',
  'terminal-screen.c',
  'terminal-screen.h',
  'terminal-screen-container.c',
  'terminal-screen-container.h',
  'terminal-search-popover.c',
  'terminal-search-popover.h',
  'terminal-tab-label.c',
  'terminal-tab-label.h',
  'terminal-util.c',
  'terminal-util.h',
  'terminal-window.c',
  'terminal-window.h',
)

if get_option('search_provider')
  server_sources += files(
    'terminal-search-provider.c',
    'terminal-search-provider.h',
  )

  search_provider_gdbus_data = [
    dbus_interfacedir / 'org.gnome.ShellSearchProvider2.xml',
  ]

  server_sources += gnome.gdbus_codegen(
    'terminal-search-provider-gdbus-generated',
    sources: search_provider_gdbus_data,
    interface_prefix: 'org.gnome.Shell',
    namespace: gt_namespace,
  )
endif

server_cflags = [
  '-DTERMINAL_COMPILATION',
  '-DTERM_LOCALEDIR="@0@"'.format(gt_prefix / gt_localedir),
  '-DVTE_DISABLE_DEPRECATION_WARNINGS',
]

server_common_deps = [
  dconf_dep,
  gio_dep,
  glib_dep,
  gsettings_dep,
  libm_dep,
  pcre2_dep,
  pthreads_dep,
  uuid_dep,
]

server_gtk3_deps = server_common_deps + [
  gtk3_dep,
  vte_gtk3_dep,
  x11_dep,
]

server_gtk3 = executable(
  gt_name + '-server',
  sources: server_sources,
  include_directories: top_inc,
  dependencies: server_gtk3_deps,
  c_args: server_cflags,
  install: true,
  install_dir: gt_libexecdir,
)

# Server data files

service_conf = {
  'libexecdir': gt_prefix / gt_libexecdir
}

configure_file(
  input: 'org.gnome.Terminal.service.in',
  output: '@BASENAME@',
  configuration: service_conf,
  install: true,
  install_dir: dbus_servicedir,
)

configure_file(
  input: 'gnome-terminal-server.service.in',
  output: '@BASENAME@',
  configuration: service_conf,
  install: true,
  install_dir: systemd_userunitdir,
)

install_data(
  'org.gnome.Terminal.gschema.xml',
  install_dir: gio_schemadir,
)

meson.add_install_script(
  'meson_compileschemas.py',
  gio_schemadir,
)

if get_option('search_provider')
  install_data(
    'gnome-terminal-search-provider.ini',
    install_dir: gs_searchproviderdir,
  )
endif

# Client

client_sources = debug_sources + enum_sources + gdbus_sources + i18n_sources + settings_sources + version_sources + files(
  'terminal.c',
  'terminal-client-utils.c',
  'terminal-client-utils.h',
  'terminal-defines.h',
  'terminal-libgsystem.h',
  'terminal-options.c',
  'terminal-options.h',
)

client_cflags = [
  '-DTERMINAL_CLIENT',
  '-DTERMINAL_COMPILATION',
  '-DTERM_LOCALEDIR="@0@"'.format(gt_prefix / gt_localedir),
]

client_deps = [
  dconf_dep,
  gio_dep,
  glib_dep,
  uuid_dep,
]

client_deps += [
  gtk3_dep,
  vte_gtk3_dep,
  x11_dep,
]

client = executable(
  'gnome-terminal',
  client_sources,
  include_directories: top_inc,
  dependencies: client_deps,
  c_args: client_cflags,
  install: true,
)

# Nautilus extension

if get_option('nautilus_extension') and get_option('gtk3')
  nautilus_sources = enum_sources + gdbus_sources + i18n_sources + files(
    'terminal-client-utils.c',
    'terminal-client-utils.h',
    'terminal-defines.h',
    'terminal-libgsystem.h',
    'terminal-nautilus.c',
  )

  nautilus_cflags = [
    '-DTERM_LOCALEDIR="@0@"'.format(gt_prefix / gt_localedir),
  ]

  nautilus_deps = [
    gio_dep,
    glib_dep,
    gtk3_dep,
    nautilus_dep,
  ]

  nautilus_symbols_map = meson.current_source_dir() / 'nautilus.map'
  nautilus_ldflags = [
    cc.get_supported_link_arguments('-Wl,--version-script,@0@'.format(nautilus_symbols_map)),
  ]

  nautilus_module = shared_module(
    'terminal-nautilus',
    sources: nautilus_sources,
    include_directories: top_inc,
    dependencies: nautilus_deps,
    c_args: nautilus_cflags,
    link_args: nautilus_ldflags,
    link_depends: nautilus_symbols_map,
    install: true,
    install_dir: nautilus_extensiondir,
  )
endif

# Unit tests

test_regex = executable(
  'test-regex',
  sources: regex_sources,
  include_directories: top_inc,
  c_args: ['-DTERMINAL_REGEX_MAIN',],
  dependencies: [glib_dep, pcre2_dep],
)

test_env = [
  'GNOME_TERMINAL_DEBUG=0'
]

test_units = [
  ['regex', test_regex],
]

foreach test: test_units
  test(
    test[0],
    test[1],
    env: test_env,
  )
endforeach
