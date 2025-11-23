# super-duper-octo-umbrella
an octopus umbrella.... 
modular semi-automated drm-media pipeline. designed for workflows where:
* only specific scenes matter (not entire episodes)
* subtitles needs to be retained
* translations are needed because the preferred language doesn't exist
* future-proofing for personal archives or community preservation.
produces portable, watermarked and archive-ready mp4 files.

command extraction (CDM+manifest) > batch download > manual scene segmentation > subtitle cleaning > subtitle translation > final mastering: burn-in, optional watermarking, optional compression


## technical overview
### cdm & command extraction
* load `WidevineProxy2` ([link](https://github.com/DevLARLEY/WidevineProxy2)) via `chrome-extension://<EXTENSION-ID>/panel/panel.html`
* open DevTools in Chrome (⌘+⌥+I)
* trigger content requests by manually opening target episodes
* execute a custom DOM-scraping JavaScript snippet in Console to:
  * parse `.log-container` elements
  * extract wv-generated `N_m3u8DL-RE` license + manifest commands
  * infer episode numbers via RegEx (`episode-(\d+)-`)
  * copy formatted batch commands to clipboard
### command prep
* paste clipboard output into a `commands.txt` file
* (format: `Episode {n}: N_m3u8DL-RE ...`)
### batch download
* run `.py` script which:
  * parses `commands.txt`
  * adds download metadata, save dir, filename, 1080p vid stream, audio and subtitle streams
  * executes sequential `N_m3u8DL-RE` ([link](https://github.com/nilaoda/N_m3u8DL-RE), also install [FFmpeg](https://www.ffmpeg.org/) if you haven't already) downloads into output dir 
### scene segmentation
* manual segmentation of `.mkv` files in LosslessCut ([link](https://github.com/mifi/lossless-cut))
* export cut scene `.mkv` into target processing folder
### subtitles [extraction, cleaning, translation]
* `extract_subs.command` 
  * `ffmpeg -map 0:s:0` to dump first subtitle stream --> `.srt`
* `clean_subs.command`
  * strip `<c.*>` color tags via `sed -E 's/<c\.[a-zA-Z]+>//g'`
* `run_translate_srt.command`
  * auto-provisions Python venv (if missing)
  * runs `translate_srt.py` which is a [Gemini API SRT Translator CLI wrapper](https://github.com/MaKTaiL/gemini-srt-translator), translates to English `.srt`
### mastering [burn-in, compress, watermark]
* `sub_720p_wm.command`
* for each `.mkv` with matching translated `.srt`:
  * resize to 720p
  * burn subtitles (via `force_style`)
  * apply rgba watermark overlay
  * re-encode `libx264` + AAC into a finalized `.mp4` in `/done`
 
  # NOTICE
  THIS TOOLKIT DOES **NOT** PROVIDE ACCESS TO MEDIA, KEYS, OR DECRYPTION MATERIAL. IT ONLY AUTOMATES THE LOCAL HANDLING OF CONTENT THE USER IS ALREADY AUTHORIZED TO VIEW.  THIS TOOLKIT ONLY **ORCHESTRATES** THIRD-PARTY TOOLS.

super-duper-octo-umbrella does not:
• circumvent access to copyrighted content
• provide decryption keys or DRM components
• distribute or facilitate sharing of media files

This project only automates handling of media
that users are already lawfully authorized to view.

All copyrights remain with their respective owners.
No responsibility is assumed for misuse or redistribution.

**Users are fully responsible for compliance with
content licenses, DRM terms, and applicable laws.**
