project('gssproxy', 'c',
	version: '0.9.2',
	license_files: 'COPYING'
	default_options : [
		'buildtype=debugoptimized',
		'warning_level=1',
		'c_std=gnu99'
	]
)

# Primary version number
VERSION_NUMBER = meson.project_version()

# If the PRERELEASE_VERSION_NUMBER is set, we'll append
# it to the release tag when creating an RPM or SRPM
PRERELEASE_VERSION_NUMBER = ''
# PRERELEASE_VERSION = PRERELEASE_VESION_NUMBER
conf.set('PRERELEASE_VERSION', PRERELEASE_VERSION_NUMBER)

add_project_arguments('-D_GNU_SOURCE', language: 'c')
add_project_arguments('-D_FILE_OFFSET_BITS=64', language: 'c')
add_project_arguments('-D_LARGEFILE_SOURCE', language: 'c')
add_project_arguments('-D_LARGEFILE64_SOURCE', language: 'c')

i18n = import('i18n')
#gettext_package = 'foo'
#add_project_arguments('-DGETTEXT_PACKAGE=' + gettext_package, language: 'c')
subdir('po')


