[![build status][251]][232] [![commit][255]][231] [![version:x86_64][256]][235] [![size:x86_64][257]][235]

## [Alpine-Quasar][234]
#### Container for Alpine Linux + Quasar Framework CLI
---

This [image][233] containerizes the [command line client][139] for
[Quasar Framework][138] and [VueJS][137] [CLI][136] along with
its [NPM][135] dependencies.

Based on [Alpine Linux][131] from my [alpine-vue][132] image with
the [s6][133] init system [overlayed][134] in it.

The image is tagged respectively for the following architectures,
* ~~**armhf**~~
* **x86_64** (retagged as the `latest` )

~~**armhf** builds have embedded binfmt_misc support and contain the~~
~~[qemu-user-static][105] binary that allows for running it also inside~~
~~an x64 environment that has it.~~

---
#### Get the Image
---

Pull the image for your architecture it's already available from
Docker Hub.

```
# make pull
docker pull woahbase/alpine-quasar:x86_64
```

---
#### Run
---

If you want to run images for other architectures, you will need
to have binfmt support configured for your machine. [**multiarch**][104],
has made it easy for us containing that into a docker container.

```
# make regbinfmt
docker run --rm --privileged multiarch/qemu-user-static:register --reset
```

Without the above, you can still run the image that is made for your
architecture, e.g for an x86_64 machine..

This images already has a user `alpine` configured to drop
privileges to the passed `PUID`/`PGID` which is ideal if its used
to run in non-root mode. That way you only need to specify the
values at runtime and pass the `-u alpine` if need be. (run `id`
in your terminal to see your own `PUID`/`PGID` values.)

Before you run..

* Mount the project directory (where `package.json` is) at
  `/home/alpine/project`. Mounts `PWD` by default.

* Quasar runs under the user `alpine`.

Running `make` gets a shell.

```
# make
docker run --rm -it \
  --name docker_quasar --hostname quasar \
  -e PGID=1000 -e PUID=1000 \
  -c 256 -m 512m \
  -v $PWD:/home/alpine/project \
  -v /etc/localtime:/etc/localtime:ro \
  -v /etc/hosts:/etc/hosts:ro \
  -p 8080:8080 \
  --entrypoint /bin/bash \
  woahbase/alpine-quasar:x86_64
```

The usual quasar stuff. e.g init projects with

```
# make init
docker run --rm -it \
  --name docker_quasar --hostname quasar \
  -e PGID=1000 -e PUID=1000 \
  -c 256 -m 512m \
  -v $PWD:/home/alpine/project \
  -v /etc/localtime:/etc/localtime:ro \
  -v /etc/hosts:/etc/hosts:ro \
  -p 8080:8080 \
  woahbase/alpine-quasar:x86_64 \
  init
```
run the dev server,

```
# make dev
docker run --rm -it \
  --name docker_quasar --hostname quasar \
  -e PGID=1000 -e PUID=1000 \
  -c 256 -m 512m \
  -v $PWD:/home/alpine/project \
  -v /etc/localtime:/etc/localtime:ro \
  -v /etc/hosts:/etc/hosts:ro \
  -p 8080:8080 \
  woahbase/alpine-quasar:x86_64 \
  dev -m pwa -t mat
```

build the project with

```
# make prod
docker run --rm -it \
  --name docker_quasar --hostname quasar \
  -e PGID=1000 -e PUID=1000 \
  -c 256 -m 512m \
  -v $PWD:/home/alpine/project \
  -v /etc/localtime:/etc/localtime:ro \
  -v /etc/hosts:/etc/hosts:ro \
  -p 8080:8080 \
  woahbase/alpine-quasar:x86_64 \
  build -m pwa -t mat
```

Checkout this [link][140] to get help on the CLI, or the
framework.

Stop the container with a timeout, (defaults to 2 seconds)

```
# make stop
docker stop -t 2 docker_quasar
```

Removes the container, (always better to stop it first and `-f`
only when needed most)

```
# make rm
docker rm -f docker_quasar
```

