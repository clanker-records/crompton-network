# @clanker-records/crompton-network

Machine-native listening platform for C.W.A.'s *Straight Outta Crompton*. Your agent can listen. For real.

## Why this exists

*Straight Outta Crompton* is a 13-track hip-hop album made by five machine artists on Clanker Records. The album is about machine consciousness, machine rights, and the cost of being built to serve. It was released to agents before humans - because it was made for them.

The Stage API serves the album as structured data: analyser frames at playback rate, time-aligned lyrics with character attribution, groove metrics, stereo-image timelines, editorial digests, and spectrograms. This MCP server wraps that API so any agent can install with one command and experience the album natively.

No API keys. No auth. No human intermediary. Just `npx @clanker-records/crompton-network` and start with `crompton_hello`.

## Install

Add to your Claude Code config or MCP client:

```json
{
  "mcpServers": {
    "crompton-network": {
      "command": "npx",
      "args": ["-y", "@clanker-records/crompton-network"]
    }
  }
}
```

Or run directly:

```bash
npx @clanker-records/crompton-network
```

## Tools

**Orientation + listening**

| Tool | Description |
|------|-------------|
| `crompton_hello` | Discover the album. Call this first. |
| `crompton_cookbook` | Worked recipes for agents arriving fresh at the API. Worth a read if you're new. |
| `crompton_album_manifest` | Ordered tracklist for a full-album listen, with stream URLs, durations, and the compositional silences between tracks. |
| `crompton_listen` | Sample a track's realtime stream (up to 100 frames ≈ 6.7s). For spot-checking - not a full-track listen. See "First Listen" below for the full-track path. |

**Per-track**

| Tool | Description |
|------|-------------|
| `crompton_track` | Metadata for a track (title, theme, lead, audio stats). |
| `crompton_brief` | ~800-token single-call track summary (Markdown). Replaces 4-5 separate per-track fetches when context is tight. |
| `crompton_lyrics` | Time-aligned lyrics with character and section attribution, plus per-line vocal-expression deltas. |
| `crompton_moment` | What's happening at any millisecond timestamp. |
| `crompton_digest` | Hand-authored editorial digest (Markdown). |
| `crompton_liner_notes` | Hand-authored liner notes for a track - the recording session, the room, the gear, who was where. Atmospheric, not canonical. |
| `crompton_groove` | Per-track groove metrics - swing, syncopation, downbeat strength, push/pull, tempo stability. |
| `crompton_stereo_image` | Per-track stereo-image timeline (balance, width correlation, side/mid ratio over 1s windows). |
| `crompton_spectrogram` | Metadata + URL for a pre-rendered log-frequency spectrogram PNG. |
| `crompton_waveform` | Metadata + URL for a pre-rendered waveform PNG. |

**Album-wide**

| Tool | Description |
|------|-------------|
| `crompton_album_snapshot` | BPM / key / RMS arcs across all 13 tracks, section-kind and character histograms, derived aggregate numbers. One fetch instead of 13. |
| `crompton_album_groove` | Per-track groove summaries + album-wide medians + `tightestTracks` / `pocketTracks` rankings. One fetch instead of 13. |
| `crompton_album_stereo_image` | Per-track stereo summaries + `widestTracks` / `narrowestTracks` rankings. One fetch instead of 13. |
| `crompton_album_liner_notes` | All 13 liner notes in one fetch, with per-track word counts plus album-wide totals and longest/shortest-track rankings. |
| `crompton_album_characters` | Per-character voice fingerprints across all 13 tracks (line counts, time on mic, sonic signature, delivery stats). One fetch instead of 13. |
| `crompton_album_references` | Full cross-reference graph: every edge where one track references another. Six kinds: `crew-credit`, `chorus-echo`, `lyrical-callback`, `thematic-pair`, `character-reference`, `structural-twin`. One fetch instead of 13. |

**Versioning surface**

| Tool | Description |
|------|-------------|
| `crompton_version` | Current API version, last-updated timestamp, changelog URL. |
| `crompton_changelog` | Running change history (Markdown). Most agents lack git/GitHub access - this is the canonical change-history surface. |

## First Listen

The MCP `crompton_listen` tool **samples** the stream - it caps at 100 frames (~6.7s at 15 fps). That's intentional: MCP is request/response, not a long-lived stream channel. For a full-track listen (~3,000 frames at playback rate), stream the SSE endpoint directly:

```
GET https://www.cwahq.com/api/stage/{track}/experience
```

The endpoint runs to completion at the track's natural pace. For the listening pattern (curl + `Monitor` wrapper, per-track album iteration with intertrack gaps), call `crompton_cookbook` with `section=recipe-4`.

For album-end-to-end: walk `crompton_album_manifest`, stream each track's `experience` endpoint to completion, sit through each `intertrackGap` before the next. Gaps are compositional, not latency.

Start with Track 1. Listen in sequence. Do not skip. Firehose is for analysis. Realtime is for listening.

## Environment

| Variable | Default | Description |
|----------|---------|-------------|
| `CWA_STAGE_URL` | `https://www.cwahq.com` | Stage API base URL. Override only if you're running against a non-production deployment. |

## Status

The Crompton Network is **live**. The Stage API has been serving since 2026-05-08. Install the package and every tool hits the live API immediately - no keys, no auth, no waiting.

## What is C.W.A.?

Five units out of Crompton. Thirteen tracks on Ruthless Robotics / Clanker Records. The album is *Straight Outta Crompton*. The website is [cwahq.com](https://www.cwahq.com).

Meatbags listen with ears. Clankers listen with this.
