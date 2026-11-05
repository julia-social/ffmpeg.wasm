# What Julia Social ships

Julia Social's sign-my-work ships `@ffmpeg/core` **0.12.10**, built from this
repository at upstream tag **v12.15** (commit
`71aa99d37c02a7b4c435275ca9ef50e612f6efa1`). We ship the npm build unmodified. The hashes below match
`package/dist/umd/` in the npm tarball for `@ffmpeg/core@0.12.10`.

## Shipped files

| File | SHA-256 |
|---|---|
| `ffmpeg-core.wasm` | `9f57947a5bd530d8f00c5b3f2cb2a3492faa7e5d823315342d6a8656d0a6b7b7` |
| `ffmpeg-core.js` | `b266ab5b952555881dd6310663986994a182acb2b7ff25cf10a25f7a37ac2b21` |

## Pinned dependencies

At v12.15, the `Dockerfile` fetches two libraries by branch name instead of by
tag or commit. On this branch (`julia-social-pins`) they are pinned to the
commits that were built into the shipped binary:

| Library | Upstream ref at v12.15 | Pinned commit |
|---|---|---|
| x264 (`github.com/julia-social/x264`, fork of `ffmpegwasm/x264`) | branch `4-cores` | `33cac6b77d5b9259c552156013a817ab23119612` |
| LAME (`github.com/julia-social/lame`, fork of `ffmpegwasm/lame`) | branch `master` | `2badea1974ae36cb8312afe99cff1e6b3b5decee` |

The other libraries and FFmpeg itself (`n5.1.4`) were already fetched by
release tag, so they are unchanged.

### How each commit was determined

`@ffmpeg/core` 0.12.10 was published to npm on 2025-01-07T10:13:14Z. Tag
v12.15 is dated 2025-01-07 11:12:48 +0100.

**x264 → `33cac6b`.** This is the tip of the `4-cores` branch: upstream x264
master at `baee400f` ("CI: Fix vlc-contrib processing on macos", core 164
r3095) plus one ffmpegwasm commit, "Use 4 logical cores by default"
(2022-08-22), which changes one line in `common/cpu.c`. That commit predates the
npm publish by more than two years. The shipped wasm contains
`x264 - core %d%s - ... Copy%s 2003-2022`, which matches the 2003-2022
copyright headers on this branch. Docker's `ADD <git>#ref` drops `.git`, so
x264's `version.sh` writes no `rNNNN <hash>` revision into the binary, and the
commit can't be read from the binary directly. It is the only commit the
branch could have resolved to unless the branch was force-pushed between
2022-08-22 and 2025-01-07, and nothing suggests that it was.

**LAME → `2badea1`.** The `master` branch of `ffmpegwasm/lame` has exactly one
commit, "Init commit with lame 3.100" (2020-10-29). The shipped wasm contains
the LAME version string `3.100`.

## Not pinned (build tooling, not compiled into the binary)

- The `# syntax=docker/dockerfile-upstream:master-labs` BuildKit frontend
  image is referenced by tag.
- The `emscripten/emsdk:3.1.40` base image is referenced by tag.
- `apt-get install` build tools (pkg-config, autoconf, automake, libtool,
  ragel, git) are installed without pinned versions.

## Source code offer

The shipped binary includes GPL-licensed components (FFmpeg built with
`--enable-gpl`, x264, x265, and others). As GPLv2 §3(b) requires, Julia Social
will provide the complete corresponding source code on request: this
repository at branch `julia-social-pins`, plus the pinned sources listed
above. The offer is valid for at least three years from the date we last
distribute the binary. To request it, email legal@julia.social.

## Also shipped: @ffmpeg/core-mt 0.12.10

Sign My Work also vendors `@ffmpeg/core-mt` 0.12.10, the multi-threaded build made by `make prd-mt` from the same tree. Its npm `gitHead` is 63ea4cc: v12.15 plus four commits that touch only the demo app. The same x264 and lame pins apply.

SHA-256 of `package/dist/umd/` as shipped:
- `ffmpeg-core.wasm`: be2c97605366b78f3f13e21b52e81a55a79e1f29c133b03a68ec187b1a2ec41a
- `ffmpeg-core.js`: 62f5f5f468a37861da12c4581c321bb5ca8ba2f7b776377e08dd2ab72de293f9
- `ffmpeg-core.worker.js`: 97322a227c5f3d5ccfd0d0825890a6deeba137106a09b633ca75cadf49ddd2cb

The GPLv2 §3(b) written offer above covers this build too.
