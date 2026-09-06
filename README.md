# modus-catalog-images

Hand-made artwork for Meta product catalogs, served over GitHub Pages.

**This repository is public.** That is the point: Meta's crawler has to fetch
every image anonymously, and a private repo's raw URLs need a token it cannot
send. Put nothing here that is not already meant to run as an ad.

## Why it exists

Feed rows normally carry a poster scraped from the client's own site. That works,
but the scraped image is whatever the site's `og:image` happens to be, which is
often a wide banner that Meta crops badly, or a logo that is under Meta's minimum
size. For films worth a designed asset, the artwork lives here and the feed
points at it.

Hand editing the image anywhere downstream does not survive: the Google Sheet tab
is rewritten from scratch by every run, and Commerce Manager is overwritten by
the next scheduled fetch. The override map in the feed repo is the only durable
place, and this repo is where the file it points at is served from.

## Layout

    <client-slug>/<film-id>.jpg

The file name is the FILM id (`group_id` in the feed, shipped as
`custom_number_2`), not a screening id, so one file covers every screening of
that film. Name a file after a screening id only for a genuine one-off.

Served at:

    https://mishka-modus.github.io/modus-catalog-images/<client-slug>/<file>

## Image spec

| requirement | value |
|---|---|
| aspect | square |
| recommended | 1080 x 1080 |
| Meta minimum | 500 x 500 |
| max file size | 8 MB |
| formats | JPEG, PNG |

Catalog ads are cropped again from the centre for 4:5 and 9:16 placements, so
keep the logo and any text inside the middle of the frame.

## `.nojekyll` is load-bearing

GitHub Pages runs Jekyll by default, and Jekyll silently drops every file and
folder whose name starts with an underscore. A poster named `_teaser.jpg` would
404 with a green build and no error anywhere. The empty `.nojekyll` file at the
repo root turns Jekyll off and publishes the tree verbatim. Do not delete it.

## `cinematheque-tlv/_selftest.png`

A 1080 x 1080 checkerboard, kept on purpose. It is the one file whose correct
appearance is known, so it answers "is Pages serving, and is it serving image
bytes" without touching a client asset. Its underscore name is also the standing
proof that `.nojekyll` is still doing its job.

## Wiring an image up

1. Commit the file here and push. Pages redeploys in about a minute.
2. Add the URL to `clients/image_overrides_<client-slug>.json` in the feed repo,
   under `by_feature`, keyed by the film id.
3. The next run validates the URL, uses it, and falls back to the scraped poster
   if it does not answer.

Related: `mishka-modus/cinematheque-meta-feed`.
