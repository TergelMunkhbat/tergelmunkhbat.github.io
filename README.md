# Tergel Munkhbat's Website (based on Joel Jang's template)

## Updates guide
Change one of the files in `_data`, unless you are changing the look of the website.

Test changes locally with:
```bash
jekyll serve --livereload
```

Visit http://127.0.0.1:4000 to preview your changes.

## Deployment
This site is deployed via GitHub Pages. Simply push to the `master` branch to deploy:
```bash
git add .
git commit -m "Your commit message"
git push origin master
```

## Site Features
- **Context-Aware Headers**: Blog posts display a slim sticky navbar, while the homepage shows a full hero header
- **Responsive Navigation**: Mobile-friendly hamburger menu with CSS-only implementation (no JavaScript required)
- **Multilingual Blog**: Support for English, Korean, and Mongolian posts
- **Progressive Enhancement**: All features work without JavaScript


## External Libraries
- Framework: [Jekyll](http://jekyllrb.com/)
- CSS
  - [Skeleton](http://getskeleton.com) - Responsive CSS framework
  - [Skeleton Tabs](https://github.com/nathancahill/skeleton-tabs) - Tab navigation
  - [Timeline](https://codepen.io/NilsWe/pen/FemfK) - Experience timeline
  - [Font Awesome](http://fontawesome.io/) - General icons
  - [Academicons](https://jpswalsh.github.io/academicons/) - Academic icons (Google Scholar, Semantic Scholar)
- JS
  - [jQuery (3.1.1)](https://jquery.com/) - DOM manipulation and events
