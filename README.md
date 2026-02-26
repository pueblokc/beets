# beets Web UI — Enhanced by KCCS

> Spotify-like web interface for the beets music library manager — album grid, full-text search, metadata editing, and a dark aesthetic.

![Library View](docs/screenshots/library.png)

## What's New in This Fork

- **Album grid with cover art** — genre-color gradients with instant hover reveal
- **Track list view** — click any album to drill into tracks with format/bitrate info
- **Full-text search** — SQLite FTS5, sub-250ms results across title/artist/genre/label
- **Sidebar filters** — filter by genre, format (FLAC/MP3/etc), or artist in one click
- **Stats dashboard** — total albums, tracks, artists, and total playtime at a glance
- **Metadata editing** — PATCH any album's title, artist, year, genre, or label via modal
- **HTML5 audio player bar** — transport controls, progress scrubbing, volume
- **Demo mode** — 55 real albums seeded across 20+ genres, no beets installation required
- **Dark Spotify aesthetic** — fully self-hosted, zero build tools

## Quick Start

### Demo mode (no beets required)

```bash
# From the beets repo root:
pip install fastapi uvicorn

# Run the server
python -m uvicorn beets_web.app:app --port 8508 --reload

# Open in browser
open http://localhost:8508
```

The server seeds a SQLite demo library on first boot (`beets_web/demo_library.db`).

### With your real beets library

```bash
BEETS_DB=~/.config/beets/library.db \
  python -m uvicorn beets_web.app:app --port 8508
```

Full beets-native integration (live query via `beet ls`, tag writing, reimport) is planned for v0.2.

## Screenshots

| Screenshot | Description |
|---|---|
| ![Library](docs/screenshots/library.png) | Album grid with genre-color gradients, sidebar filters, and stats dashboard |

## Features

- **Album grid** — cover art via genre-color gradients, instant hover reveal
- **Track list** — click any album to drill into its tracks with format/bitrate info
- **Full-text search** — SQLite FTS5, sub-250ms results across title/artist/genre/label
- **Sidebar filters** — filter by genre, format (FLAC/MP3/etc), or artist in one click
- **Stats dashboard** — total albums, tracks, artists, and total playtime at a glance
- **Metadata editing** — PATCH any album's title, artist, year, genre, or label via modal
- **HTML5 audio player bar** — transport controls, progress scrubbing, volume (demo sim)
- **Dark theme** — Spotify-dark aesthetic, fully self-hosted
- **Demo mode** — 55 real albums seeded across 20+ genres, no beets installation required

### Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `Space` | Play/pause |
| `Escape` | Close modal / back to grid |
| `/` or `Ctrl+F` | Focus search |

## API Reference

All endpoints return JSON. Base URL: `http://localhost:8508`

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/stats` | Library statistics |
| `GET` | `/api/albums` | List/search albums (`?q=`, `?genre=`, `?artist=`, `?format=`, `?sort=`, `?limit=`, `?offset=`) |
| `GET` | `/api/albums/{id}` | Album detail with full track list |
| `PATCH` | `/api/albums/{id}` | Update album metadata |
| `DELETE` | `/api/albums/{id}` | Remove album from library |
| `GET` | `/api/tracks` | List/search tracks (`?q=`) |
| `PATCH` | `/api/tracks/{id}` | Update track metadata |
| `GET` | `/api/genres` | Genre list with counts |
| `GET` | `/api/artists` | Artist list with album counts |
| `GET` | `/api/formats` | Format list with counts |

## Docker

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY beets_web/ beets_web/
RUN pip install --no-cache-dir fastapi uvicorn
EXPOSE 8508
CMD ["uvicorn", "beets_web.app:app", "--host", "0.0.0.0", "--port", "8508"]
```

```bash
docker build -t beets-web .
docker run -p 8508:8508 beets-web
```

## Architecture

```
beets_web/
  __init__.py        # Package version
  app.py             # FastAPI app — API + demo data seeding
  demo_library.db    # Auto-generated SQLite (55 demo albums)
  static/
    index.html       # Single-file SPA — no build step, no Node
```

