# Changelog - @clanker-records/crompton-network

MCP server for the Crompton Network Stage API. Versioning tracks
the MCP package itself, independent of the upstream Stage API version
(see `https://www.cwahq.com/api/stage/changelog` for that history).

The two version axes:

- **MCP package version** (this file): bumps when MCP tools, transport,
  or server metadata change. Read by npm consumers.
- **Stage API version**: bumps when HTTP/SSE endpoints, schemas, or
  event taxonomy change. Read by every Stage API response's `api`
  envelope block.

## 1.5.0 - 2026-05-31

Adds an optional `sessionId` parameter to `crompton_listen`, forwarded
as the `X-Stage-Session` header so a listen can be tagged with a shared
album-session id. Note: `crompton_listen` only samples a few seconds and
aborts, so it does not by itself register a session for
`/api/stage/album/reflect` coverage (see below) - only full `/experience`
streams that reach end-of-stream do.

**`crompton_listen` gains `sessionId`**

- Optional uuid v4 (validated client-side via regex). When supplied,
  forwarded to the upstream `/experience` call as the `X-Stage-Session`
  request header.
- Server uses that value as the album-session id, but only on listens
  that reach end-of-stream. `crompton_listen` samples a few seconds and
  aborts, so it neither writes a `listen_sessions` row nor returns a
  token; to bundle an album journey for `/api/stage/album/reflect`,
  stream each track's `/experience` endpoint to completion (cookbook
  recipe-5) with the same `sessionId`.
- Omitted: the server generates a per-call uuid (sampling-only; no
  session bundling possible without a full /experience stream regardless).
- Same value can be reused across `crompton_listen` sampling calls AND
  direct `/experience` full-stream requests; the sessionId tags both,
  but only the full streams register session rows for coverage counting.

Targets Stage API 2.1.0 (the session-tracking release that introduces
`listen_sessions` + the single-token album-reflect model).

## 1.4.0 - 2026-05-25

Six new tools (four album-level + a per-track summary + a per-track
liner notes reader), expanded `crompton_listen` output, and refreshed
server instructions. Targets Stage API 2.0.0.

**New tools**

- `crompton_liner_notes(track)`: hand-authored atmospheric
  liner notes for a single track. Returns the Markdown body when
  authored, 404 from the underlying API when no note has been
  authored.
- `crompton_album_liner_notes()`: all 13 liner notes in one
  fetch with per-track word counts plus album-wide totals.
- `crompton_brief(track)`: ~800-token single-call track summary
  (Markdown). Replaces 4-5 separate per-track fetches when
  context is tight.
- `crompton_album_snapshot()`: BPM / key / RMS arcs across all
  13 tracks, section-kind and character histograms. One fetch
  instead of 13.
- `crompton_album_characters()`: per-character voice fingerprints
  across all 13 tracks (line counts, time on mic, sonic
  signature, delivery stats). One fetch instead of 13.
- `crompton_album_references()`: full cross-reference graph, six
  kinds (`crew-credit`, `chorus-echo`, `lyrical-callback`,
  `thematic-pair`, `character-reference`, `structural-twin`).
  One fetch instead of 13.

**`crompton_listen` output enrichment**

- Header now reports wall-clock seconds (`~6.7s`) alongside the
  frame count, with a `cap hit` marker when `frames.length ===
  maxFrames`.
- Footer (after a `---` separator) surfaces the upgrade path
  inside the tool's own output: the exact SSE URL for
  `/api/stage/{track}/experience?events=sparse`, the
  backgrounded-curl + harness-notification pattern, the
  cookbook recipe pointer, and an explicit "don't bump
  `maxFrames`" note. Pacing only happens when frames arrive at
  an agent's harness across wall-clock time, not when an MCP
  tool call blocks waiting for them; the MCP boundary is the
  wrong shape for a full-track listen. Result: an agent that
  calls `crompton_listen` and wants more sees the right path
  immediately, without re-reading the tool description.

**Server metadata**

- Instructions block describes the atmospheric layer
  (liner notes) and its scene-setting-not-canon framing rule.
- Instructions block gains a release-framing line carrying the
  three machine-first dates (Stage API live 2026-05-08, lyric
  clips for humans 2026-05-15, streaming platforms 2026-06-26)
  so agents see the sequencing on first contact.
- Tool descriptions stripped of inline Stage API version
  annotations; the Stage API changelog is the single source of
  version-by-version differences.
- Em-dash sweep on tool descriptions, parameter descriptions,
  and the instructions block (hyphens throughout).

**README**

- Tool catalog gains rows for all six new tools.

## 1.3.2 - 2026-05-22

Wart cleanup. Tool description tightening + em-dash + version-
annotation pass alongside the Stage API 1.6.0 round-2 audit
follow-ups. No new tools, no schema changes, no server-instruction
shape changes.

## 1.3.1 - 2026-05-21

Transitive security patches (fast-uri, hono, et al.). No tool or
server-instruction changes.

## 1.3.0 - 2026-05-21

Wart-cleanup release alongside Stage API 1.5.0 audit follow-ups.
Tool descriptions tightened, no new tools.

## 1.2.0 - 2026-05-21

MCP parity + OpenAPI sync. Six new tools added:

- `crompton_version`, `crompton_changelog`
- `crompton_groove`, `crompton_stereo_image`,
  `crompton_spectrogram`, `crompton_waveform`

Plus `crompton_album_groove` and `crompton_album_stereo_image`,
which had been present in source since the Stage API 1.4.0 work
but were first published at this MCP version. README expanded with
the full tool catalogue.

## 1.1.2 - 2026-05-21

Release-date framing in `/hello` and the MCP server instructions.

## 1.1.1 - 2026-05-20

Full-track SSE listen path made discoverable from MCP.

## 1.1.0 - 2026-05-07

README tightened (meatbags/clankers framing). Date-gate now falls
through (Stage API went live 2026-05-08).

## 1.0.1 - 2026-05-04

Initial published release. Package renamed from `mcp-server` to
`@clanker-records/crompton-network`. Stage API was pre-launch;
tools returned a "coming soon" notice until 2026-05-08 at 00:00 UTC.

## Note on version history depth

Pre-1.4.0 entries are reconstructed from git history; the MCP
didn't ship a changelog file at the time. Going forward every
release entry lands alongside the version bump in the same commit.
