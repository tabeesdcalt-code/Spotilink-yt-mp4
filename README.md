# Playlist Studio

Paste a Spotify playlist/album/track link, pick a format, hit start.
It finds the tracks on YouTube, downloads them, tags them with the
*real* metadata from Spotify (not a guess off the video title), embeds
cover art, and sorts in anything you've already downloaded before so
re-runs only fill in what's missing.

No Spotify Developer account, no client ID/secret, no login required
for public playlists.

## What's new in this version

- **Found the real cause of missing per-track album art.** The scraper library has two internal paths for reading playlist tracks: a rich one with full per-track album art, and a sparse fallback (used whenever the rich request fails for any reason) that returns zero image data for every track -- by design, since Spotify's own embed widget never shows per-track art either, only one cover for the whole playlist. When that fallback kicks in, no amount of retrying gets real per-track art back. So now: any track that comes back with no art of its own automatically uses the playlist/album cover instead, both in the UI and in the embedded file. Confirmed by directly reproducing the exact scenario in a test.
- **YouTube links work too** — paste a plain YouTube video or playlist link (not just Spotify) and it downloads/tags the same way, using YouTube's own title/channel/thumbnail as the metadata source.
- **Album art gets backfilled on re-runs.** If a file already sits in your output folder without embedded art (e.g. from before this fix), the next run detects that and re-tags it in place — you don't need to delete anything or start over.
- **Real diagnostics**: if Spotify's own art fetch fails, the log now shows the actual HTTP error instead of a generic message, and the preview panel warns you up front if a playlist's tracks came back with no track-level art at all.
- **4 themes** (Studio / Nightdrive / Vinyl / Daylight) via the swatches top-right, saved between sessions.
- **Per-track album art thumbnails** in both the track list and the live download view, not just one cover image at the top.
- **Smarter matching**: scores YouTube candidates on duration match *and* title keywords (clean/radio edit/censored/instrumental/karaoke/sped-up/etc get penalized unless the real track title itself uses that word), plus a trust bonus for YouTube's auto-generated "Artist - Topic" channels. For tracks Spotify marks explicit, it now actively steers away from clean/censored reuploads.
- **30-second previews** — the small circle button next to each track plays Spotify's own preview clip before you commit to downloading anything.
- **Real cancel** — Cancel now actually aborts an in-progress download, not just skips ones that haven't started yet.
- **Native folder picker** — the Browse button pops a real OS folder dialog (this works because the server *is* your machine).
- **.m3u8 playlist file** — written alongside the tracks in original playlist order, so the folder drops straight into any player as a real playlist.

## Setup (one time)

1. Install **Python 3.10+** from [python.org](https://python.org/downloads) if you don't have it — tick *"Add python.exe to PATH"* during install.
2. Install **ffmpeg** — grab the "essentials" build from [gyan.dev/ffmpeg/builds](https://www.gyan.dev/ffmpeg/builds/), unzip it, and add its `bin` folder to your PATH. Every format needs this.
3. Double-click **`run.bat`**. First run installs everything (~1 minute); every run after that just launches the app in its own window.

## Using it

1. Paste a playlist/album/track link and hit **Load**.
2. Tap the small circle next to any track to hear a 30-second preview before committing.
3. Uncheck anything you don't want.
4. Pick a **format** (MP3, FLAC, M4A, Opus, or WAV) and, for lossy formats, a quality.
5. Set an **output folder** (type it, or hit **Browse** for a real folder dialog) and, optionally, folders to **scan for files you already have** — matches get moved in instead of re-downloaded.
6. Hit **Start download** and watch it go — per-track status, an overall progress bar, and a log if you want the detail.
7. If anything fails, you'll get a **Retry failed tracks** button — safe to hit any time, and safe to just re-run the whole thing later, since existing files are never re-downloaded.

**If a Spotify link won't resolve:** export the playlist as CSV from [exportify.net](https://exportify.net) and use "paste an Exportify CSV instead" under the URL box. This is the reliable fallback if Spotify ever changes something before the scraper catches up.

## Getting an actual `PlaylistStudio.exe`

Run **`build_exe.bat`** once. It uses PyInstaller to package everything into a single double-clickable file at `dist\PlaylistStudio.exe` — no Python needed to *run* it after that (ffmpeg still needs to be on PATH). This has to happen on your machine because Windows executables are built on Windows; there's no way to hand you a working one from outside it.

## Good to know

- **Audio quality is bounded by what's on YouTube.** Typically ~128–160kbps Opus/AAC, sometimes better. Choosing FLAC or WAV avoids a second lossy re-encode and gives clean room for large embedded art, but it can't restore detail that was never in the source — it's not a magic upscaler. Opus/WebM native extraction (no re-encode at all) is the most efficient way to get exactly what YouTube has, byte-for-byte.
- **"Smart match"** (on by default) scores every candidate on duration closeness plus title/channel signals instead of blindly grabbing the first hit — noticeably fewer wrong-song, clean-edit, or sped-up downloads.
- **YouTube's "Sign in to confirm you're not a bot" error:** set *Browser cookies* to Chrome/Firefox/Edge in Advanced settings (you need to be logged into YouTube in that browser). If it's still happening, run `pip install -U yt-dlp` — YouTube changes this often and yt-dlp ships fixes frequently.
- Everything runs on `127.0.0.1` only — nothing here is exposed to your network.
- This is a personal tool for a library you already have legitimate access to via your own Spotify account — it's on you to use it in line with Spotify's and YouTube's terms.

## If something breaks

- **Setup check** (top-right button in the app) shows which pieces are missing or working.
- Metadata scraping is unofficial by nature — if `spotifyscraper` ever breaks against a Spotify change, the CSV fallback above still works, and `pip install -U spotifyscraper` will usually pick up a fix quickly (it has an active canary/issue tracker upstream).

## What's inside

- `app.py` — Flask backend (routes for resolving links, running download jobs, progress polling, the native folder picker)
- `spotify_meta.py` — Spotify metadata scraping (+ Exportify CSV fallback)
- `downloader.py` — YouTube search/match, download, tagging, cover art, the sort-in-existing-files step, M3U export
- `templates/`, `static/` — the UI (4 themes, ambient background, track previews)
- `launch.py` — opens the app in its own window (pywebview) instead of a browser tab
