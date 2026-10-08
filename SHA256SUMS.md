# Verified field-artifact hashes (not redistributed)

The original files are **not** included in this repository. Verify provenance, licensing and the exact target device before using any binary.

| Artifact | Size | SHA-256 |
|---|---:|---|
| Patched APBoot 1.5.7.2, proven on both devices | 338,488 bytes | `5df37951a8b8796cbb8b8a57aa72161d5288e188371aac1943918671ebd2938d` |
| Known-good OpenWrt initramfs | 9,229,096 bytes | `4332b1281f7259dafe5cab82fdeea1401f180799415d711483de95f1479712c8` |
| Known-good OpenWrt sysupgrade | 9,707,777 bytes | `800e8ca3af67c8c6c0d77c5fb2b1d07ed0350c50506ed4f429e974d9f4bca516` |
| Build configuration (historical recorded hash) | — | `71d0ca0d2e848186107267c9dda307c803e314d757d2a80b5ed1ccd6b0153515` |

**Warning:** A different APBoot artifact from a later experimental build had SHA-256 `78f5069e8fb9b9fa05d5f00baeefcd0bcc1e4ebe1f2c0695592c8af30028091c`. That is **not** the proven bootloader; do not substitute it.

Source version recorded: OpenWrt SNAPSHOT `r0-f795077`, source Git `f795077`. Hashes identify specific historical test artifacts; they do not establish redistribution permission or universal hardware compatibility.
