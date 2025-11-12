# python-build

Building Python for Android, iOS, macOS, Linux, and Windows

## Overview

This repository contains build scripts and GitHub Actions workflows for building Python 3.14 for multiple platforms:
- **Android**: All ABIs (arm64-v8a, x86_64)
- **Darwin**: iOS and macOS
- **Linux**: x86_64 and aarch64
- **Windows**: amd64

## Build Status

Builds are automated using GitHub Actions. The workflow is triggered on:
- Pushes to branches matching `python-3.*` or `main`
- Pull requests to branches matching `python-3.*` or `main`
- Manual workflow dispatch

## Build Artifacts

Build artifacts are automatically uploaded for each platform and can be downloaded from:
- GitHub Actions workflow runs (for development builds)
- GitHub Releases (for tagged releases)

### Release Process

To create a release:
1. Tag a commit with the version format: `v3.14` (matching `PYTHON_VERSION_SHORT`)
2. Push the tag to GitHub
3. GitHub Actions will automatically build all platforms and create a release with artifacts

## Platform-Specific Details

### Android
- Uses Android NDK r27
- Builds all standard ABIs
- Creates packages for mobile-forge and Flutter/Dart

### Darwin (iOS/macOS)
- Uses BeeWare's Python-Apple-support
- Builds both iOS and macOS frameworks
- Packages for Flutter/Dart integration

### Linux
- Uses python-build-standalone from Astral
- Builds for x86_64 and aarch64 architectures
- Creates stripped install-only packages

### Windows
- Downloads official Python installer
- Creates custom distribution for Flutter/Dart
- Compiles Python bytecode for distribution

## Migration from AppVeyor

This repository previously used AppVeyor for CI/CD. The migration to GitHub Actions provides:
- Better integration with GitHub
- Faster build times
- More flexible workflow configuration
- Native support for all platforms

