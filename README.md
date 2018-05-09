# docker-diskho

> /ˈdɪsːkˌhuː/

Kitchen-sink docker image for CI jobs.

<img src="https://upload.wikimedia.org/wikipedia/commons/8/83/Chez_moi_010.jpg" alt="En diskho" height="300">

### Content

Based on `ubuntu-16.04`, and contains:
- Everything from [the stackage base image](https://github.com/fpco/stackage/blob/master/debian-bootstrap.sh), e.g.:
  - `nodejs` and `npm`
  - `R`
  - `java`
  - Erlang stuff
- `gcloud` (Google Cloud SDK) and related utils (e.g. `gsutil`, `kubectl`, etc.)
- `docker`
- `dhall`, and related utils (e.g. `dhall-json`, `dhall-yaml`, `dhall-text`)
- `envsubst`, and the other things in the `gettext` package
- some amount of python packages, e.g. `py-yaml` and `jinja`

### Docker tags

Available tags:
- `ksfmedia/diskho:latest`     - always the most recent Stack LTS
- `ksfmedia/diskho:lts-11`     - Stack LTS 11, latest of all the rest
- `ksfmedia/diskho:lts-11.XXX` - Stack LTS 11, every update of all the other software gets a version

#### TODO:
- [ ] Terraform
- [ ] Install a good amount of stack packages so we don't have to compile them all the time
