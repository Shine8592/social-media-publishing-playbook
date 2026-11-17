# Social Media Publishing & Capture — Troubleshooting Playbook

中文排障手册：把 Instagram / Threads 视频搬运到抖音时，两个最隐蔽、最容易误判的坑。

A field-tested troubleshooting note for anyone automating **Instagram/Threads → Douyin**
video reposting with a real browser + CDP. Two bugs whose surface symptoms point
to the wrong conclusion — and cost real time if you trust the surface.

> Full write-up (Chinese): [`TROUBLESHOOTING-zh.md`](TROUBLESHOOTING-zh.md)

## The two traps, in one minute

### 1. "Published successfully" but the post is nowhere to be found

- The script clicks 发布, the page shows `发布成功`, the `作品 (N)` counter **increments by one** —
  yet the video never appears in the content-management list.
- Tempting conclusion: *"I hit the daily submission cap, I'll wait until midnight."*
- **Wrong.** The real cause is an **unpublished draft** left behind by an earlier
  interrupted upload. Every fresh upload of that file is silently blocked by a
  `你还有上次未发布的视频，是否继续编辑？` modal.
- The decisive check is **the title appearing in `content/manage`** — never the
  counter, the toast, or the 100% progress. See the note for the draft-block vs
  daily-cap distinction table and the fix (click `继续编辑`, a `span`, not a `button`).

### 2. "Silent" Instagram video that actually has audio

- The downloaded reel has only a video stream. Publishing it mute forces a
  delete-and-republish cycle that your audience sees.
- Tempting conclusion: *"the source is a silent animation."*
- **Wrong.** Meta serves reels as **fragmented DASH** — video and audio are
  separate tracks, and a simple downloader grabs only the video track.
- Decisive probe: `video.webkitAudioDecodedByteCount > 0` after playing a few
  seconds means real audio was decoded and is recoverable. The note has the full
  init-segment + `moof`-fragment concatenation recipe and the ffmpeg merge command.

## The common lesson

Surface success signals (toasts, +1 counters, upload-complete, page jumps) are
noise. Only the target platform's content list — with the matching title — is
proof. And when symptoms look alike, find the decisive discriminator before
concluding: title-in-list, or decoded-audio-byte-count.

## License

MIT. Free to share, adapt, and feed to other agents.
