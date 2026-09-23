# WebTV

**Your Web. Your Channels. Your TV.**

WebTV is a free, open-source, browser-based IPTV client with a premium Smart-TV-style interface. It plays **your own legally authorized** M3U / M3U8 playlists directly in the browser — no installs, no plugins, no server required.

WebTV does not include, host, link to, or endorse any TV channels, streams, or playlists. It is a generic player: what you watch is entirely up to the playlists you choose to add.

---

## 1. What WebTV is

WebTV is a single-page web app built with React (via CDN, no build step) that:

- Parses M3U/M3U8 playlists (`#EXTINF`, `tvg-id`, `tvg-name`, `tvg-logo`, `group-title`)
- Plays HLS (`.m3u8`) streams using [hls.js](https://github.com/video-dev/hls.js), falling back to a browser's native HLS support (e.g. Safari) when available
- Organizes channels automatically by category, with search, favorites, and recently-watched
- Offers a custom video player with a premium overlay, keyboard/remote-friendly controls, and optional XMLTV EPG (program guide) support
- Keeps all of your data (favorites, recents, playlists, preferences) in your browser's `localStorage` — nothing is sent to any server WebTV controls

It is intended as a clean foundation for anyone who wants a self-hosted or local IPTV front-end for content they already have the rights to access (self-produced streams, licensed IPTV subscriptions with M3U export, over-the-air re-streams you operate, test streams, etc.).

## 2. How to run it

No build tools, package manager, or Node.js are required.

**Option A — open directly:**

1. Download or clone this repository.
2. Open `index.html` in a modern desktop browser (Chrome, Edge, Firefox, Safari).

Some browsers restrict `fetch()` for local files loaded via `file://`. If adding a playlist by URL doesn't work when opened directly, use Option B.

**Option B — simple static server (recommended):**

```bash
# Python 3
python3 -m http.server 8080

# or Node's http-server, if you have it
npx http-server -p 8080
```

Then visit `http://localhost:8080` in your browser.

There is nothing to `npm install` — `index.html` loads React, ReactDOM, Babel Standalone, and hls.js from public CDNs at runtime.

## 3. How to add an M3U playlist

Click **Add Playlist** in the header. You can add a source in three ways:

| Method | What it does |
|---|---|
| **M3U File** | Upload a local `.m3u`/`.m3u8` file from your device. Its contents are parsed and stored in your browser. |
| **M3U URL** | Provide a URL to a hosted M3U/M3U8 playlist. WebTV fetches and parses it; you can refresh it later to pick up changes. |
| **Direct M3U8** | Provide a single direct stream URL (skips the playlist format) if you just want to watch one channel. |

Channels are grouped automatically by their `group-title` attribute (or "Uncategorized" if missing), and are searchable by name or category. You can add multiple playlists; their channels are merged into one library.

## 4. How M3U8 playback works

```
M3U Playlist → Parse channels → User selects channel → Get M3U8 URL → HLS.js / native HTML5 video → Playback
```

- If the browser supports HLS natively (Safari/iOS), WebTV uses a plain `<video>` element.
- Otherwise, WebTV initializes [hls.js](https://github.com/video-dev/hls.js) to demux and feed the stream into a standard `<video>` element.
- Adaptive bitrate levels reported by hls.js are exposed as a **Quality** menu in the player (Auto or a specific rendition).
- Playback, network, and media errors are caught and shown as a friendly on-screen message with a **Reconnect** button — raw JavaScript errors are never shown to the user.

## 5. Browser & CORS limitations

Because WebTV runs entirely in the browser, it is subject to standard web security rules:

- **CORS**: If a playlist server or stream server does not send permissive CORS headers, the browser may block `fetch()` (for playlist URLs) or block cross-origin `<video>` playback. This is a server-side restriction, not a WebTV bug — WebTV will show a friendly "may not allow browser playback" message rather than crash.
- **Mixed content**: If WebTV is served over `https://`, it generally cannot load `http://` streams. Serve WebTV over `http://` (e.g. locally) or use `https://` streams to avoid this.
- **DRM-protected streams** are not supported; WebTV only handles plain HLS.
- Large playlists (thousands of channels) are supported, but browsers still have to hold the parsed list and any playlist text in memory/`localStorage`, so extremely large files may be slow to add on low-end devices.

## 6. Legal responsibility of playlist owners

**WebTV is a generic media player, not a content provider.** It ships with zero pre-loaded channels, playlists, or stream URLs.

By adding a playlist or stream URL, **you** confirm that:

- You own the content, or
- You have a valid license/subscription that explicitly permits playback in a third-party or self-hosted browser player, or
- The stream is otherwise lawful for you to access and play (e.g. public-domain or freely licensed content).

The maintainers and contributors of WebTV are not responsible for how the software is used. Do not use WebTV to access unauthorized or pirated streams; doing so may violate copyright law and the terms of your content provider.

## 7. How developers can contribute

Contributions are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines and [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) for community expectations.

Project structure:

```
index.html    Loads CDN dependencies and mounts the app
style.css     Design tokens + all component styles
app.js        All React components (JSX, transpiled in-browser by Babel Standalone)
README.md
LICENSE
```

The codebase intentionally avoids TypeScript, bundlers, and heavy frameworks so it stays approachable — it can be opened and edited by anyone comfortable with plain HTML/CSS/JS. As the project grows, `app.js` can be split into ES modules (e.g. `components/ChannelCard.js`) without introducing a build step, using native `<script type="module">` imports.

### Roadmap ideas

- Split `app.js` into ES modules
- Optional PWA/offline shell
- Drag-to-reorder favorites
- Multi-language UI strings
- Chromecast / AirPlay casting affordances

## License

MIT — see [LICENSE](LICENSE).
