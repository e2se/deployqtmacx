## deployqtmacx

Finalize app bundle like *macdeployqt* with path resolver.

:construction: *beta* :construction:

The main difference with *macdeployqt* is the path resolver used to fix relative paths and to pass your Qt path, framework an plugins path.

This prevent errors when deploy dependencies with wrong `@loader_path`. The script fix also `@rpath` on each dependency found, using `@rpath` instead of `@executable_path` that is what used by *macdeployqt*.

> [!TIP]
> The script has a **simulation mode**, to try all the actions without write any files

> [!IMPORTANT]
> This script does not has key features of *macdeployqt* distribuited with Qt, for example deploy of qml, translations, binary strip, and signing. You should provide your own routine to strip binaries, sign, etc.

By default it looks for Qt installed via *Homebrew* in `/usr/homebrew/opt/qt` or `/usr/local/opt/qt`

This script uses macOS built-in tools: `otool`, `install_name_tool`, `codesign`

**It requires `bash` and its use is intendend for macOS only**


## Usage

`bash deployqtmacx.sh AppBundle.app [OPTIONS]`

| Options |  |
| ------- | - |
| --verbose | Verbose |
| -s --simulate | Simulate actions without write files |
| -e --print-excluded | Print excluded files |
| -f --force | Force overwrite of files |
| -np --no-deploy-plugins | Disallow plugins deploy |
| -plugins | Plugins to deploy (platforms styles) |
| -exclude-lib-paths | Library paths to exclude (/usr/lib /System/Library) |
| -qt-path | Set Qt path |
| -framework-path | Set Qt framework path |
| -plugins-path | Set Qt plugins path |
| -h --help | Display this help and exit |


## Example

An example of the script ran with `--verbose` and `--simulate` using your Qt path passed to `-qtpath`
```
chmod +x deployqtmacx.sh

./deployqtmacx.sh built/AppBundle.app --verbose --simulate -qtpath /path/to/qt
```

Produces this output:
```
bundle: /path/to/user/repo/built/AppBundle.app
executable: MacOS/AppBinary
dependency: AppBinary
resolved: /path/to/qt/lib/QtWidgets.framework/Versions/A/QtWidgets  from: @rpath/QtWidgets.framework/Versions/A/QtWidgets
copy: QtWidgets.framework  to: Frameworks/QtWidgets.framework/
  copy directory from "/path/to/qt/lib/QtWidgets.framework/" to "/path/to/user/repo/built/AppBundle.app/Contents/Frameworks/QtWidgets.framework/"
fix: AppBinary  with: @rpath/QtWidgets.framework/Versions/A/QtWidgets
  change rpath from @rpath/QtWidgets.framework/Versions/A/QtWidgets to @rpath/QtWidgets.framework/Versions/A/QtWidgets on "/path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary"
fix: QtWidgets  with: @rpath/QtWidgets.framework/Versions/A/QtWidgets
  change dylib id to @rpath/QtWidgets.framework/Versions/A/QtWidgets on "/path/to/user/repo/built/AppBundle.app/Contents/Frameworks/QtWidgets.framework/Versions/A/QtWidgets"
  change rpath from @rpath/QtWidgets.framework/Versions/A/QtWidgets to @rpath/QtWidgets.framework/Versions/A/QtWidgets on "/path/to/user/repo/built/AppBundle.app/Contents/Frameworks/QtWidgets.framework/Versions/A/QtWidgets"
unsign: QtWidgets
  remove sign on "/path/to/user/repo/built/AppDir/AppBundle.app/Contents/Frameworks/QtWidgets.framework/Versions/A/QtWidgets"
dependency: QtWidgets
resolved: /path/to/qt/lib/QtGui.framework/Versions/A/QtGui  from: @rpath/QtGui.framework/Versions/A/QtGui
copy: QtGui.framework  to: Frameworks/QtGui.framework/
[...]
resolved: /path/to/qt/lib/QtCore.framework/Versions/A/QtCore  from: /path/to/qt/lib/QtCore.framework/Versions/A/QtCore
no overwrite: QtCore.framework  already at: Frameworks/QtCore.framework/
fix: AppBinary  with: @rpath/QtCore.framework/Versions/A/QtCore
  change rpath from /path/to/qt/lib/QtCore.framework/Versions/A/QtCore to @rpath/QtCore.framework/Versions/A/QtCore on "/path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary"
clean: AppBinary
complete: AppBinary
  remove rpath /usr/homebrew/lib on "/path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary"
  remove rpath @executable_path/../Frameworks on "/path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary"
  add rpath @loader_path/ on "/path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary"
  add rpath @executable_path/../MacOS on "/path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary"
  add rpath @executable_path/../Frameworks on "/path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary"
plugin: platforms
copy: platforms  to: PlugIns/platforms
  copy directory from "/path/to/qt/share/qt/plugins/platforms" to "/path/to/user/repo/built/AppBundle.app/Contents/PlugIns/platforms"
dependency: libqcocoa.dylib
resolved: /path/to/qt/lib/QtGui.framework/Versions/A/QtGui  from: @rpath/QtGui.framework/Versions/A/QtGui
no overwrite: QtGui.framework  already at: Frameworks/QtGui.framework/
fix: libqcocoa.dylib  with: @rpath/QtGui.framework/Versions/A/QtGui
  change rpath from @rpath/QtGui.framework/Versions/A/QtGui to @rpath/QtGui.framework/Versions/A/QtGui on "/path/to/user/repo/built/AppBundle.app/Contents/PlugIns/platforms/libqcocoa.dylib"
resolved: /path/to/qt/QtCore.framework/Versions/A/QtCore  from: @rpath/QtCore.framework/Versions/A/QtCore
no overwrite: QtCore.framework  already at: Frameworks/QtCore.framework/
[...]
```

Finally, you should provide your own routine to strip binaries, sign, etc., as for example:
```
strip /path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary
codesign --preserve-metadata=identifier,entitlements --force -s YourAuthority /path/to/user/repo/built/AppBundle.app/Contents/MacOS/AppBinary
```

&nbsp;

### License

Source code licensed under the terms of the [MIT License](https://github.com/e2se/deployqtmacx/blob/main/LICENSE-MIT).

It is also licensed under the terms of the [GNU GPLv3](https://github.com/e2se/deployqtmacx/blob/main/LICENSE-GPL-3.0-or-later).

