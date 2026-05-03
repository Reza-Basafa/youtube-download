# YouTube Downloader

Download YouTube videos (and playlists) automatically using GitHub Actions. Videos are stored as zipped release assets — your repository stays small.

---

## How it works

1. You add a YouTube URL to `videos.json` with `"status": "pending"`
2. Push to GitHub
3. GitHub Actions downloads the video using **yt-dlp** at 720p and uploads it as a `.zip` to a GitHub Release
4. `videos.json` is automatically updated with the download status and release link

Everything runs on GitHub's servers — no local software needed.

---

## Setup

### 1. Fork or clone this repo

Create your own copy of this repository on GitHub.

### 2. Make the repo public *(recommended)*

Go to **Settings → General → Danger Zone → Change visibility → Public**.

> If you keep the repo private, release asset download links will require GitHub login.

### 3. That's it

The workflow uses the built-in `GITHUB_TOKEN` — no extra secrets or configuration needed.

---

## Adding a video

Open `videos.json` and add an entry:

```json
[
  {
    "url": "https://www.youtube.com/watch?v=dQw4w9WgXcQ",
    "status": "pending",
    "title": null,
    "release_tag": null,
    "release_url": null,
    "error": null,
    "added_at": "2026-05-03",
    "downloaded_at": null
  }
]
```

Only `url`, `status`, and `added_at` are required when adding. The rest are filled in automatically.

Push the change — the workflow starts within seconds.

---

## Adding a playlist

Use a playlist URL exactly the same way:

```json
{
  "url": "https://www.youtube.com/playlist?list=PLxxxxxxxxxxxxxxxx",
  "status": "pending",
  "title": null,
  "release_tag": null,
  "release_url": null,
  "error": null,
  "added_at": "2026-05-03",
  "downloaded_at": null
}
```

All videos in the playlist are downloaded and bundled into a single release.

---

## Downloading your video

1. Go to the **Releases** tab of your repository
2. Find the release named after your video
3. Download the `.zip` file
4. Extract it — the `.mp4` is inside

### Large videos (split into parts)

If a video exceeds 1.9 GB, it is split into numbered zip files:

```
video.part1.zip
video.part2.zip
video.part3.zip
```

Extract all parts, then reassemble:

```bash
cat video.part*.mp4 > video.mp4
```

---

## Status values

| Status | Meaning |
|---|---|
| `pending` | Waiting to be downloaded |
| `done` | Downloaded and uploaded to a release |
| `failed` | Something went wrong — check the `error` field |

---

## Video quality

Videos are downloaded at **720p maximum**. This keeps file sizes manageable and ensures compatibility with GitHub's 1.9 GB per-asset limit.

---

## Limits

| Limit | Value |
|---|---|
| Max quality | 720p |
| Max file size per release asset | 1.9 GB (split automatically if larger) |
| GitHub Actions minutes (private repo) | 2,000 / month free |
| GitHub Actions minutes (public repo) | Unlimited free |

---

## Troubleshooting

**The workflow didn't trigger**
- Make sure you edited and pushed `videos.json` (the workflow only triggers on changes to that file)
- Check the **Actions** tab to see if a run is in progress or failed

**Status is `failed`**
- Open `videos.json` and read the `error` field for the reason
- Common causes: private/deleted video, geo-blocked content, or an unsupported URL

**Download link doesn't work**
- If the repo is private, you must be logged into GitHub to download release assets
- Make the repo public to get direct shareable download links
