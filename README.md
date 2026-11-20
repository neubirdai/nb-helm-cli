# nbhelmctl

`nbhelmctl` prepares, checks, installs and upgrades NeuBird in your Kubernetes
cluster, from the install profile NeuBird prepares for you. This repository
holds its releases; you need an install code from NeuBird to use it.

## Installation

### macOS / Linux (Homebrew)

```bash
brew install neubirdai/tap/nbhelmctl
```

Release files include the version in their names (for example
`nbhelmctl_0.1.0_linux_amd64.deb`), so the commands below look up the latest
version first.

### Linux (Debian / Ubuntu)

```bash
VERSION=$(curl -fsSL https://api.github.com/repos/neubirdai/nb-helm-cli/releases/latest \
  | sed -n 's/.*"tag_name": *"v*\([^"]*\)".*/\1/p')
: "${VERSION:?could not resolve the latest release; check https://github.com/neubirdai/nb-helm-cli/releases}"
curl -fLO "https://github.com/neubirdai/nb-helm-cli/releases/download/v${VERSION}/nbhelmctl_${VERSION}_linux_amd64.deb"
sudo dpkg -i "nbhelmctl_${VERSION}_linux_amd64.deb"
```

Replace `amd64` with `arm64` on 64-bit ARM.

### Linux (Fedora / RHEL)

```bash
VERSION=$(curl -fsSL https://api.github.com/repos/neubirdai/nb-helm-cli/releases/latest \
  | sed -n 's/.*"tag_name": *"v*\([^"]*\)".*/\1/p')
: "${VERSION:?could not resolve the latest release; check https://github.com/neubirdai/nb-helm-cli/releases}"
curl -fLO "https://github.com/neubirdai/nb-helm-cli/releases/download/v${VERSION}/nbhelmctl_${VERSION}_linux_amd64.rpm"
sudo rpm -i "nbhelmctl_${VERSION}_linux_amd64.rpm"
```

Replace `amd64` with `arm64` on 64-bit ARM.

### Any Linux or macOS (no package manager)

```bash
VERSION=$(curl -fsSL https://api.github.com/repos/neubirdai/nb-helm-cli/releases/latest \
  | sed -n 's/.*"tag_name": *"v*\([^"]*\)".*/\1/p')
OS=$(uname -s | tr '[:upper:]' '[:lower:]')                       # linux or darwin
ARCH=$(uname -m | sed 's/x86_64/amd64/; s/aarch64/arm64/')        # amd64 or arm64
curl -fL "https://github.com/neubirdai/nb-helm-cli/releases/download/v${VERSION}/nbhelmctl_${VERSION}_${OS}_${ARCH}.tar.gz" | tar -xz nbhelmctl
sudo mv nbhelmctl /usr/local/bin/
```

### Windows (amd64)

In PowerShell. The same commands install for the first time and upgrade an
existing install:

```powershell
$tag = (Invoke-RestMethod "https://api.github.com/repos/neubirdai/nb-helm-cli/releases/latest").tag_name
$version = $tag.TrimStart("v")
Invoke-WebRequest -Uri "https://github.com/neubirdai/nb-helm-cli/releases/download/$tag/nbhelmctl_${version}_windows_amd64.zip" -OutFile nbhelmctl.zip
Expand-Archive nbhelmctl.zip -DestinationPath "$env:LOCALAPPDATA\nbhelmctl" -Force

# Add to PATH (current session)
$env:Path += ";$env:LOCALAPPDATA\nbhelmctl"

# Add to PATH (permanent, for new terminals; only needed on first install)
[Environment]::SetEnvironmentVariable("Path", "$([Environment]::GetEnvironmentVariable('Path','User'));$env:LOCALAPPDATA\nbhelmctl", "User")
```

Or download the zip from the [latest release](https://github.com/neubirdai/nb-helm-cli/releases/latest)
by hand.

### Verify the download

Each release has a `checksums.txt` with the SHA-256 of every file:

```bash
sha256sum --ignore-missing -c checksums.txt     # Linux; on macOS: shasum -a 256 -c
```

### Verify installation

```bash
nbhelmctl --version
```

## Getting started

`nbhelmctl` uses `kubectl` and `helm` (3.14 or later) from your `PATH`, and the
cluster your kube context points at (or `--context`).

```bash
# 1. Redeem the one-time install code NeuBird gave you, into NeuBird's namespace
nbhelmctl init --code nbic_... --namespace neubird

# 2. Check the cluster is ready (read-only; changes nothing)
nbhelmctl check

# 3. Install
nbhelmctl install

# Later
nbhelmctl status
nbhelmctl upgrade
```

Every command has `--help`. For an air-gapped cluster, `nbhelmctl mirror`
copies the images into your registry first; ask NeuBird for the details of
your install.

## Support

Contact your NeuBird representative, or support@neubird.ai.
