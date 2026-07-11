#  SpotiLink

SpotiLink is a self-hosted, full-featured desktop application designed to download, tag, and organize music and video streams directly from Spotify or YouTube links. It fetches high-fidelity, authentic metadata and high-resolution cover art from Spotify web sources (rather than guessing details from YouTube titles) and matches tracks intelligently against corresponding video/audio candidates. 

With interactive preview players, persistent visual themes, advanced search matching, and automatic local file deduplication, SpotiLink offers a professional, offline-first archiving solution.

---

##  Visual Identity & Themes
SpotiLink features an elegant visual theme engine containing **seven distinct, hand-crafted presets** that can be swapped instantly from the interface:

*   **Studio (Default)**: A clean, classic obsidian dark theme focusing on rich grays and vibrant gold accents.
*   **Nightdrive**: A late-night neon pink and midnight blue cyber-synth aesthetic with subtle ambient glow.
*   **Vinyl**: A warm retro-cream layout using deep amber accents and a comforting analog aesthetic.
*   **Daylight**: A crisp, high-contrast light mode designed for maximum legibility.
*   **Cyberpunk**: High-vibrancy cyan and neon purple highlights set against a dark grid canvas.
*   **Terminal**: A retro-futurist phosphor green scheme emulating classic CRT displays.
*   **Forest Aura**: A cozy, natural pairing of pine green and warm earth tones.

---

##  Core Capabilities

### 1. High-Fidelity Metadata Scraper
*   **Authentic Tagging**: Automatically queries and writes real ID3 tags (artists, album title, release year, genre, and track number) using direct Spotify scraper nodes—bypassing the need for custom Spotify Developer API keys or user logins.
*   **Per-Track Cover Art**: Embeds authentic, high-resolution track artwork directly into each file. If Spotify lacks track-specific imagery, the master playlist/album cover is backfilled automatically.
*   **In-Place Metadata Repair**: Re-running the application over existing files automatically identifies and embeds missing tags or artwork in-place without re-downloading the streams.

### 2. Intelligent Search & Matching
*   **Smart Scoring**: Evaluates candidate matches based on a combined score of title similarity, artist verification, and exact duration comparisons. 
*   **Explicit Track Guard**: Active filters prioritize original explicit tracks and actively penalize clean, censored, or radio-edit re-uploads.
*   **Keyword Filters**: Automatically avoids cover versions, karaoke tracks, instrumental copies, and sped-up/remixed variants unless requested.

### 3. Comprehensive Format Support
*   **Audio Archives**: High-fidelity encodings to `MP3`, `FLAC` (lossless), `M4A/AAC`, `Opus`, and `WAV` (lossless).
*   **Video Formats**: Downloader pipelines support high-resolution `MP4` and `MKV` output.
*   **iPod Classic Video**: Built-in profile containing custom H.264 Baseline 3.0 / AAC MP4 parameters, downscaled to 640x480 max resolution. This profile is specifically structured to run natively on legacy clickwheel hardware (5G, 5.5G, 6G iPods) without lag, skips, or audio sync errors.

### 4. Interactive Audio Control & Queue
*   **30-Second Previews**: Tap the play icon next to any Spotify track to listen to high-quality audio previews before initiating downloads.
*   **Custom Match Links**: Paste custom YouTube URLs directly onto individual tracks in the list to bypass automatic search matching.
*   **Responsive Queue Control**: Manage downloads with an interactive queue, including real-time progress bars, per-track status badges, and safe active download cancellation.

---

##  Windows Installation Guide

Setting up SpotiLink on Windows takes only a few minutes. 