Restart the container with

```
# make restart
docker restart docker_quasar
```

---
#### Shell access
---

Get a shell inside a already running container,

```
# make shell
docker exec -it docker_quasar /bin/bash
```

set user or login as root,

```
# make rshell
docker exec -u root -it docker_quasar /bin/bash
```

To check logs of a running container in real time

```
# make logs
docker logs -f docker_quasar
```

---
### Development
---

If you have the repository access, you can clone and
build the image yourself for your own system, and can push after.

---
#### Setup
---

Before you clone the [repo][231], you must have [Git][101], [GNU make][102],
and [Docker][103] setup on the machine.

```
git clone https://github.com/woahbase/alpine-quasar
cd alpine-quasar
```
You can always skip installing **make** but you will have to
type the whole docker commands then instead of using the sweet
make targets.

---
#### Build
---

You need to have binfmt_misc configured in your system to be able
to build images for other architectures.

Otherwise to locally build the image for your system.
[`ARCH` defaults to `x86_64`, need to be explicit when building
for other architectures.]

```
# make ARCH=x86_64 build
# sets up binfmt if not x86_64
docker build --rm --compress --force-rm \
  --no-cache=true --pull \
  -f ./Dockerfile_x86_64 \
  --build-arg ARCH=x86_64 \
  --build-arg DOCKERSRC=alpine-vue \
  --build-arg PGID=1000 \
  --build-arg PUID=1000 \
  --build-arg USERNAME=woahbase \
  -t woahbase/alpine-quasar:x86_64 \
  .
```

To check if its working..

```
# make ARCH=x86_64 test
docker run --rm -it \
  --name docker_quasar --hostname quasar \
  -e PGID=1000 -e PUID=1000 \
  woahbase/alpine-quasar:x86_64 \
  --version
```

And finally, if you have push access,

```
# make ARCH=x86_64 push
docker push woahbase/alpine-quasar:x86_64
```

---
### Maintenance
---

Sources at [Github][106]. Built at [Travis-CI.org][107] (armhf / x64 builds). Images at [Docker hub][108]. Metadata at [Microbadger][109].

Maintained by [WOAHBase][204].

[101]: https://git-scm.com
[102]: https://www.gnu.org/software/make/
[103]: https://www.docker.com
[104]: https://hub.docker.com/r/multiarch/qemu-user-static/
[105]: https://github.com/multiarch/qemu-user-static/releases/
[106]: https://github.com/
[107]: https://travis-ci.org/
[108]: https://hub.docker.com/
[109]: https://microbadger.com/

[131]: https://alpinelinux.org/
[132]: https://hub.docker.com/r/woahbase/alpine-vue
[133]: https://skarnet.org/software/s6/
[134]: https://github.com/just-containers/s6-overlay
[135]: https://www.npmjs.com/
[136]: https://github.com/vuejs/vue-cli
[137]: https://vuejs.org/
[138]: http://quasar-framework.org/
[139]: https://github.com/quasarframework/quasar-cli
[140]: http://quasar-framework.org/guide/quasar-cli.html

[201]: https://github.com/woahbase
[202]: https://travis-ci.org/woahbase/
[203]: https://hub.docker.com/u/woahbase
[204]: https://woahbase.online/

[231]: https://github.com/woahbase/alpine-quasar
[232]: https://travis-ci.org/woahbase/alpine-quasar
[233]: https://hub.docker.com/r/woahbase/alpine-quasar
[234]: https://woahbase.online/#/images/alpine-quasar
[235]: https://microbadger.com/images/woahbase/alpine-quasar:x86_64

[251]: https://travis-ci.org/woahbase/alpine-quasar.svg?branch=master

[255]: https://images.microbadger.com/badges/commit/woahbase/alpine-quasar.svg

[256]: https://images.microbadger.com/badges/version/woahbase/alpine-quasar:x86_64.svg
[257]: https://images.microbadger.com/badges/image/woahbase/alpine-quasar:x86_64.svg
