# colaH16/UxPlay

A fork of [FDH2/UxPlay](https://github.com/FDH2/UxPlay) by F. Duncanh and contributors
(itself derived from RPiPlay by Florian Draschbacher). It carries one small change on top of
an upstream release and is built for the Homebrew formula
[`colah16/tap/uxplay`](https://github.com/colaH16/homebrew-tap), which runs UxPlay as an
audio-only AirPlay receiver on macOS.

Report anything not caused by the change below to upstream.

## Branches and tags

| Ref | Base | Contents |
|---|---|---|
| `macos-brew-1.73.7` | upstream tag `v1.73.7` | the change below |
| `v1.73.7-cola.1` | `macos-brew-1.73.7` | release with a macOS bottle and source |

`master` tracks upstream unchanged.

## Change: `-async` client clock offset (2026-10-31, colaH16)

In Audio-Only (ALAC) mode with `-async`, `uxplay.cpp` maps the client's timestamps onto
the local clock with an offset taken from the first audio packet, and keeps it until every
connection closes. If NTP timing has not synced when that packet arrives, the offset is a
guess from the local clock, so audio plays without the `-async` delay, ahead of the
client's video, for the rest of the connection. On macOS this happens whenever the first
timing request fails, for example while macOS asks a `launchd` agent for Local Network
access.

The change:

- replaces the guessed offset once NTP has synced;
- recomputes the offset for each Audio-Only stream (each `audio_get_format`);
- logs it as `audio clock offset (local - client)`.

Mirror mode (`usingScreen=1`) and video are unchanged.

## License

GPL-3.0-or-later, as upstream. See [LICENSE](LICENSE). Bundled third-party code keeps its own
license (see the README).
