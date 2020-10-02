# Using image: linuxserver/code-server
Their image and docker command guidance worked. Was able to open coder server home page using exposed url/port.

* ref: https://hub.docker.com/r/linuxserver/code-server
* Adaptation of the run-in-docker example from above page:
```
$ docker run -dt -p 8443:8443 \
-v "$HOME/coderapp/config:/config" \
-e PUID=1001 -e PGID=100 -e TZ=America/Los_Angeles \
linuxserver/code-server
0a64bae936f5f625cc99a8f680d55992c8f0e179592e4e3960e57236e0c56daa

9d266976c7b6        linuxserver/code-server              "/init"             8 seconds ago       Up 7 seconds        0.0.0.0:8443->8443/tcp                     silly_allen

```
Container start up log output :
```
coder@hackberry:~/coderapp/config> docker logs 0a64bae936f5
[s6-init] making user provided files available at /var/run/s6/etc...exited 0.
[s6-init] ensuring user provided files have correct perms...exited 0.
[fix-attrs.d] applying ownership & permissions fixes...
[fix-attrs.d] done.
[cont-init.d] executing container initialization scripts...
[cont-init.d] 01-envfile: executing...
[cont-init.d] 01-envfile: exited 0.
[cont-init.d] 10-adduser: executing...

-------------------------------------
          _         ()
         | |  ___   _    __
         | | / __| | |  /  \
         | | \__ \ | | | () |
         |_| |___/ |_|  \__/


Brought to you by linuxserver.io
-------------------------------------

To support LSIO projects visit:
https://www.linuxserver.io/donate/
-------------------------------------
GID/UID
-------------------------------------

User uid:    1001
User gid:    100
-------------------------------------

[cont-init.d] 10-adduser: exited 0.
[cont-init.d] 30-config: executing...
[cont-init.d] 30-config: exited 0.
[cont-init.d] 99-custom-scripts: executing...
[custom-init] no custom files found exiting...
[cont-init.d] 99-custom-scripts: exited 0.
[cont-init.d] done.
[services.d] starting services
[services.d] done.
starting with no password
[2020-09-29T04:40:36.023Z] info  Using config file ~/.config/code-server/config.yaml
[2020-09-29T04:40:36.742Z] info  Using user-data-dir ~/data
[2020-09-29T04:40:36.754Z] info  code-server 3.5.0 de41646fc402b968ca6d555fdf2da7de9554d28a
[2020-09-29T04:40:36.765Z] info  HTTP server listening on http://0.0.0.0:8443
[2020-09-29T04:40:36.766Z] info    - No authentication
[2020-09-29T04:40:36.766Z] info    - Not serving HTTPS
coder@hackberry:~>
```
