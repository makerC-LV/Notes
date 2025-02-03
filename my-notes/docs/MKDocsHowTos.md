## MKDocs HowTos

MKDocs documentation is [here](https://www.mkdocs.org/getting-started)

- Deploying this site
   - Manually: run `mkdocs gh-deploy`
   - CI set up via `.github/workflows/ci.yml` (following [this](https://squidfunk.github.io/mkdocs-material/publishing-your-site/))

- Material for Mkdocs: Good theme and documentation [this link](https://squidfunk.github.io/mkdocs-material/getting-started/)

- Publish site on Github-pages: [this link](https://squidfunk.github.io/mkdocs-material/publishing-your-site/#with-github-actions-material-for-mkdocs)

- Make links downloadable vs viewable - [this SE post](https://stackoverflow.com/questions/76275641/mkdocs-how-to-attach-a-downloadable-file)
To `mkdocs.yml` add 
```
markdown_extensions:
  - attr_list
```

Then specify a downloadable file as `\[name\](URL){:download="filename"}`<br>
**NOTE:** As yet unable to make the browser display a text file, it always downloads, at least when previewing. Apparently this is specified by the [server](https://stackoverflow.com/questions/7267242/opening-files-in-browser-instead-of-downloading)
