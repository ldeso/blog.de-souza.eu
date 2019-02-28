---
title: 'Hugo Flex: a Lightweight Hugo Theme Leveraging CSS Flexbox'
date: '2019-02-28T18:00:58+01:00'
tags:
  - hugo
  - html
  - css
type: posts
---
Hugo Flex is the theme made for this blog, for use with the static site generator [Hugo](https://gohugo.io/).

As it is my first project with HTML and CSS, it was made with simplicity in mind and does not rely on any framework or external dependency.

Whilst developing this theme I made some opinionated choices: I decided not to go with the "[Reset CSS](https://meyerweb.com/eric/tools/css/reset/)" approach and let the browsers decide for most basic styles (font sizes, paragraphs margins...); I went with the same [system font stack](https://css-tricks.com/snippets/css/system-font-stack/) as used by the Medium and Wordpress websites; I included all the CSS and Javascript code directly on every page; last, I did not care about old browsers and I used CSS3 to make the theme responsive. This allowed me not to include any javascript in the theme (except for optional [shortcodes](https://gohugo.io/content-management/shortcodes/)).

Theme repository: https://github.com/de-souza/hugo-flex

Official page: https://themes.gohugo.io/hugo-flex/

# Hugo Flex

A lightweight Hugo theme leveraging CSS Flexbox


## Features

- Flexbox-based responsive layout
- Full posts in RSS feed
- Themed RSS feed
- No framework
- No javascript
- 100% speed score on PageSpeed Insight

Optional features:

- CSS and JS can be [dynamically embedded](#dynamically-embedded-css-and-js) with shortcodes
- Built-in shortcodes:
  - On-click Soundcloud player
  - Netlify contact form


## Installation

From the website root:

```bash
git submodule add https://github.com/de-souza/hugo-flex.git themes/hugo-flex
```

The theme must be set in the website config:

```bash
echo 'theme: hugo-flex' >> config.yaml
```


## Updating

From the website root:

```bash
git submodule update --remote --rebase
```

## Configuration

Configuration options may be copied and modified from the theme defaults:

```yaml
params:
  color: teal  # Any color in CSS syntax
  footer: Except where otherwise noted, content on this site is licensed under
    a <a href="http://creativecommons.org/licenses/by/4.0/" rel="license">
    Creative Commons Attribution 4.0 International License</a>.
  rss: To subscribe to this RSS feed, copy its address and paste it into your
    favorite feed reader.

menu:
  nav:
  - name: About
    url: /about/
    weight: 1
  - name: Posts
    url: /post/
    weight: 2
  - name: Tags
    url: /tags/
    weight: 3
  - name: Categories
    url: /categories/
    weight: 4
  - name: RSS
    url: /index.xml
    weight: 5
```


## Built-In Shortcodes

### Netlify Contact Form

A contact form working with the built-in Netlify form handling service is inserted with the shortcode:

```
{{</* contact */>}}
```

A custom success page URL may be given as a parameter:

```
{{</* contact "/success/" */>}}
```

### On-Click Soundcloud Player

An on-click Soundcloud Player is inserted with the shortcode:

```
{{</* soundcloud 123456789 */>}}
```

The parameter is a track ID and can be extracted from the "embed" sharing menu on the track webpage.


## Dynamically embedded CSS and JS

Additional CSS and JS may be embedded with shortcodes. The code must be saved as a partial (e.g. `website/layout/partials/myscript.js`) and its filename must be added to the page-wide `.Scratch` variable. From within the shortcode template:

```html
{{ slice "myscript.js" | .Page.Scratch.Add "js" }}
```

As an example here is the shortcode template for the on-click Soundcloud player:

```html
{{ slice "soundcloud.css" | .Page.Scratch.Add "css" }}
{{ slice "soundcloud.js" | .Page.Scratch.Add "js" }}
<div class="soundcloud" data-id="{{ .Get 0 }}"></div>
```

## License

This theme is licensed under the [Apache License 2.0](https://github.com/de-souza/hugo-flex/blob/master/LICENSE).
