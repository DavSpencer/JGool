# 📥 JGool — DL

A GitHub Actions workflow that downloads files from URLs directly into your repository and generates ready-to-use download links in `Links.md`.

Supports YouTube, Twitch, Reddit, Vimeo, SoundCloud, PornHub, Bunkr, direct file links, and magnet/torrent links.

---

## ⚙️ Setup

### 1. Set repository permissions

Go to **Settings → Actions → General → Workflow permissions** and set it to **Read and write permissions**.

### 2. (Optional) Add cookies for restricted content

If you need to download age-restricted or login-required content, add your cookies as repository secrets:

| Secret name | Used for |
|---|---|
| `YOUTUBE_COOKIES` | YouTube age-restricted / member videos |
| `PH_COOKIES` | PornHub login-required videos |

To export cookies, use a browser extension like [Get cookies.txt LOCALLY](https://github.com/kairi003/Get-cookies.txt-LOCALLY) and paste the full content as the secret value.

---

## 🚀 How to Run

1. Go to your repository on GitHub
2. Click the **Actions** tab
3. Select **JGool - DL** from the left sidebar
4. Click **Run workflow**
5. Fill in the options and click the green **Run workflow** button

After the job finishes, downloaded files are saved to the `dl/` folder and direct download links are added to `Links.md`.

---

## 📋 Options

### 🔗 URLs *(required)*

Enter one or more URLs separated by **commas or spaces**.

**To rename a file**, add a custom name in parentheses right after the URL:

```
https://example.com/file.apk (My App)
```

Multiple URLs — all formats are valid:

```
https://example.com/File.zip (My File) https://example.com/Game.apk (My Game)
https://example.com/App.apk (My App), https://example.com/Movie.mp4 (My Movie)
https://example.com/Photo.jpg https://example.com/Game.apk
```

> The file extension is always preserved automatically. If subtitles are selected, the language tag is appended after your custom name — e.g. `My Video [EN].mp4`.

---

### 🎬 Video Quality

Applies to video sources (YouTube, Twitch, Vimeo, etc.).

| Option | Description |
|---|---|
| `1080p MP4` | Up to 1080p, MP4 container |
| `720p MP4` | Up to 720p, MP4 container |
| `480p MP4` | Up to 480p, MP4 container |
| `360p MP4` | Up to 360p, MP4 container |
| `1080p WebM` | Up to 1080p, WebM container (better compression, smaller size) |
| `720p WebM` | Up to 720p, WebM container |
| `480p WebM` | Up to 480p, WebM container |
| `360p WebM` | Up to 360p, WebM container |
| `Audio MP3` | Audio only, extracted as MP3 |
| `Best` | Highest available quality, MP4 container |

> Quality selection only applies to yt-dlp sources (EX. YouTube, PH...). Direct links, Bunkr, and magnet downloads are not affected.

---

### 💬 Subtitles

Optionally embed subtitles into the downloaded video. The selected language is automatically added as a tag to the filename.

| Option | Filename tag |
|---|---|
| `None` | *(no tag)* |
| `English (Manual)` | `[EN]` |
| `English (Auto-Generated)` | `[EN]` |
| `English (Manual + Auto)` | `[EN]` |
| `Persian (Manual)` | `[FA]` |
| `Persian (Auto-Generated)` | `[FA]` |
| `Persian (Manual + Auto)` | `[FA]` |
| `Persian + English` | `[EN-FA]` |

**Examples:**

```
My Video [EN].mp4
My Video [FA].mp4
My Video [EN-FA].mp4
My App [FA].apk          ← custom name + subtitle tag
```

> **MP4 / MKV** — subtitles are embedded directly into the file.  
> **WebM** — subtitles cannot be embedded; saved as a `.vtt` file alongside the video instead.  
> If the requested subtitle language is unavailable, it is silently skipped — the video still downloads normally.

---

### 🗜️ ZIP Mode

Controls how files are packaged before being pushed to the repository.

| Option | Behavior |
|---|---|
| `Auto (ZIP only if >95MB)` | Files under 95MB are saved raw. Files over 95MB are zipped automatically and split into 95MB parts. Setting a password always triggers a zip. |
| `Individual ZIP (one zip per file)` | If you add multiple links, Every file gets its own `.zip`, split if over 95MB. |
| `Bundle ZIP (all files in one zip)` | If you add multiple links, All downloaded files are packed into a single `.zip`, split into parts if the total size exceeds 95MB. |

**Bundle ZIP naming** — the zip is named after the first downloaded file plus a timestamp:

```
MyApp_20260502_1430.zip              ← single file
MyApp+2more_20260502_1430.zip        ← multiple files
```

**Split parts** are named `.zip`, `.z01`, `.z02`, etc. You need all parts to extract the archive. Most extraction tools (7-Zip, WinRAR) handle this automatically when you open the `.zip` part.

---

### 🔑 ZIP Password *(optional)*

Set a password to protect the ZIP archive. Leave empty for no encryption.

---

## 🌐 Supported Sources

| Source | Handler |
|---|---|
| YouTube | yt-dlp (+ optional cookies) |
| Twitch, Reddit, Vimeo, SoundCloud | yt-dlp |
| PornHub | yt-dlp (+ optional cookies) |
| Bunkr (`bunkr.si`, `bunkr.cr`, `bunkr.rip`, etc.) | Custom Python script |
| Direct HTTP/HTTPS links | aria2c |
| Magnet / Torrent (`magnet:?xt=...`) | aria2c ⚠️ | 

---

## ⚠️ Magnet / Torrent Warning

Magnet links are supported but have important limitations:

- GitHub Actions has a **6-hour hard job limit**. Large or slow torrents **will be killed mid-download** with no output saved.
- Works reliably only for **small (under ~1GB) and well-seeded** torrents.
- Some public trackers may block GitHub's runner IP addresses.

Paste the full `magnet:?xt=urn:btih:...` string directly into the URL field.

---

## 📄 Links.md

After every run, `Links.md` is automatically updated with direct download links. The most recent run always appears at the top. Each file entry shows:

- An icon based on file type — 📱 APK · 🎬 Video · 🎵 Audio · 🗜️ ZIP · 📥 Other
- The filename as a **clickable direct download link**
- The file size

---

## 📁 Repository Structure

```
your-repo/
├── .github/
│   └── workflows/
│       └── gigili.yml      ← workflow file
├── dl/                     ← all downloaded files land here
└── Links.md                ← auto-generated direct download links
```

---

## 📝 Notes

- Timestamps in `Links.md` use **Iran Standard Time (Asia/Tehran)**.
- If two workflow runs finish at the same time and both try to push, the second one automatically rebases — no conflicts or manual fixes needed.
- `.vtt` subtitle sidecar files are automatically deleted before zipping and are never pushed to the repository.
