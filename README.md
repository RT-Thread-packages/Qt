# Qt for RT-Thread kernel mode

This repository is the RT-Thread kernel-mode package wrapper for Qt. The
actual port, patch series, examples and integration implementation are
maintained in
[GuEe-GUI/Qt-for-RT-Thread](https://github.com/GuEe-GUI/Qt-for-RT-Thread)
and referenced here as the `port` Git submodule.

RT-Smart userspace support is intentionally not exposed by this package. It
will use the same upstream port repository through a separate RT-Smart
userapps package.

## Layout

```text
Qt/
├── Kconfig       kernel package configuration proxy
├── SConscript    kernel SCons integration proxy
└── port/         pinned Qt-for-RT-Thread upstream submodule
```

The package entry delegates to:

```text
port/integrations/kernel/Kconfig
port/integrations/kernel/SConscript
```

Clone this repository with its submodule:

```bash
git clone --recursive https://github.com/GuEe-GUI/Qt.git
```

For an existing checkout, initialize or synchronize it explicitly:

```bash
git submodule update --init --recursive
```

The RT-Thread package delivery path must preserve this submodule. Before an
online-package release, verify that its package manager checkout initializes
submodules; otherwise the `port/` source needs to be vendored or fetched by a
supported package-source mechanism.

## Kernel-mode use

The selected BSP supplies an application entry compatible with the package:

```cpp
extern "C" void qt_main(void *parameter);
```

Enable the package in `menuconfig`, set `PKG_QT_RTTHREAD_SDK_PATH` to the
assembled kernel SDK, then start the application from msh with `qt_run`.

The package integration is architecture-neutral once the SDK is assembled.
The current kernel SDK specifications cover AArch64 and RV64
(`qemu-virt64-riscv`, `riscv64-unknown-linux-musl-`, `lp64`).  The selected
SDK and BSP must use the same toolchain ABI; do not use an AArch64 SDK on RV64
or mix the RV64 toolchain's `lp64` and `lp64d` variants.

`PKG_QT_RTTHREAD_FONTDIR` defaults to `/fonts/`. QWidget and QML text require
TTF, TTC, or OTF files available in that target directory before `qt_run`;
QtCore-only applications do not require fonts.

## Updating the port revision

Make and test port changes in `Qt-for-RT-Thread` first. Commit the resulting
patches, SDK assembly updates, and examples there; then update only this
repository's pinned `port` revision. Keeping that order makes each package
revision reproducible.
