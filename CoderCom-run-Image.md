# Using image: codercom/code-server:latest

Coder docker command example from https://github.com/cdr/code-server/blob/v3.5.0/doc/install.md ...
```
# This will start a code-server container and expose it at http://127.0.0.1:8080.
# It will also mount your current directory into the container as `/home/coder/project`
# and forward your UID/GID so that all file system operations occur as your user outside
# the container.
#
# Your $HOME/.config is mounted at $HOME/.config within the container to ensure you can
# easily access/modify your code-server config in $HOME/.config/code-server/config.json
# outside the container.
mkdir -p ~/.config
docker run -it -p 127.0.0.1:8080:8080 \
  -v "$HOME/.config:/home/coder/.config" \
  -v "$PWD:/home/coder/project" \
  -u "$(id -u):$(id -g)" \
  codercom/code-server:latest
```

After some trial/error this form of the run command worked:
```
docker run -dt -p 8080:8080 \
-v "$HOME/.config:/home/coder/.config" \
-v "$PWD:/home/coder/project" \
-u "1001:100" \
codercom/code-server:latest
7c7f39c61368ecb5f6a66ad9e61f2a57ec5760b50fc96753c3145cf301c58e27

coder@hackberry:~/.config> docker ps
CONTAINER ID        IMAGE                                COMMAND                  CREATED             STATUS              PORTS                                      NAMES
603603c598ee        codercom/code-server:latest          "/usr/bin/entrypoint…"   10 seconds ago      Up 8 seconds        0.0.0.0:8080->8080/tcp                     vigorous_grothendieck

coder@hackberry:~/.config/code-server> docker logs 603603c598ee
whoami: cannot find name for user ID 1001
[2020-09-30T03:23:30.345Z] info  Wrote default config file to ~/.config/code-server/config.yaml
[2020-09-30T03:23:30.350Z] info  Using config file ~/.config/code-server/config.yaml
[2020-09-30T03:23:31.058Z] info  Using user-data-dir ~/.local/share/code-server
[2020-09-30T03:23:31.071Z] info  code-server 3.5.0 de41646fc402b968ca6d555fdf2da7de9554d28a
[2020-09-30T03:23:31.082Z] info  HTTP server listening on http://0.0.0.0:8080
[2020-09-30T03:23:31.083Z] info      - Using password from ~/.config/code-server/config.yaml
[2020-09-30T03:23:31.083Z] info      - To disable use `--auth none`
[2020-09-30T03:23:31.083Z] info    - Not serving HTTPS
[2020-09-30T03:23:50.490Z] warn  discarding socket connection: not authenticated
[2020-09-30T03:23:50.629Z] warn  discarding socket connection: not authenticated
```
