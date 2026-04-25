# music_cleanup.py

A PyQt5 desktop tool for cleaning up MP3 files downloaded from YouTube via yt-dlp, and finding duplicate audio files in your library. Two tabs, one window.

---

## Features

### Cleanup tab

- **Skips non-songs** — files over 13 minutes are ignored (full concerts, DJ sets, etc.)
- **Cleans filenames** — strips YouTube junk like `| Official Video [abc123xyz]` from filenames
- **Sets ID3 tags** — reads `Artist - Title` from the filename and writes the artist and title tags
- **Fetches album art** — searches multiple sources and embeds the best result. Skips files that already have art.
- **Fetches artist** — looks up missing artist tags from metadata sources when the filename has no `Artist -` prefix

### Duplicates tab

- Scans a folder (including subfolders) for duplicate audio files across all common formats
- Groups files by song title, confirms matches via iTunes API, and shows real-time scan progress
- Three modes: **Dry Run** (preview only), **Interactive** (review and choose what to delete), **Auto-Delete** (keeps the highest quality file automatically)
- Duration tolerance setting to catch slight-length variants of the same song

---

## Requirements

```bash
pip install mutagen requests PyQt5 --break-system-packages
```

---

## Usage

**Launch the GUI:**

```bash
python3 music_cleanup.py
```

**Or pass a folder directly (runs cleanup with all options on):**

```bash
python3 music_cleanup.py /path/to/your/music
```

---

## Cleanup tab options

All options are on by default and can be toggled before running:

| Option | What it does |
|---|---|
| Clean filenames | Strips YouTube junk from the filename |
| Set ID3 tags | Writes artist and title tags from the filename |
| Fetch album art | Searches all sources and embeds the best result |
| Fetch artist | Looks up missing artist from metadata sources |

Hitting **Run Cleanup** opens a terminal (Konsole, GNOME Terminal, or xterm) showing live progress.

---

## Art and artist sources (in order)

1. **Spotify** — requires a free Client ID + Secret (see below)
2. **iTunes** — broad coverage, no key needed
3. **Last.fm** — good for less mainstream tracks, free API key
4. **Deezer** — no key needed
5. **MusicBrainz** — open database fallback (art only)

Each source is tried in order and the chain stops as soon as one succeeds.

---

## API keys

Keys are entered in the GUI and saved to `~/.music_cleanup_config.json`. They are never stored in the script itself.

**Spotify** — needed for Spotify art/artist lookups:
1. Go to [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Create an app (any name/description, set redirect URI to `http://localhost`)
3. Copy the **Client ID** and **Client Secret** into the GUI and hit Save

**Last.fm** — optional, improves coverage for less mainstream tracks:
1. Go to [last.fm/api/account/create](https://www.last.fm/api/account/create)
2. Copy the API key into the GUI and hit Save

---

## Example

A file named:

```
Glass Animals - A Tear In Space (Airlock) ｜ Official Video [9-LI98Q-rsg].mp3
```

Gets renamed to:

```
Glass Animals - A Tear In Space (Airlock).mp3
```

With tags written:
- **Artist:** Glass Animals
- **Title:** A Tear In Space (Airlock)
- **Album art:** fetched and embedded automatically

---

## Notes

- Files are processed in place — back up your music folder first if you want to be safe
- Album art is only fetched if not already present in the file
- If a filename has no ` - ` separator, the whole name is used as the search term
- The Duplicates scanner supports: `.mp3 .flac .m4a .aac .ogg .opus .wav .wma .ape .mpc .wv .aiff`
