# music_cleanup.py

Cleans up MP3 files downloaded from YouTube Music via yt-dlp. It renames files, sets artist/title tags, and fetches album art automatically from multiple sources.

## What it does

- **Skips non-songs** — any file over 13 minutes is ignored (catches full concert videos etc.)
- **Cleans filenames** — removes YouTube junk like `| Official Video [abc123xyz]` from the filename
- **Sets tags** — reads `Artist - Title` from the filename and writes the ID3 artist and title tags
- **Fetches album art** — tries Deezer, Last.fm, iTunes, then MusicBrainz in order. Works even if the filename has no artist (e.g. `24K Magic.mp3`). Skips files that already have art embedded.

## Requirements

Install dependencies:

```bash
pip install mutagen requests PyQt5 --break-system-packages
```

tkinter is not required — the GUI uses PyQt5 which is KDE-native.

## Usage

**With GUI (recommended):**

```bash
python3 music_cleanup.py
```

A window will appear letting you toggle which operations to run, enter your Last.fm API key, and pick your music folder. Hit **Run Cleanup** and a Konsole terminal opens showing live progress.

**Or pass the folder directly:**

```bash
python3 music_cleanup.py /path/to/your/music
```

## GUI options

All three options are on by default and can be toggled before running:

- **Clean filenames** — strips YouTube junk from the filename
- **Set ID3 tags** — writes artist and title tags from the filename
- **Fetch album art** — searches all sources and embeds the best result

## Last.fm API key

A default key is baked in. You can replace it in the GUI and hit **Save** — it gets stored in `~/.music_cleanup_config.json` and loaded automatically next time.

To get your own free key: [last.fm/api/account/create](https://www.last.fm/api/account/create)

## Art sources (in order)

1. **Deezer** — no key needed, best coverage
2. **Last.fm** — free API key, great for less mainstream tracks
3. **iTunes** — broad coverage, free
4. **MusicBrainz** — open database fallback

## Example

A file named:

```
Glass Animals - A Tear In Space (Airlock) ｜ Official Video [9-LI98Q-rsg].mp3
```

Gets renamed to:

```
Glass Animals - A Tear In Space (Airlock).mp3
```

With tags set:
- **Artist:** Glass Animals
- **Title:** A Tear In Space (Airlock)
- **Album art:** fetched and embedded automatically

## fix_apic.py

If you ran an older version of the script, your album art may have been saved with the wrong internal key (`APIC:Cover` instead of `APIC:`) which causes Elisa and some other players to not display it. Run this once to fix all existing files:

```bash
python3 fix_apic.py /path/to/your/music
```

Then clear the Elisa cache:

```bash
rm -rf ~/.cache/elisa ~/.cache/thumbnails
```

## Notes

- Files are processed in place — make a backup of your music folder first if you want to be safe
- Album art is only fetched if not already present in the file
- If a filename has no ` - ` separator, the whole name is used as the search term
