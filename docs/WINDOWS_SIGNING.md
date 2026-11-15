# Windows signing — v9.8.78

The test build provided here is **not Authenticode-signed**. The build environment
has neither a Windows code-signing certificate nor an authenticated Microsoft
Artifact Signing account/profile. Its existing Ed25519 key authenticates updates
only. A self-signed certificate would not supply public Windows publisher trust.

## Ready-to-run signing pipeline

`Sign-WindowsRelease.ps1` in the private source creates a separate signed release.
It verifies the input release, runs Windows regression tests, checks Microsoft
signatures on signing tools, applies SHA-256 Authenticode and an RFC 3161 timestamp,
verifies the Windows signature and expected publisher, regenerates the Ed25519
update manifest **after signing**, tests package authentication/tamper rejection,
and produces the final EXE, ZIP and WINDOWS_SIGNATURE.json. A failure stops output.
It never modifies the supplied source EXE or an existing output directory.

Use 64-bit Windows, the supported Go version in go.mod, and the Windows SDK
SignTool. Credentials remain in your signing provider or Windows certificate
store. Keep the separate AmpSpread release private key outside the source/output
folders. Do not put private keys, account secrets or credentials into metadata.json.

## Microsoft Artifact Signing

For an individual publisher in Canada, Microsoft's Public Trust service requires
an Azure subscription, an Individual billing account, identity validation and a
Public Trust certificate profile with signing permission. The legal billing
name/address must match the identity supplied for verification. These account and
identity steps must be completed by the publisher; this build has not created a
paid account or submitted identity information.

1. Follow [Microsoft's setup guide](https://learn.microsoft.com/en-us/azure/artifact-signing/quickstart).
2. Install the official [Artifact Signing Client Tools](https://learn.microsoft.com/en-us/azure/artifact-signing/how-to-signing-integrations)
   and authenticate using an authorized supported credential, such as Azure CLI.
3. Copy metadata.example.json outside the source/output folders and replace the
   regional endpoint, account name and profile name with the actual Azure values.
4. From private source, use the exact verified publisher name:

```powershell
.\Sign-WindowsRelease.ps1 `
  -ExpectedPublisher 'YOUR VERIFIED LEGAL PUBLISHER NAME' `
  -ReleaseSigningKeyPath 'C:\Private\AmpSpread-release-signing-key.pem' `
  -OutputDirectory 'C:\AmpSpreadSigned\v9.8.78' `
  -ArtifactMetadataPath 'C:\Private\ampspread-signing-metadata.json' `
  -DlibPath 'C:\ACTUAL-TOOLS-PATH\x64\Azure.CodeSigning.Dlib.dll'
```

The script can locate x64 SignTool in a normal Windows SDK installation, or accepts
`-SignToolPath`. The Dlib path must be from the official installed client tools.
The default timestamp server is Microsoft's documented endpoint.

## Existing public code-signing certificate

A certificate/provider already installed in CurrentUser\My can instead be selected
with `-CertificateThumbprint`. The certificate needs its private-key provider,
code-signing EKU, current validity and a trusted chain. Hardware tokens/HSMs may
require their own PIN or user approval. Add `-LocalMachineCertificate` only if the
certificate is in LocalMachine\My; no store import is performed.

```powershell
.\Sign-WindowsRelease.ps1 `
  -ExpectedPublisher 'YOUR VERIFIED LEGAL PUBLISHER NAME' `
  -ReleaseSigningKeyPath 'C:\Private\AmpSpread-release-signing-key.pem' `
  -OutputDirectory 'C:\AmpSpreadSigned\v9.8.78' `
  -CertificateThumbprint 'YOUR_40_HEX_CHARACTER_CERTIFICATE_THUMBPRINT'
```

The provided PowerShell workflow has been reviewed, but signing cannot be executed
here without Windows and authorized signing credentials. No trusted-signature or
Smart App Control acceptance claim is made for the current download. Signing and
SmartScreen reputation are separate; a newly signed build can still show warnings.

References: [SignTool](https://learn.microsoft.com/en-us/windows/win32/seccrypto/signtool),
[Artifact Signing profiles and roles](https://learn.microsoft.com/en-us/azure/artifact-signing/concept-resources-roles),
[Windows signing options](https://learn.microsoft.com/en-us/windows/apps/package-and-deploy/code-signing-options).
