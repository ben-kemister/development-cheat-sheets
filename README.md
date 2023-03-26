# development-cheat-sheets

A GitHub pages site which captures language syntax, tools, and ideas to help develop code quicker and better. 

## How it works
This site uses Jekyll to parse the files in this GitHub repo and produce a static website which can be hosted by GitHub pages.

## Running Locally

### Docker
Use:
`docker run --rm --volume="%CD%:/srv/jekyll" -it jekyll/jekyll sh -c "chown -R jekyll /usr/gem/ && jekyll new development-cheat-sheets" && cd development-cheat-sheets`


docker run --rm --volume="%CD%:/srv/jekyll" -it nexus.dev-space.duckdns.org:5000/jekyll/jekyll sh -c "chown -R jekyll /usr/gem/ && jekyll new development-cheat-sheets"

### Requirements

* Ruby
* Jekyll

You can host this site on your computer by using Jekyll, and using the command:
`bundle exec jekyll serve`.

### Developer mode

To get the browser to:
* automatically reload when the content changes, and
* show the draft pages in `_drafts`
use the command:

`bundle exec jekyll serve --watch --drafts --config _config.yml,_development.yml`
