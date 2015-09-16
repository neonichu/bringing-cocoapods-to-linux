# Bringing CocoaPods to Linux

## NSSpain, September 2015

### Boris Bügling - @NeoNacho

![20%, original, inline](images/contentful.png)

![](images/penguin-group.gif)

<!--- use Sketchnote theme, white -->

---

# CocoaPods

![](images/cocoapods.jpg)

dependency management ✅

---

# Contentful

![](images/contentful-bg.png)

content management ✅

---

# Agenda

- Why?
- Xcode's build system
- How does CocoaPods interact with it?
- Alternatives
- Plan

![](images/background.jpg)

---

![50%](images/y-u-no.png)

---

![](images/oss.jpg)

---

![](images/oss-linux.jpg)

---

# No Xcode on Linux

![](images/background.jpg)

---

# Xcode is broken

![](images/penguin-hole.gif)

---

# ya tu sabes

![](images/background.jpg)

---

# Just two examples

- Archiving watchOS 2.0 apps
- CLANG\_ENABLE\_MODULES

![](images/background.jpg)

---

### [Xcode 7 beta] Linking dual (iphoneos and watchos) frameworks with same product name causes archive to fail

<http://openradar.appspot.com/22392501>

![](images/background.jpg)

---

![99%, inline](images/yolo-xcode.png)

![](images/background.jpg)

---

## CLANG\_ENABLE\_MODULES

![](images/background.jpg)

---

> Enables the use of modules for system APIs.  System headers are imported as semantic modules instead of raw headers.

![](images/background.jpg)

---

# "system APIs" == any framework outside your target 😂

![](images/background.jpg)

---

![99%, inline](images/clang-modules.png)

![](images/background.jpg)

---

# Bring back CocoaPods to its core and move rest (e.g. Xcode integration) into plugins. #2729

From October 2014

<https://github.com/CocoaPods/CocoaPods/issues/2729>

![](images/background.jpg)

---

> Let's face it, programming sucks.

![](images/background.jpg)

---

# Xcode's build system

![](images/Xcode.png)

---

- Fairly old (`Xcode3Core.ideplugin`)
- Entirely based on mtimes
- Poorly documented

![](images/Xcode.png)

---

# De-facto documentation

- <http://pewpewthespells.com/blog/managing_xcode.html>
- <http://pewpewthespells.com/blog/buildsettings.html>

![](images/Xcode.png)

---

# Xcode project files 🙈🙊🙉

![](images/Xcode.png)

---

# Tomorrow 16:30 - 17:00

## Grant Paul - Making Sense of Xcode

![](images/Xcode.png)

---

# How does CocoaPods interact with it?

![](images/cocoapods.jpg)

---

# Alternatives

![](images/penguin-work.gif)

---

# Makefiles

![](images/gnu.jpg)

---

```makefile
package: $(EXECUTABLE_NAME) $(INFO_PLIST)
	mkdir -p "$(PRODUCT_NAME).app"
	@/bin/echo -n 'AAPL' > "$(PRODUCT_NAME).app/PkgInfo"
	@$(PLBUDDY) -c 'Print CFBundleSignature' $(INFO_PLIST) \
		>> "$(PRODUCT_NAME).app/PkgInfo"
	cp $(EXECUTABLE_NAME) "$(PRODUCT_NAME).app"
	$(BASE_DIR)/sh/build_plist $(INFO_PLIST) "$(PRODUCT_NAME).app/Info.plist"
	$(BASE_DIR)/sh/build_ipa "$(PRODUCT_NAME).app"

$(EXECUTABLE_NAME): $(OBJS)
	$(LD) $(CFLAGS) $(LDFLAGS) -o $@ $^
```

![](images/gnu.jpg)

---

# Makefiles

- Build rules to transform files (e.g. `.m` => `.o`)
- Based on mtimes
- "Phony" rules which run unconditionally
- Dependencies to files or other rules
- Exists everywhere

![](images/gnu.jpg)

---

# Rules

```makefile
target: dependencies
	build step(s)
```

![](images/gnu.jpg)

---

# Execution

```bash
make [options] [target1 target2 ...]
```

![](images/gnu.jpg)

<!-- more transparency, but same flawed build process -->

---

# Buck

![](images/facebook.png)

<!-- https://buckbuild.com/ -->

---

![100%](images/buck/Talk.001.jpg)

---

![100%](images/buck/Talk.002.jpg)

---

![100%](images/buck/Talk.003.jpg)

---

![100%](images/buck/Talk.004.jpg)

---

![100%](images/buck/Talk.006.jpg)

---

=> no more DerivedData nonsense 🎉

![](images/background.jpg)

---

# Installation

```bash
$ brew update
$ brew tap facebook/fb
$ brew install --HEAD buck
```

![](images/background.jpg)

---

# Quickstart

```bash
$ buck quickstart --type ios --dest-dir .
[...]
$ buck build demo_app_ios
[...]
$ ls buck-out/gen/ios/BuckDemoApp
BuckDemoApp.app  BuckDemoApp.dSYM
```

![](images/background.jpg)

---

# Initial build

```bash
$ buck build demo_app_ios
[...]
[-] BUILDING...FINISHED 2.4s (8/8 JOBS, 8 UPDATED, 0.0% CACHE HITS)
```

# Subsequent builds

```bash
$ buck build demo_app_ios
[...]
[-] BUILDING...FINISHED 0.0s (1/8 JOBS, 0 UPDATED)
```

![](images/background.jpg)

---

# BUCK

```python
apple_binary(
  [...]
)

xcode_project_config(
  name = 'Hello',
  src_target = ':Hello',
  action_config_names={'profile': 'Profile'}
)
```

![](images/background.jpg)

---

# Source files

```python
srcs = [
    'Code/AppDelegate.m'
    'Code/ViewController.m',
    'Code/main.m',
],
```

![](images/background.jpg)

---

# Headers

```python
headers = [
    'Code/AppDelegate.h'
    'Code/ViewController.h',
],
```

![](images/background.jpg)

---

# Linked frameworks

```python
frameworks = [
    '$SDKROOT/System/Library/Frameworks/Foundation.framework',
    '$SDKROOT/System/Library/Frameworks/UIKit.framework',
],
```

![](images/background.jpg)

---

# Dependencies

```python
deps = [
    '//Libraries/EXExample:EXExample',
],
```

![](images/background.jpg)

---

# Plan

![](images/ateam.jpg)




---

- Xcode's build system
- How does CocoaPods interact with it?
- Breaking the strong coupling with a new abstraction
- Discussion of replacement build systems:
	- GNU Makefiles
	- Buck
- Notes on the concrete implementation inside CocoaPods

---

# Thanks!

![](images/penguins-walking.gif)

---

https://www.facebook.com/events/1495505997428211/

# _

@NeoNacho

boris@contentful.com

http://buegling.com/talks

![](images/la-tavina.gif)