### Step 1: Install Python (3.10+)
1. Download the Python installer for Windows from the [Official Downloads Page](https://www.python.org/downloads/).
2. Run the installer.
3. **CRITICAL**: Check the **"Add python.exe to PATH"** checkbox at the bottom of the installation wizard before proceeding.

### Step 2: Install Node.js (18+)
1. Download Node.js from the [Official Downloads Page](https://nodejs.org/).
2. Run the `.msi` installer and proceed with the standard settings.

### Step 3: Install FFmpeg (Required)
FFmpeg processes and packages audio/video files.
1. Download the latest "essentials" build from [gyan.dev FFmpeg Builds](https://www.gyan.dev/ffmpeg/builds/).
2. Extract the ZIP file and rename the folder to `ffmpeg` (e.g., move it to `C:\ffmpeg`).
3. Add the `bin` folder to your Path:
    *   Search for **Edit the system environment variables** in the Windows Start menu.
    *   Click the **Environment Variables...** button.
    *   Under *System variables* (or *User variables*), double-click the **Path** variable.
    *   Click **New** and paste the path to your folder: `C:\ffmpeg\bin`
    *   Click **OK** to save and apply.

---

##  Launching the Application

### Option A: Standard Startup (Automatic)
Double-click the **`run.bat`** file in the root folder.
*   On the **first boot**, the script automatically installs all required Python packages, installs Node dependencies, compiles the client proxy, and launches the application in a clean, standalone desktop window.
*   On **subsequent runs**, it immediately opens the desktop interface in seconds.

### Option B: Terminal Startup (Manual)
To run the server manually in development or custom server mode, execute the following commands in your shell:

```bash
# Install package dependencies
npm install

# Start the Flask web server
python run.py
```
Open your web browser and navigate to `http://127.0.0.1:3000`.

---

##  Creating a Standalone Portable `.exe`
You can compile SpotiLink into a single, portable Windows executable (`.exe`) to run on other machines without requiring Python to be pre-installed.

1. Double-click **`build_exe.bat`** in the repository.
2. The PyInstaller utility compiles the Flask server, metadata modules, and web resources.
3. Once compiled, your portable executable is available at:
   `dist\SpotiLink.exe`

*(Note: Target machines still require FFmpeg on their system Path to run encoding operations).*

---

##  Pro-Tips & Advanced Features

###  Bypassing YouTube Verification Errors
If you encounter "Sign in to confirm you are not a bot" errors:
1. Open the **Settings** menu in SpotiLink.
2. Under **Browser Cookies**, select your active web browser (Chrome, Edge, Firefox, Brave).
3. Ensure you are signed into YouTube on that browser. SpotiLink will safely access your browser's session cookies to confirm authentic, human downloads.
4. Alternatively, you can paste raw `cookies.txt` contents (Netscape format) directly into the provided text area.

###  Exportify CSV Fallback
If Spotify changes its layout and the web scraper fails to resolve a link:
1. Visit [Exportify](https://exportify.net) and export your playlist as a `.csv` file.
2. In SpotiLink, click the **"or paste an Exportify CSV instead"** link under the main input field.
3. Paste the CSV content directly. The app will resolve the tracks perfectly.

###  Avoiding Duplicate Downloads
*   Set an **Output Folder** (type it manually or click **Browse** for a native OS folder dialog).
*   Add folders to scan under **Scan for existing files**. If a matching track is found locally, SpotiLink automatically copies or moves it into the main output folder instead of re-downloading.

---

##  Repository Map

```text
/
├── server.ts              # Proxy orchestration server for AI Studio
├── metadata.json          # Container configurations and permissions
├── tsconfig.json          # TypeScript compilation settings
├── package.json           # Node.js project manifests and scripts
└── spotilink/             # Core Application Directory
    ├── app.py             # Flask REST API backend and routes
    ├── downloader.py      # Search matching, yt-dlp downloader, and tagging
    ├── spotify_meta.py    # Direct Spotify web scraper
    ├── launch.py          # pywebview container wrapper
    ├── requirements.txt   # Python dependency list
    ├── run.bat            # Windows startup script
    ├── build_exe.bat      # Single executable builder script
    ├── templates/
    │   └── index.html     # Responsive SPA layout
    └── static/
        ├── css/
        │   └── style.css  # Layout, themes, custom controls, and animations
        └── js/
            └── app.js     # Client event handlers, audio engine, and queue manager
```

---
*Disclaimer: This is a private archiving utility. Please ensure your usage complies with local copyright laws and third-party platform terms of service.*
