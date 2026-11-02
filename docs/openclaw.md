# OpenClaw Talk candidate

The daemon lives in the sibling `techo5` fork. Read its
[native Talk setup and Pi audit](../../techo5/docs/openclaw.md). This repository
continues to own the Dot's existing Linux image, kernel, drivers and boot setup.
This change does not alter wake models, microphone processing, speaker tuning,
kernel or firewall.

The common daemon's connected follow-up captures now retain the same native
logical Talk session after local reply playback drains. Each capture keeps its
own input/end and playback fences; silence, cancellation, mute, source changes
and disconnect close that logical conversation. This source repair preserves
native spoken-confirmation scope and adds no operator/admin grants. It still
requires native tests, matching server qualification and a separately authorized
device update before hardware acceptance can be claimed.

The same common client negotiates `activity-v1` for sequenced input outcomes and
input/work/output progress. Empty captures can settle without waiting for a model
timeout; unsupported or unknown telemetry keeps the existing watchdog. These
observations add no requests or authority and never replace local playback drain.

Build the modified source, rather than downloading the upstream daemon:

```bash
cd ../techo5/echod
GOOS=linux GOARCH=arm GOARM=7 CGO_ENABLED=0 go build -tags dot -trimpath -ldflags=-s -o ../../techo5-dot/bin/echod-dot-openclaw ./cmd/echod
```

For an image candidate, follow [building.md](building.md) to prepare the existing
inputs and companion tools, then explicitly supply the custom binary:

```bash
cd techo5-dot
python3 tools/linux/build-dot-rootfs.py --daemon bin/echod-dot-openclaw --release v0.0.0-openclaw-test --out bin/techo5-dot-openclaw-rootfs.tar.gz
```

The second command starts from the common parent containing both repositories;
after the first build, return there before changing into `techo5-dot`.
No signed release, boot/root-filesystem image or installation is produced merely
by the Go build. Reuse the established recovery procedure after selecting the
actual compatible model and qualifying matching Core/SDK/provider versions.
Preserve each device's own adopted firmware; do not distribute another unit's.

This candidate selects `dot` (Dot 2). The existing Dot 3 route requires
`dot,donut`, `--board donut` and its additional inputs, and is not qualified here.
Leave automatic upstream firmware installation off during a private custom
trial, since it would replace the new daemon.

A supervised Dot 2 has now booted the custom daemon on the signed `dot-v1.0.35`
Linux base with its own adopted firmware. Boot health, the installed daemon hash,
Wi-Fi persistence across a normal reboot and local API reachability were checked.
Only the daemon and release label differ from that base filesystem. Native
enrollment has also passed: both role tokens reconnect after bootstrap removal,
with `operator.talk`, the `talk` node capability and no node commands. That
enrollment predates the Talk-event scope repair: the new client also requires
an explicitly approved `operator.read` grant to receive native `talk.event`.
Its Gateway-wide read access must be reviewed before upgrading the live grant.
The optional device-local `openclaw.personal_agent_access` selection uses the
same shared client as Show: false requests exact read/talk; true requires exact
read/talk/write and separately approved native personal/device grants. It never
accepts admin scopes, falls back to setup after token rejection, or changes live
credentials by itself. See the sibling personal-access provisioning section.
The pending scope repair is not part of that boot/enrollment qualification. Real speech
remains unqualified; see the sibling
[verification record](../../techo5/docs/openclaw-verification.md) for the remaining
Gateway/provider and acoustic checks.
