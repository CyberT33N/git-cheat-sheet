


https://github.com/unhappychoice/gitlogue?tab=readme-ov-file




-----------------------



https://github.com/gitui-org/gitui


# Install

## Ubuntu

```sh
#!/bin/bash

# Simple gitui installation script for Ubuntu
# Downloads and installs to /usr/local/bin

set -e  # Exit on any error

# Architecture mapping
MACHINE_ARCH=$(uname -m)
case $MACHINE_ARCH in
    x86_64)
        GITHUB_ARCH="x86_64"
        ;;
    aarch64|arm64)
        GITHUB_ARCH="aarch64"
        ;;
    armv7l)
        GITHUB_ARCH="armv7"
        ;;
    armv6l|arm)
        GITHUB_ARCH="arm"
        ;;
    *)
        echo "❌ Unsupported architecture: $MACHINE_ARCH"
        echo "Supported: x86_64, aarch64, armv7l, armv6l, arm"
        exit 1
        ;;
esac

echo "🔍 Architecture: $MACHINE_ARCH -> $GITHUB_ARCH"

# Get download URL
echo "📡 Fetching latest release..."
url=$(curl -sL https://api.github.com/repos/gitui-org/gitui/releases/latest | \
      grep -o "https://github.com/gitui-org/gitui/releases/download/.*/gitui-linux-${GITHUB_ARCH}.tar.gz")

if [[ -z "$url" ]]; then
    echo "❌ Could not find download URL for $GITHUB_ARCH"
    exit 1
fi

echo "📥 Download URL: $url"

# Create temp directory
TEMP_DIR=$(mktemp -d)
cd "$TEMP_DIR"

echo "⬇️  Downloading gitui..."
curl -L "$url" -o gitui.tar.gz

echo "📦 Extracting..."
tar -xzf gitui.tar.gz

# Verify binary exists
if [[ ! -f "gitui" ]]; then
    echo "❌ gitui binary not found after extraction"
    exit 1
fi

# Test binary
echo "🧪 Testing binary..."
./gitui --version

# Check if already installed
if command -v gitui >/dev/null 2>&1; then
    CURRENT_VERSION=$(gitui --version 2>/dev/null || echo "unknown")
    echo "⚠️  gitui is already installed: $CURRENT_VERSION"
    read -p "Replace with new version? (y/N): " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        echo "Installation cancelled"
        rm -rf "$TEMP_DIR"
        exit 0
    fi
fi

# Install to /usr/local/bin
echo "📥 Installing to /usr/local/bin..."
sudo cp gitui /usr/local/bin/
sudo chmod 755 /usr/local/bin/gitui
sudo chown root:root /usr/local/bin/gitui

# Cleanup
cd /
rm -rf "$TEMP_DIR"

# Verify installation
echo "✅ Installation complete!"
echo "📋 Installed version: $(gitui --version)"
echo "📍 Location: $(which gitui)"
echo "💡 Usage: Run 'gitui' in any git repository"
```

Remove
```shell
sudo rm /usr/local/bin/gitui
```
