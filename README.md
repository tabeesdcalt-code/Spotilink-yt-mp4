#  Playlist Studio

[![Platform: Windows](https://img.shields.io/badge/platform-Windows-blue.svg?style=flat-square)](https://microsoft.com/windows)
[![Python: 3.10+](https://img.shields.io/badge/python-3.10%2B-blue.svg?style=flat-square)](https://python.org)
[![Node.js: 18+](https://img.shields.io/badge/node.js-18%2B-green.svg?style=flat-square)](https://nodejs.org)
[![FFmpeg: Required](https://img.shields.io/badge/FFmpeg-Required-red.svg?style=flat-square)](https://ffmpeg.org)
[![License: MIT](https://img.shields.io/badge/license-MIT-yellow.svg?style=flat-square)](LICENSE)

Playlist Studio is a self-hosted, desktop-focused tool to download and tag music or video files from Spotify playlists, albums, or individual track links. 

It fetches real, rich metadata and high-resolution cover art from Spotify (not guesses from video titles) and matches tracks against YouTube audio/video. It also supports scanning your local folders to avoid downloading duplicates, native folder selection, and beautiful theme customization.

---

##  Features

- **Real Spotify Metadata**: Tags downloaded tracks with authentic metadata (artists, album, release year, genres, track numbers, and genuine high-resolution per-track artwork).
- **YouTube Links Supported**: Paste plain YouTube video, playlist, or short links directly for seamless download and fallback tagging.
- **Dynamic Artwork Backfilling**: If a track is already downloaded but is missing its embedded artwork, re-running the tool detects and embeds the correct artwork in-place without re-downloading.
- **7 Gorgeous Visual Themes**: Change the visual vibe on the fly! Includes:
  - `Studio` (Classic Obsidian Dark)
  - `Nightdrive` (Neon Pink/Midnight Blue)
  - `Vinyl` (Warm Retro Cream/Amber)
  - `Daylight` (Clean High-Contrast White)
  - `Cyberpunk` (Neon Cyan/Purple Glowing Grid)
  - `Terminal` (Green Phosphor Phosphorescent CRT)
  - `Forest Aura` (Cozy Deep Pine/Earth Warmth)
- **Extensive Format Selection**:
  - **Audio Formats**: MP3, FLAC, M4A, Opus, WAV (lossless).
  - **Video Formats**: MP4, MKV.
  - **iPod Classic Video**: Custom H.264 Baseline 3.0 / AAC MP4 profile, downscaled to 640x480 max resolution — specifically structured to run natively on the legendary 5G, 5.5G, and 6G iPod Classic clickwheel hardware without lag or sync errors.
- **30-Second Previews**: Listen to Spotify's official high-quality track previews directly in the app before committing to download.
- **Smart Queue & Real Cancel**: Fully interactive queue panel. Cancel active downloads safely on-the-fly and clear finished jobs.
- **Native OS File Selectors**: Pick output directories and scanning directories with real Windows explorer folder dialogs.
- **Automated M3U Playlists**: Exports custom `.m3u8` playlist files inside your download folder matching the exact order of your Spotify playlist.

---

##  Windows Installation Guide

Setting up Playlist Studio on Windows is fast. Just follow these steps:

### Step 1: Install Python (3.10+)
1. Download Python for Windows from the [Official Downloads Page](https://www.python.org/downloads/).
2. Run the installer.
3. **CRITICAL**: Make sure to check the box that says **"Add python.exe to PATH"** at the bottom of the installer window before clicking install.

### Step 2: Install Node.js (18+)
1. Download Node.js from the [Official Downloads Page](https://nodejs.org/).
2. Run the `.msi` installer and follow the standard instructions.

### Step 3: Install FFmpeg (Required)
FFmpeg is used for extracting audio, transcoding videos, and preparing iPod-compatible streams.
1. Download the latest "essentials" build zip from [gyan.dev FFmpeg Builds](https://www.gyan.dev/ffmpeg/builds/).
2. Extract the zip file and rename the folder to `ffmpeg` (move it somewhere permanent, like `C:\ffmpeg`).
3. Add FFmpeg to your PATH environment variable:
   - Open the Windows Start Menu, type `env`, and select **Edit the system environment variables**.
   - Click on the **Environment Variables...** button.
   - Under *User variables* or *System variables*, select the `Path` variable and click **Edit...**.
   - Click **New** and paste the path to your extracted `ffmpeg\bin` folder (e.g., `C:\ffmpeg\bin`).
   - Click **OK** on all windows to save.

---

##  How to Run

### Option A: The Easy Way (Automatic)
Double-click the **`run.bat`** file in the root directory. 
- On the first run, the script will automatically install all backend dependencies, build the frontend asset proxy, and launch the application in its own native-looking app window.
- On subsequent runs, it will immediately boot up the application in seconds.

### Option B: The Manual Way (Developer Terminal)
If you prefer running via PowerShell, Command Prompt, or Git Bash, run:

```cmd
# 1. Install Node dependencies
npm install

# 2. Run the application
python run.py
```

Now open your web browser to `http://127.0.0.1:3000` (or the port specified in your console logs).

---

##  Packaging as a Standalone Portable `.exe`

Want to carry Playlist Studio around on a thumb drive without needing Python installed on other PCs?
1. Double-click **`build_exe.bat`** in the repository.
2. The compilation utility uses **PyInstaller** to compile the complete Flask server, Spotify scraper engines, and user interfaces into a single Windows executable file.
3. Once completed, your portable executable is located inside the `dist\` folder:
   ```text
   dist\PlaylistStudio.exe
   ```
*(Note: Your target machine will still need FFmpeg on its PATH to transcode formats).*

---

##  Troubleshooting & Tips

###  Bypassing YouTube "Sign in to confirm you're not a bot"
If Google detects excessive downloads and throws a bot error:
1. Expand the **Advanced Settings** dropdown inside Playlist Studio.
2. Under **Browser Cookies**, select the name of the browser you actively use on your computer (Chrome, Edge, Firefox, Brave, Vivaldi, Opera, etc.).
3. Make sure you are logged into a Google/YouTube account on that browser. Playlist Studio will safely leverage your browser cookies to verify you are a human.
4. If the error persists, open a command line and update yt-dlp:
   ```cmd
   pip install -U yt-dlp
   ```

###  Using Exportify CSV as a Backup
If a Spotify playlist link fails to resolve (for instance, if Spotify updates its web layout and the scraper temporarily breaks):
1. Go to [Exportify](https://exportify.net) and download your playlist as a `.csv` file.
2. In Playlist Studio, click **"Paste an Exportify CSV instead"** below the URL input field.
3. Paste the contents of your downloaded CSV file into the text box and press load. The app will scrape track details perfectly!

---

##  Technical Breakdown

- `app.py`: Flask-based REST backend managing resolution, active queues, folder picking, and diagnostic queries.
- `downloader.py`: Custom pipeline processing search scoring, yt-dlp downloads, mutagen tagging, cover art extraction, and sorting.
- `spotify_meta.py`: Lightweight web scraper querying Spotify metadata directly without API key limits.
- `templates/` & `static/`: Modern Tailwind-driven front-end featuring rich queue progress nodes, playback controls, and client-side states.
- `launch.py`: Seamless pywebview implementation encapsulating the browser inside a clean OS container frame.

---

*This application is a personal archiver tool. Please abide by copyright legislation and both platform agreements while downloading music or videos.*
