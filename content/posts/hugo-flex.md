---
title: Hugo Flex
date: '2019-02-28T18:00:00+01:00'
tags:
  - Hugo
  - HTML
  - CSS
type: posts
---
Today I released the theme I made for this blog, [Hugo Flex](https://themes.gohugo.io/hugo-flex/). It can be used with the static site generator [Hugo](https://gohugo.io/).

As it is my first project with HTML and CSS, it is made with simplicity in mind and does not rely on any framework or external dependency.

While developing this theme I made some opinionated choices:

* I decided not to go with the "[reset CSS](https://meyerweb.com/eric/tools/css/reset/)" approach and let the browsers decide on most of the basic styles (font sizes, paragraphs margins...);
* I went with the [system font stack](https://css-tricks.com/snippets/css/system-font-stack/) used by Medium and Wordpress websites;
* I included all the CSS directly in every page;
* Last, I did not care about compatibility with older browsers and used CSS3 to make the theme responsive. This allowed me not to include any Javascript in the theme (except for optional [shortcodes](https://gohugo.io/content-management/shortcodes/));

Feel free to use and modify this theme. Its repository can be found at: https://github.com/de-souza/hugo-flex
