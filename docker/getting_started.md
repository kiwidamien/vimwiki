# Simple Docker Commands

Mostly notes for myself, not going through downloading etc.

## Getting a list of running docker containers
```bash
docker ps
```

A slightly longer, but maybe easier to remember command, is `docker container ls`

## Attaching to container

```bash
docker container attach <NAME>
```
where <NAME> is the name from `docker container ls`

To get back out, you should be able to use <ctrl-p> <ctrl-q>
This doesn't work for me when attaching to the webserver for Flask/FastAPI.

This can be useful for seeing the log, but what I probably want is to either execute a command
on the shell of that running container, or to launch a new bash shell on the container.

To do that, run in interactive mode:
```bash
docker container exec -i -t <NAME> /bin/bash
```
or other commands (e.g. looking for a file)
```bash
docker container exec -i -t <NAME> ls <DIR>
```


