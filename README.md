# development-cheat-sheets

A GitHub pages site which captures language syntax, tools, and ideas to help develop code quicker and better. 

## How it works

This site uses [Hugo](https://gohugo.io/) to parse the files in this GitHub repo and produce a static website which can 
be run locally or hosted on GitHub pages.

## GitHub pages

This repo contain a github workflow in `.github/worflows/hugo.yaml`.

When a commit is pushed to the branch listed in `.github/worflows/hugo.yaml` github will start the worflow.
The results of the workflow can be seen on the [GitHub Actions page](https://github.com/ben-kemister/development-cheat-sheets/actions).

Note that the [github-pages environment](https://github.com/ben-kemister/development-cheat-sheets/settings/environments) 
contains a list of the branches which are allowed to deploy to the _github-pages_ environment.

## Development

### Natively via installed tools

Required tools:
* git
* Go
* Hugo

Once all of these tools are installed you can serve the site using:
```powershell
hugo serve -D --disableFastRender
```

### via Docker

A [Hugo Docker image](https://hub.docker.com/r/klakegg/hugo) is available

> **WARNING**: Changes to the pages will not be detected when running Hugo in Docker on a **Windows host**.
>
> Do to a limitation with [Docker on Windows](https://forums.docker.com/t/file-system-watch-does-not-work-with-mounted-volumes/12038/25)
> which prevents Windows file systems changes from propagating into the linux file system within the container,
> Hugo will be unable to detect file systems changes and rebuild the site.

To serve the site using docker run:

```powershell
docker run --rm -it --name hugo `
       -v "$($pwd)\quickstart:/src" `
       -p 1313:1313 `
       [$DOCKER_REG/]klakegg/hugo:<VERSION>-ext server -D
```
