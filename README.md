# Reminder on how to use

## Create a new post:

1. add a new .md file under _posts, format is `yyyy-mm-dd-title`
2. add text as markdown, leave images and other static content under under /assets. Images specifically goes under `/assets/img/`
3. test locally if you like, by running `bundle exec jekyll serve`
4. push to *master* to publish

## Create a new page

Pages are added under `/_pages`. They should have a defined permlink, e.g. /About/. Otherwise the same process as with posts. Pages which should be added to global navigation are added in the `/_data/navigation.yml` file


## How to serve 

`bundle exec jekyll serve`
## Theme info 

- Theme is [Minimal Mistakes a Jekyll theme](https://mmistakes.github.io/minimal-mistakes)
- [Documentation](https://mmistakes.github.io/minimal-mistakes/docs/configuration/)

## General things to remember

[GitHub documentation](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/testing-your-github-pages-site-locally-with-jekyll) on Jekyll and testing locally. Revisit a couple of times a year, make sure github-pages gem is up-to-date. 

`bundle update github-pages`

Example: what to do after updating Gemfile
`bundle update`

Example: how to add a gem
`bundle add github-pages`

I created this using 
`bundle init`
`exec jekyll _3.9.0_ new . --force`
# add reference gem "github-pages", group: :jekyll_plugins
`bundle update`