- **Backend:** FastAPI + SQLite (FTS5 for search)
- **Frontend:** Vanilla JS SPA — zero dependencies, zero build tooling
- **Demo data:** 55 classic albums across 20+ genres, seeded on first run
- **Port:** 8508

## Roadmap

- [ ] Live beets database integration (read from `~/.config/beets/library.db`)
- [ ] Album art from MusicBrainz / Cover Art Archive
- [ ] Inline tag editing (write back via `beet modify`)
- [ ] Duplicate detection view
- [ ] Import queue UI (`beet import` wrapper)
- [ ] Playlist management
- [ ] Mobile-responsive layout

## Why not the existing `beets web` plugin?

The built-in `beet web` plugin is read-only, built on Bottle, and hasn't seen significant updates in years. This project aims to be a full replacement with:

- Modern UI framework (FastAPI)
- Write operations (edit, delete)
- Full-text search
- Real-time stats
- A UI people actually want to use

---

<details>
<summary>Original Project README (reStructuredText)</summary>

```rst
.. image:: https://img.shields.io/pypi/v/beets.svg
    :target: https://pypi.python.org/pypi/beets

.. image:: https://img.shields.io/codecov/c/github/beetbox/beets.svg
    :target: https://codecov.io/github/beetbox/beets

.. image:: https://img.shields.io/github/actions/workflow/status/beetbox/beets/ci.yaml
    :target: https://github.com/beetbox/beets/actions

.. image:: https://repology.org/badge/tiny-repos/beets.svg
    :target: https://repology.org/project/beets/versions

beets
=====

Beets is the media library management system for obsessive music geeks.

The purpose of beets is to get your music collection right once and for all. It
catalogs your collection, automatically improving its metadata as it goes. It
then provides a suite of tools for manipulating and accessing your music.

Here's an example of beets' brainy tag corrector doing its thing:

::

    $ beet import ~/music/ladytron
    Tagging:
        Ladytron - Witching Hour
    (Similarity: 98.4%)
     * Last One Standing      -> The Last One Standing
     * Beauty                 -> Beauty*2
     * White Light Generation -> Whitelightgenerator
     * All the Way            -> All the Way...

Because beets is designed as a library, it can do almost anything you can
imagine for your music collection. Via plugins, beets becomes a panacea:

- Fetch or calculate all the metadata you could possibly need: album art,
  lyrics, genres, tempos, ReplayGain levels, or acoustic fingerprints.
- Get metadata from MusicBrainz, Discogs, and Beatport. Or guess metadata
  using songs' filenames or their acoustic fingerprints.
- Transcode audio to any format you like.
- Check your library for duplicate tracks and albums or for albums that are
  missing tracks.
- Clean up crufty tags left behind by other, less-awesome tools.
- Embed and extract album art from files' metadata.
- Browse your music library graphically through a Web browser and play it in any
  browser that supports HTML5 Audio.
- Analyze music files' metadata from the command line.
- Listen to your library with a music player that speaks the MPD protocol.

If beets doesn't do what you want yet, writing your own plugin is shockingly
simple if you know a little Python.

Install
-------

You can install beets by typing ``pip install beets`` or directly from Github.
Beets has also been packaged in the software repositories of several distributions.
Check out the Getting Started guide for more information.

Contribute
----------

Thank you for considering contributing to beets! Whether you're a programmer
or not, you should be able to find all the info you need at CONTRIBUTING.rst.

Read More
---------

Learn more about beets at its Web site: https://beets.io/
Follow @b33ts on Mastodon for news and updates.

Contact
-------

- Encountered a bug you'd like to report? Check out our issue tracker!
- Need help/support or would like to start a discussion? Check out GitHub Discussions!

Authors
-------

Beets is by Adrian Sampson with a supporting cast of thousands.
```

</details>

---

Developed by **[KCCS](https://kccsonline.com)** — [kccsonline.com](https://kccsonline.com)
