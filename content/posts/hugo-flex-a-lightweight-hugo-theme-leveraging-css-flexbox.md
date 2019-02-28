---
title: 'Hugo Flex: a Lightweight Hugo Theme Leveraging CSS Flexbox'
date: '2019-02-28T18:00:58+01:00'
tags:
  - hugo
  - html
  - css
type: posts
---
Hugo Flex is the theme of this blog, used with the static site generator [Hugo](https://gohugo.io/).

As it is my first project with HTML and CSS, it was made with simplicity in mind and does not rely on any framework or external dependency.

While developing this theme I made some opinionated choices: I decided not to go with the "[Reset CSS](https://meyerweb.com/eric/tools/css/reset/)" approach and let the browsers decide on most of the basic styles (font sizes, paragraphs margins...); I went with the same [system font stack](https://css-tricks.com/snippets/css/system-font-stack/) used by Medium and Wordpress websites; I included all the CSS and Javascript code directly in each page; last, I did not care about compatibility with older browsers and used CSS3 to make the theme responsive. This allowed me not to include any javascript in the theme (except for optional [shortcodes](https://gohugo.io/content-management/shortcodes/)).

Theme repository: https://github.com/de-souza/hugo-flex

Official page: https://themes.gohugo.io/hugo-flex/
