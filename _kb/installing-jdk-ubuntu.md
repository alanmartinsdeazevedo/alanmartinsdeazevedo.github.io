---
layout: article
title: Installing JDK on Ubuntu
description: Guide to installing Java Development Kit on Ubuntu
category: Development Environment
date: 2025-10-28
---

## Overview

The Java Development Kit (JDK) is essential for Java development. This guide covers installing different JDK versions on Ubuntu.

## Choosing a JDK

### Available Options

- **OpenJDK**: Open-source implementation (recommended)
- **Oracle JDK**: Official Oracle version (requires license for production)
- **Amazon Corretto**: AWS distribution of OpenJDK
- **Azul Zulu**: Commercial distribution with free builds

## Installing OpenJDK

### Check Available Versions

```bash
apt search openjdk
```

### Install Default JDK

```bash
sudo apt update
sudo apt install -y default-jdk
```

### Install Specific Versions

**JDK 21 (Latest LTS):**
```bash
sudo apt install -y openjdk-21-jdk
```

**JDK 17 (LTS):**
```bash
sudo apt install -y openjdk-17-jdk
```

**JDK 11 (LTS):**
```bash
sudo apt install -y openjdk-11-jdk
```

**JDK 8 (Legacy LTS):**
```bash
sudo apt install -y openjdk-8-jdk
```

### Verify Installation

```bash
java -version
javac -version
```

## Managing Multiple Java Versions

### List Installed Versions

```bash
update-java-alternatives --list
```

### Switch Java Version

```bash
sudo update-alternatives --config java
```

Select the desired version from the list.

### Switch Java Compiler Version

```bash
sudo update-alternatives --config javac
```

## Setting JAVA_HOME

### Find Java Installation Path

```bash
update-alternatives --display java
```

Look for the path, typically: `/usr/lib/jvm/java-17-openjdk-amd64`

### Set JAVA_HOME Permanently

Edit your bash profile:

```bash
nano ~/.bashrc
```

Add these lines:

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
```

Apply changes:

```bash
source ~/.bashrc
```

### Verify JAVA_HOME

```bash
echo $JAVA_HOME
```

## Installing Oracle JDK

### Manual Installation

1. Download JDK from [Oracle website](https://www.oracle.com/java/technologies/downloads/)

2. Extract the archive:
```bash
tar -xzf jdk-21_linux-x64_bin.tar.gz
```

3. Move to installation directory:
```bash
sudo mv jdk-21 /usr/lib/jvm/
```

4. Configure alternatives:
```bash
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk-21/bin/java 1
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk-21/bin/javac 1
```

## Installing Amazon Corretto

### Add Repository

```bash
wget -O- https://apt.corretto.aws/corretto.key | sudo apt-key add -
sudo add-apt-repository 'deb https://apt.corretto.aws stable main'
```

### Install Corretto

```bash
sudo apt update
sudo apt install -y java-21-amazon-corretto-jdk
```

## Installing via SDKMAN

SDKMAN is a version manager for Java and other JVM languages.

### Install SDKMAN

```bash
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

### Install Java with SDKMAN

```bash
# List available versions
sdk list java

# Install specific version
sdk install java 21.0.1-open

# Use specific version
sdk use java 21.0.1-open

# Set default version
sdk default java 21.0.1-open
```

## Verifying Installation

### Check Java Version

```bash
java -version
```

Expected output:
```
openjdk version "21.0.1" 2023-10-17
OpenJDK Runtime Environment (build 21.0.1+12-Ubuntu-2)
OpenJDK 64-Bit Server VM (build 21.0.1+12-Ubuntu-2, mixed mode, sharing)
```

### Check Compiler Version

```bash
javac -version
```

### Test Java Installation

Create a test file:

```bash
nano HelloWorld.java
```

Add this code:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

Compile and run:

```bash
javac HelloWorld.java
java HelloWorld
```

## IDE Setup

### Installing IntelliJ IDEA

```bash
# Using snap
sudo snap install intellij-idea-community --classic

# Or download from JetBrains website
```

### Installing Eclipse

```bash
sudo snap install eclipse --classic
```

### Installing VS Code with Java

```bash
sudo snap install --classic code

# Install Java Extension Pack in VS Code
code --install-extension vscjava.vscode-java-pack
```

## Common Tools

### Maven

```bash
sudo apt install -y maven
mvn --version
```

### Gradle

```bash
sudo apt install -y gradle
gradle --version
```

Or use Gradle Wrapper in projects:
```bash
./gradlew build
```

## Environment Setup for Development

### Complete Java Environment

Edit `~/.bashrc`:

```bash
# Java
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin

# Maven
export M2_HOME=/usr/share/maven
export PATH=$PATH:$M2_HOME/bin

# Gradle
export GRADLE_HOME=/opt/gradle/gradle-8.5
export PATH=$PATH:$GRADLE_HOME/bin
```

## Troubleshooting

### Java Command Not Found

```bash
# Check if Java is installed
dpkg -l | grep -i jdk

# Reconfigure alternatives
sudo update-alternatives --config java
```

### Wrong Java Version

```bash
# Check current version
java -version

# Switch version
sudo update-alternatives --config java
```

### JAVA_HOME Not Set

```bash
# Find Java installation
dirname $(dirname $(readlink -f $(which java)))

# Set JAVA_HOME in ~/.bashrc
export JAVA_HOME=/path/to/java
```

### Compiler Errors

Ensure `javac` points to correct version:

```bash
sudo update-alternatives --config javac
```

## Uninstalling Java

### Remove OpenJDK

```bash
sudo apt remove openjdk-17-jdk
sudo apt autoremove
```

### Remove All Java Packages

```bash
sudo apt purge openjdk-*
sudo apt autoremove
```

### Clean Up Manually Installed Versions

```bash
sudo rm -rf /usr/lib/jvm/jdk-*
sudo update-alternatives --remove java /usr/lib/jvm/jdk-*/bin/java
```

## Version Comparison

| Version | Release | Support Until | Use Case |
|---------|---------|---------------|----------|
| Java 8  | 2014    | 2030 (LTS)    | Legacy applications |
| Java 11 | 2018    | 2026 (LTS)    | Stable production |
| Java 17 | 2021    | 2029 (LTS)    | Current LTS |
| Java 21 | 2023    | 2031 (LTS)    | Latest LTS |

## Best Practices

1. **Use LTS versions** for production (8, 11, 17, 21)
2. **Set JAVA_HOME** properly in system-wide config
3. **Use version managers** (SDKMAN) for multiple versions
4. **Keep updated** with security patches
5. **Test compatibility** before upgrading

## Resources

- [OpenJDK Website](https://openjdk.org/)
- [Oracle Java Documentation](https://docs.oracle.com/en/java/)
- [SDKMAN Documentation](https://sdkman.io/)
- [Java Version History](https://en.wikipedia.org/wiki/Java_version_history)

## Next Steps

- Set up your preferred IDE (IntelliJ IDEA, Eclipse, VS Code)
- Install build tools (Maven or Gradle)
- Learn about Java modules (JPMS)
- Explore Spring Boot for web applications
