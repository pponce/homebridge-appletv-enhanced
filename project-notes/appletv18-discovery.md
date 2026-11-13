# AppleTV18,1 compatibility handoff

## Objective

Recognize AppleTV18,1 devices reported by pyatv 0.18 as an unknown model and operating system, and support installation directly from this GitHub fork with the Homebridge APT package's hb-service command.

## Repository and version

- Repository: https://github.com/pponce/homebridge-appletv-enhanced
- Base commit: edb204810a5a5af6c40d2a167621af0a78b2e473
- Validation branch: fix/appletv18-discovery
- Installation branch: main
- Package version: 1.16.0

## Current state

- Compatibility commit 33462a233b19c0dba5640179c64ab20734fb0686 is pushed to main and fix/appletv18-discovery.
- The remote helper and package scripts were read back and verified after the push.
- No GitHub Actions runs or check results appeared for that commit. Do not treat the full build as validated.
- Remote commits preserve the existing repository history. The authoring workspace used a text source snapshot for local validation; its synthetic local root commit must not be pushed to GitHub.
- A live installation now reaches the PIN pairing page and displays PINs on the Apple TV, confirming the discovery fallback is active. Pairing then repeats after PIN submission; the precise device-side attempt log has not yet been collected.
- Pin srptools to 1.0.1 with pyatv 0.18.0. Upstream pyatv issue #2934 documents that srptools 2.0.0 rejects the positional constructor argument used immediately after PIN submission. The package version remains 1.16.0 as requested.

## Changes

- Centralize the discovery filter in src/deviceSupport.ts.
- Accept the exact hardware identifier AppleTV18,1 when its reported operating system is Unknown or TvOS.
- Preserve the existing supported-model checks and device identity. Do not broaden support to arbitrary unknown devices or modify pyatv's installed files.
- Add npm prepare for GitHub installs. Remove npm ci from the build script to avoid recursive prepare execution; npm ci now installs dependencies and runs prepare itself.
- Add regression tests, package checks in CI, and public GitHub installation instructions.

## Validation

- Five discovery regression tests passed on Node 24.22.1, including mixed-device scans and rejection of unrelated AirPlay devices.
- Node's TypeScript parser accepted the changed source files.
- Package and lockfile versions match; git diff --check passed.
- The README installation block passed Bash syntax checking and simulated success, stop failure, add failure and start failure scenarios. It attempts to start Homebridge after an add failure and always prints an END marker.
- Full dependency installation in the authoring workspace was blocked by a registry HTTP 403. Full lint and TypeScript compilation therefore require GitHub Actions or the installation host.
- Live PIN pairing, app control, power state and playback monitoring remain to be verified on hardware.
- Pairing dependency smoke check passed: the unmodified __init__, initialize and step1 methods extracted from pyatv v0.18.0's SRPAuthHandler initialized a test-PIN SRP session using the upstream srptools v1.0.1 and six 1.17.0 source. This checks local PIN processing, not a full exchange with an Apple TV.

## Next steps

1. Check the Build workflow for the compatibility commit.
2. Install from main using the complete stop/add/start block in README.md. No npm publication is required.
   The plugin checks exact Python dependency versions at startup and installs the updated requirements when they differ; no manual edit to the live virtual environment is needed.
3. Keep the Apple TV awake and follow the plugin's pairing URL from /var/lib/homebridge/homebridge.log.
4. Verify PIN pairing, power, app selection and playback state. Diagnose any subsequent protocol failure separately from model discovery.

## Upstream context

- Matching report: https://github.com/maxileith/homebridge-appletv-enhanced/issues/1512
- Pairing dependency report: https://github.com/postlund/pyatv/issues/2934
- pyatv maps the raw model identifier to node-pyatv's modelName property.
- The compatibility filter deliberately preserves the original metadata; pyatv may continue to display Unknown OS until upstream recognizes the model.
