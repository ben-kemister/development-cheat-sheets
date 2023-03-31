# development-cheat-sheets

A GitHub pages site which captures language syntax, tools, and ideas to help develop code quicker and better. 

## How it works
This site uses Jekyll to parse the files in this GitHub repo and produce a static website which can be hosted by GitHub pages.

## GitHub pages


## Running Locally

### Requirements

* Ruby
* Jekyll

You can host this site on your computer by using Jekyll, and using the command:
`bundle exec jekyll serve`.

### Docker

When running in Docker it looks like the gems are installed here: `/usr/gem/gems/`

#### Build the site (_site folder)

docker run --rm --volume="$PWD:/srv/jekyll" -it jekyll/jekyll:$JEKYLL_VERSION jekyll build

For Windows:
`docker run --rm --volume="$($PWD):/srv/jekyll" -it jekyll/jekyll:4.2.2 jekyll build`

Use:
`docker run --rm --volume="%CD%:/srv/jekyll" -it jekyll/jekyll sh -c "chown -R jekyll /usr/gem/ && jekyll new development-cheat-sheets" && cd development-cheat-sheets`


#### Serve the site

`docker run --rm --volume="$($PWD):/srv/jekyll" -p 4000:4000 -it jekyll/jekyll:4.2.2 jekyll serve --watch --drafts`


### Developer mode

To get the browser to:
* automatically reload when the content changes, and
* show the draft pages in `_drafts`
use the command:

`bundle exec jekyll serve --watch --drafts --config _config.yml,_development.yml`
