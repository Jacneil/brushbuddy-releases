# Brushbuddy OTA Releases 📦

Public host for **encrypted** Brushbuddy firmware containers.

- Files here are **`BBC1` containers = ciphertext only** — safe to be public:
  each payload decrypts exclusively with the *fleet key*, which lives in your
  operator vault and inside each Brushbuddy's protected NVS. This repo can
  never leak the firmware.
- Format: `magic "BBC1"(4) | version(8) | IV(16) | SHA256-CTR ciphertext | HMAC-SHA256(32)` (60 B overhead).
  See `scripts/encrypt_release.py` in the private `brushbuddy` repo.
- The Brushbuddy app fetches `container.bin` from this repo, verifies HMAC
  on-device, then boots the new image only after verification
  (plus secure-boot signature once lock-down runs).

## Files
| File | Version | Notes |
|---|---|---|
| `container.bin` | v1.1 | latest release — always overwritten on publish |

## Publishing (operator only)
```bash
# in the private repo, after a signed build:
python3 scripts/encrypt_release.py --key keys/fleet.key --version vX.Y \
    --in build/brushbuddy_eyes.ino.bin --out container.bin
cp container.bin /path/to/brushbuddy-releases/container.bin
git -C /path/to/brushbuddy-releases commit -am "release vX.Y" && git push
```
