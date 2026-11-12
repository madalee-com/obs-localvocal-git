# obs-localvocal-git (AUR package)

An AUR package for building and installing [obs-localvocal](https://github.com/royshil/obs-localvocal) on Arch Linux.

obs-localvocal is an OBS Studio plugin that provides **real-time Whisper transcription and translation** — it adds a live-caption source to OBS, supporting multiple languages.

> [!WARNING]
> This package was **built using a local AI agent** and has **not been fully vetted**. Use at your own risk:
>
> - The build patches CMake 4.x compatibility issues in the upstream source.
> - Dependencies and install paths were reverse-engineered from the project's CI configuration.
> - Please test thoroughly before relying on it in production streams.
> - Report any issues by opening an issue on this repo.

## Requirements

This package requires the following dependencies to be installed first:

| Category | Packages |
|----------|----------|
| Runtime  | `obs-studio`, `qt6-base`, `curl`, `icu`, `openblas`, `vulkan-icd-loader`, `ocl-icd` |
| Build    | `cmake>=3.28`, `ninja`, `ccache`, `pkgconf`, `qt6-svg`, `simde`, `patchelf`, `git` |

On a minimal Arch system you can install them with:

```bash
sudo pacman -S obs-studio qt6-base curl icu openblas vulkan-icd-loader ocl-icd cmake ninja ccache pkgconf qt6-svg simde patchelf git
```

## Installation

### Option 1: Direct from this repo (single line)

Clone the repository and build/install in one step:

```bash
git clone https://github.com/madalee-com/obs-localvocal-git.git && cd obs-localvocal-git && makepkg -si
```

`makepkg -si` builds the package (`-s` pulls missing build deps from pacman, `-i` installs it after building).

### Option 2: Via an AUR helper (yay)

If you use [yay](https://github.com/Jguer/yay), you can install directly from this repo:

```bash
yay -S git+https://github.com/madalee-com/obs-localvocal-git.git
```

### Option 3: Manual AUR workflow

```bash
# Clone the package build files
git clone https://github.com/madalee-com/obs-localvocal-git.git
cd obs-localvocal-git

# Build and install (prompts for confirmation before installing deps)
makepkg -si
```

## What this package does

- Clones the latest obs-localvocal source from the upstream Git repo.
- Applies two CMake 4.x compatibility patches during `prepare()` (upstream uses nested parentheses in `if()` statements that CMake 4 rejects).
- Configures with the project's `linux-x86_64` cmake preset, overriding the install prefix to `/usr` for proper Arch integration.
- Installs the plugin `.so` to `/usr/lib/obs-plugins/`, supporting libraries to `/usr/lib/obs-plugins/obs-localvocal/`, and data/models to `/usr/share/obs/obs-plugins/obs-localvocal/`.

## Updating

Since this is a `-git` package, it always builds from the latest `main` branch. To update:

```bash
cd obs-localvocal-git
makepkg -si
```

(Or if using yay: `yay -S obs-localvocal-git` will rebuild automatically when upstream changes.)

## Uninstall

```bash
sudo pacman -R obs-localvocal-git
```
