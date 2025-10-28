---
layout: article
title: Flutter Setup on Ubuntu
description: Complete guide to installing and configuring Flutter on Ubuntu
category: Development Environment
date: 2025-10-28
---

## Overview

Flutter is Google's UI toolkit for building natively compiled applications for mobile, web, and desktop from a single codebase. This guide covers Flutter installation on Ubuntu.

## Prerequisites

- Ubuntu 20.04 or later
- 4GB RAM minimum (8GB recommended)
- 10GB free disk space
- Admin/sudo privileges

## System Requirements

### Install Required Dependencies

```bash
sudo apt update
sudo apt install -y curl git unzip xz-utils zip libglu1-mesa
```

## Installing Flutter

### 1. Download Flutter SDK

Download the latest stable Flutter SDK:

```bash
cd ~
wget https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_3.24.0-stable.tar.xz
```

Check [Flutter releases](https://docs.flutter.dev/release/archive) for the latest version.

### 2. Extract the Archive

```bash
tar xf flutter_linux_3.24.0-stable.tar.xz
```

### 3. Add Flutter to PATH

Edit your shell configuration file:

```bash
nano ~/.bashrc
```

Add this line at the end:

```bash
export PATH="$PATH:$HOME/flutter/bin"
```

Apply changes:

```bash
source ~/.bashrc
```

### 4. Verify Installation

```bash
flutter --version
```

## Run Flutter Doctor

Flutter Doctor checks your environment and displays a report:

```bash
flutter doctor
```

This command checks for:
- Flutter SDK
- Android toolchain
- Connected devices
- IDE plugins

## Installing Android Studio

### 1. Download Android Studio

```bash
cd ~/Downloads
wget https://redirector.gvt1.com/edgedl/android/studio/ide-zips/2023.1.1.28/android-studio-2023.1.1.28-linux.tar.gz
```

### 2. Extract and Install

```bash
sudo tar -xzf android-studio-*.tar.gz -C /opt/
```

### 3. Launch Android Studio

```bash
/opt/android-studio/bin/studio.sh
```

### 4. Complete Setup

- Install Android SDK
- Install Android SDK Command-line Tools
- Install Android SDK Build-Tools
- Accept all licenses

### 5. Accept Android Licenses

```bash
flutter doctor --android-licenses
```

Press 'y' to accept all licenses.

## Configure Android Emulator

### Create Virtual Device

1. Open Android Studio
2. Go to Tools > Device Manager
3. Click "Create Device"
4. Select a device (e.g., Pixel 5)
5. Download a system image (e.g., Android 13)
6. Finish setup

### Launch Emulator

```bash
# List available emulators
flutter emulators

# Launch specific emulator
flutter emulators --launch <emulator_id>
```

## Setting Up VS Code (Optional)

### Install VS Code

```bash
sudo snap install --classic code
```

### Install Flutter Extensions

1. Open VS Code
2. Go to Extensions (Ctrl+Shift+X)
3. Install "Flutter" extension (also installs Dart)

## Creating Your First Flutter App

```bash
# Create new project
flutter create my_first_app

# Navigate to project
cd my_first_app

# Run app
flutter run
```

## Useful Flutter Commands

```bash
# Check Flutter version
flutter --version

# Update Flutter
flutter upgrade

# See all connected devices
flutter devices

# Run app on specific device
flutter run -d <device_id>

# Build APK
flutter build apk

# Build for release
flutter build apk --release

# Clean build cache
flutter clean

# Get project dependencies
flutter pub get

# Run tests
flutter test
```

## Configuring Environment Variables

Add to `~/.bashrc`:

```bash
# Android SDK
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
export PATH=$PATH:$ANDROID_HOME/platform-tools

# Flutter
export PATH="$PATH:$HOME/flutter/bin"

# Chrome for Flutter Web
export CHROME_EXECUTABLE=/usr/bin/google-chrome
```

## Enable Flutter Web Support

```bash
flutter config --enable-web
flutter create .
flutter run -d chrome
```

## Enable Linux Desktop Support

```bash
flutter config --enable-linux-desktop

# Install additional dependencies
sudo apt install -y clang cmake ninja-build pkg-config libgtk-3-dev
```

## Troubleshooting

### Flutter Doctor Issues

**Issue**: Android toolchain not found
```bash
# Ensure Android SDK is installed and ANDROID_HOME is set
export ANDROID_HOME=$HOME/Android/Sdk
flutter doctor --android-licenses
```

**Issue**: Connected device not found
```bash
# Enable USB debugging on Android device
# Check device connection
adb devices
```

### Performance Issues

**Hot reload not working**: Run `flutter clean` and restart

**Slow emulator**:
- Increase RAM allocation in AVD Manager
- Enable hardware acceleration (KVM)

### Enable KVM for Better Performance

```bash
# Check KVM support
egrep -c '(vmx|svm)' /proc/cpuinfo

# Install KVM
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils

# Add user to kvm group
sudo usermod -aG kvm $USER
sudo usermod -aG libvirt $USER

# Reboot
sudo reboot
```

## Project Structure

```
my_app/
├── android/          # Android-specific code
├── ios/              # iOS-specific code
├── lib/              # Dart code
│   └── main.dart     # Entry point
├── test/             # Test files
├── pubspec.yaml      # Dependencies
└── README.md
```

## Best Practices

### Dependencies Management

Edit `pubspec.yaml`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.1.0
  provider: ^6.0.0

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^3.0.0
```

Run after editing:
```bash
flutter pub get
```

### Code Organization

```
lib/
├── main.dart
├── models/
├── screens/
├── widgets/
├── services/
└── utils/
```

## Resources

- [Official Flutter Documentation](https://docs.flutter.dev/)
- [Flutter Cookbook](https://docs.flutter.dev/cookbook)
- [Dart Language Tour](https://dart.dev/guides/language/language-tour)
- [Flutter Gallery](https://gallery.flutter.dev/)
- [Pub.dev - Flutter Packages](https://pub.dev/)

## Next Steps

- Learn Dart programming language
- Build sample apps from Flutter Gallery
- Explore state management (Provider, Riverpod, Bloc)
- Study Material Design and Cupertino widgets
- Join [Flutter Community](https://flutter.dev/community)
