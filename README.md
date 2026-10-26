# wale.oyediran.me

Personal blog, built with [Jekyll](https://jekyllrb.com) (based on the [Hyde](https://github.com/poole/hyde) theme) and hosted on GitHub Pages from the `master` branch.

## Run locally

Needs Ruby 3.x (system Ruby on macOS is too old; use `chruby`/`rbenv`/Homebrew Ruby).

```sh
bundle install
bundle exec jekyll serve --drafts --livereload   # http://localhost:4000
```

`--drafts` includes posts in `_drafts/`. Drop it to see exactly what will be published.

## Writing a post

1. Start in `_drafts/my-post.md` (no date needed). Front matter defaults (`layout: post`, author) come from `_config.yml`.
2. When ready, move it to `_posts/YYYY-MM-DD-my-post.md` and push.

### Media includes

```liquid
{% include figure.html src="/images/folder/photo.jpg" alt="..." caption="..." %}            <!-- add class="wide" to bleed wider -->
{% include gallery.html images="/images/a.jpg,/images/b.jpg,/images/c.jpg" caption="..." %}
{% include video.html src="/videos/clip.mp4" poster="/images/poster.jpg" caption="..." %}   <!-- silent autoplay loop, like a GIF -->
{% include youtube.html id="VIDEO_ID" start=10 end=13 caption="..." %}
{% include strava.html id="ACTIVITY_ID" %}
{% include race-stats.html %}                                                              <!-- reads `race:` from front matter -->
```

### Preparing media

Phone photos are huge. Resize before committing (macOS built-in):

```sh
sips -Z 2000 -s formatOptions 80 images/folder/*.jpg
```

Trim a short clip (e.g. 3 s starting at 1:23) and make it web-friendly:

```sh
ffmpeg -ss 00:01:23 -i input.mov -t 3 -an -vf "scale=-2:720" \
  -c:v libx264 -crf 23 -preset veryfast -pix_fmt yuv420p -movflags +faststart videos/clip.mp4
ffmpeg -ss 1 -i videos/clip.mp4 -frames:v 1 images/folder/clip-poster.jpg   # poster frame
```

## License

Theme: MIT (see `LICENSE.md`). Post content © Oyewale Oyediran.
