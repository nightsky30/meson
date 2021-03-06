project('fs module test')

is_windows = build_machine.system() == 'windows'

fs = import('fs')

assert(fs.exists('meson.build'), 'Existing file reported as missing.')
assert(not fs.exists('nonexisting'), 'Nonexisting file was found.')

# When one creates a source release with sdist, Python
# does not store symlinks in the archive as native symlinks.
# Thus the extracted archive does not contain them either.
# Sadly this means that we can only execute the symlink test when
# running from a git checkout because otherwise we'd need to
# do postprocessing on the generated archive before actual release.
# That is both nonstandard an error prone and having symlinks in
# the archive would probably break on Windows anyway.
is_git_checkout = fs.exists('../../../.git')

if not is_windows and build_machine.system() != 'cygwin' and is_git_checkout
  assert(fs.is_symlink('a_symlink'), 'Symlink not detected.')
  assert(not fs.is_symlink('meson.build'), 'Regular file detected as symlink.')
endif

assert(fs.is_file('meson.build'), 'File not detected as a file.')
assert(not fs.is_file('subprojects'), 'Directory detected as a file.')
assert(not fs.is_file('nonexisting'), 'Bad path detected as a file.')

assert(fs.is_dir('subprojects'), 'Dir not detected correctly.')
assert(not fs.is_dir('meson.build'), 'File detected as a dir.')
assert(not fs.is_dir('nonexisting'), 'Bad path detected as a dir.')

assert(fs.is_dir('~'), 'expanduser not working')
assert(not fs.is_file('~'), 'expanduser not working')

original = 'foo.txt'
new = fs.replace_suffix(original, '.ini')
assert(new == 'foo.ini', 'replace_suffix failed')

original = 'foo'
new = fs.replace_suffix(original, '.ini')
assert(new == 'foo.ini', 'replace_suffix did not add suffix to suffixless file')

original = 'foo.dll.a'
new = fs.replace_suffix(original, '.so')
assert(new == 'foo.dll.so', 'replace_suffix did not only modify last suffix')

original = 'foo.dll'
new = fs.replace_suffix(original, '')
assert(new == 'foo',  'replace_suffix did not only delete last suffix')

# `/` on windows is interpreted like `.drive` which in general may not be `c:/`
# the files need not exist for fs.replace_suffix()
original = is_windows ? 'j:/foo/bar.txt' : '/foo/bar.txt'
new_check = is_windows ? 'j:\\foo\\bar.ini' : '/foo/bar.ini'

new = fs.replace_suffix(original, '.ini')
assert(new == new_check, 'absolute path replace_suffix failed')

# -- hash

md5 = fs.hash('subdir/subdirfile.txt', 'md5')
sha256 = fs.hash('subdir/subdirfile.txt', 'sha256')
assert(md5 == 'd0795db41614d25affdd548314b30b3b', 'md5sum did not match')
assert(sha256 == 'be2170b0dae535b73f6775694fffa3fd726a43b5fabea11b7342f0605917a42a', 'sha256sum did not match')

# -- size

size = fs.size('subdir/subdirfile.txt')
assert(size == 19, 'file size not found correctly')

# -- are filenames referring to the same file?
f1 = 'meson.build'
f2 = 'subdir/../meson.build'
assert(fs.is_samepath(f1, f2), 'is_samepath not detercting same files')
assert(fs.is_samepath(meson.source_root(), 'subdir/..'), 'is_samepath not detecting same directory')
assert(not fs.is_samepath(f1, 'subdir/subdirfile.txt'), 'is_samepath known bad comparison')
assert(not fs.is_samepath('not-a-path', f2), 'is_samepath should not error if path(s) do not exist')

if not is_windows and build_machine.system() != 'cygwin'
  assert(fs.is_samepath('a_symlink', 'meson.build'), 'symlink is_samepath fail')
endif

assert(fs.parent('foo/bar') == 'foo', 'failed to get dirname')
assert(fs.name('foo/bar') == 'bar', 'failed to get basename')

subdir('subdir')
