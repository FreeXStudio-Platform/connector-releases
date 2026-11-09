# FreexStudio Connector releases

Download builds of **FreexStudio Connector** for macOS, and check them before you open them.

FreexStudio Connector keeps a chosen macOS network service preferred while a tethering
interface that macOS can already see is present. It does not add Android USB transport.

This repository holds **builds and documentation only**. The source code is private. Each
release carries the app archive, its SHA-256 checksum, a software bill of materials (SBOM)
and the build record from the private build. A workflow in this repository checks every file
and signs an attestation for it.

- Product page: https://freexstudio.com/downloads#freexstudio-connector
- Latest release: see **Releases** on the right
- Report a bug or ask for a feature: **Issues**
- Ideas and questions: **Discussions**
- Report a security problem privately: see [SECURITY.md](SECURITY.md)

## Current release

| | |
|---|---|
| Version | 2.2.0 (beta) |
| Platform | macOS 13 or later, Intel and Apple silicon (universal) |
| Signature | Ad-hoc. No Apple Developer ID, not notarised |
| Built | GitHub-hosted macOS runner, Xcode 15.2, from a clean tree |

Because the build is not signed with a Developer ID or notarised, macOS shows a warning the
first time you open it. That warning is expected for this beta. Read
[How to verify this build](#how-to-verify-this-build) before you approve it.

## How to verify this build

Download the files from the release into one folder, then run these commands in Terminal from
that folder.

**1. The checksum matches.** Every line must end in `OK`.

```sh
shasum -a 256 -c SHA256SUMS
```

**2. This repository published the file.** Needs the GitHub CLI (`brew install gh`).

```sh
gh attestation verify FreexStudio-Connector-2.2.0-macOS.zip --repo FreeXStudio-Platform/connector-releases
```

The attestation is signed through Sigstore and recorded in its public transparency log. It
proves the file is byte-for-byte the one this repository's release workflow checked and
published. It does **not** prove which source code produced it: the build record
`FreexStudio-Connector-2.2.0-macOS.zip.provenance.json` states the source commit and
toolchain from the private build.

**3. The contents match the SBOM.** The SBOM lists every file in the archive with its SHA-256,
and every system library the app links. You can regenerate it with the script in this
repository and compare:

```sh
python3 tools/sbom.py FreexStudio-Connector-2.2.0-macOS.zip --version 2.2.0 \
  --timestamp "$(python3 -c 'import json;print(json.load(open("FreexStudio-Connector-2.2.0-macOS.sbom.cdx.json"))["metadata"]["timestamp"])')" \
  | diff - FreexStudio-Connector-2.2.0-macOS.sbom.cdx.json && echo "SBOM matches"
```

**4. The signature is what we say it is.**

```sh
unzip -q FreexStudio-Connector-2.2.0-macOS.zip
codesign -dv --verbose=2 "FreexStudio Connector.app" 2>&1 | grep -E 'Signature|Identifier|Format'
```

You should see `Signature=adhoc`. If you see anything else, do not open it and report it.

**5. Its network behaviour matches the list.** [NETWORK.md](NETWORK.md) lists everything the
app sends and every permission it asks for, and how to watch it yourself with an outbound
firewall such as LuLu (free, open source) or Little Snitch.

## What this does and does not prove

Without the source, you can confirm that a download is intact, that it is the file this
repository published, what it contains, which system libraries it uses, and what it does on
the network. You cannot prove from a binary alone that it has no hidden behaviour. If you
need that level of assurance, wait for an independent security review or for a notarised
release, and tell us in Discussions; it helps us decide what to do next.

## Help make it better

- **Bugs:** open an issue with the bug form. Include your macOS version and Mac model.
- **Features:** open an issue with the feature form, or start a Discussion.
- **Security:** never in a public issue. Use the private report described in [SECURITY.md](SECURITY.md).

Pull requests are not accepted here, because this repository has no source code. Issues and
Discussions reach the people who build the app.

## Terms

Use of the app is covered by the notices at https://freexstudio.com/legal.
