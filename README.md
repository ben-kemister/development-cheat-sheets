# development-cheat-sheets

A GitHub pages site which captures language syntax, tools, and ideas to help develop code quicker and better. 

## How it works
This site uses Jekyll to parse the files in this GitHub repo an produce a static website which can be hosted by GitHub pages.

## Running Locally
You can host this site on your computer by using Jekyll, and using the command:
`bundle exec jekyll serve`.

### Developer mode

To get the browser to automatically reload when the content changes, use the command:
`bundle exec jekyll serve --watch --config _config.yml,_development.yml`
