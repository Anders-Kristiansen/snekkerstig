# Snekkerstig AS — Static Site

Static website for **Snekkerstig AS** – Totalentreprenør ([snekkerstig.com](http://www.snekkerstig.com)).

Built with [Jekyll](https://jekyllrb.com/) and deployed to GitHub Pages via GitHub Actions.

## Site Structure

```
_config.yml           # Jekyll configuration
_layouts/             # HTML layouts
  default.html        #   base layout (head + nav + footer)
  page.html           #   standard page (extends default)
  post.html           #   blog post (extends default)
_includes/
  header.html         # sticky navigation bar
  footer.html         # site footer + hamburger JS
_posts/
  2016-10-01-blogginnlegg-tittel.md
assets/css/main.css   # all styles (brand colours, responsive)
index.html            # home page (hero + services + CTA)
om-oss.md             # about us
tjenester.md          # services
portefolje.md         # portfolio
galleri.md            # gallery
kontakt.md            # contact form
```

## Pages

| URL | File |
|-----|------|
| `/` | `index.html` |
| `/om-oss/` | `om-oss.md` |
| `/tjenester/` | `tjenester.md` |
| `/portefolje/` | `portefolje.md` |
| `/galleri/` | `galleri.md` |
| `/kontakt/` | `kontakt.md` |
| `/blogginnlegg-tittel/` | `_posts/2016-10-01-blogginnlegg-tittel.md` |

## Local Development

```bash
gem install bundler jekyll
bundle init
bundle add jekyll jekyll-feed jekyll-seo-tag
bundle exec jekyll serve
```

Then open <http://localhost:4000>.

> **Note:** Add `_site/` and `.jekyll-cache/` to `.gitignore` before running Jekyll locally.

## Deployment

Pushes to `main` automatically trigger the `.github/workflows/jekyll.yml` workflow,
which builds with `actions/jekyll-build-pages` and deploys via `actions/deploy-pages`.

Enable GitHub Pages in **Settings → Pages → Source: GitHub Actions**.

## Contact Form

The contact form on `/kontakt/` targets [Formspree.io](https://formspree.io).
Replace `YOUR_FORM_ID` in `kontakt.md` with your actual Formspree form ID:

```html
action="https://formspree.io/f/YOUR_FORM_ID"
```

## Brand Colours

| Token | Hex | Usage |
|-------|-----|-------|
| Green | `#81d742` | Buttons, accents |
| Heading | `#353535` | Page headings |
| Body | `#252525` | Body text |
| Link | `#51748e` | Hyperlinks |
| Footer | `#999999` | Footer text |
