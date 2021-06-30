# Sage Bionetworks Docker image for RStudio

[![GitHub Release](https://img.shields.io/github/release/Sage-Bionetworks/docker-rstudio.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://github.com/Sage-Bionetworks/docker-rstudio/releases)
[![GitHub CI](https://img.shields.io/github/workflow/status/Sage-Bionetworks/docker-rstudio/CI.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://github.com/Sage-Bionetworks/docker-rstudio)
[![GitHub License](https://img.shields.io/github/license/Sage-Bionetworks/docker-rstudio.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://github.com/Sage-Bionetworks/docker-rstudio/blob/main/LICENSE)
[![Docker Pulls](https://img.shields.io/docker/pulls/sagebionetworks/rstudio.svg?color=94398d&labelColor=555555&logoColor=ffffff&style=for-the-badge&label=pulls&logo=docker)](https://hub.docker.com/r/sagebionetworks/rstudio)

## Introduction

The motivation for this project is to encourage the use of portable development
environments in research and engineering. The environment should be intuitive to
use so that anyone can deploy it and reproduce your results - even you six
months from now!

This project provides a portable development environment that enables you to use
R and Python together. The Docker image [sagebionetworks/rstudio] offered by
this project is based on the image [rocker/rstudio].

Features:

- Use sentitive information like credentials without specifying them in your
  notebooks, hence preventing the risk of publishing this information publicly.
- Create and manage Conda environments (Miniconda) using the R library
  [reticulate] to run and/or develop Python programs that require different
  version of Python or packages.
- Render Rmd notebook to HTML using the Docker image provided in this project,
  e.g. to generate HTML notebooks in GitHub workflows before publishing them to
  GitHub Pages.
- Benefit from regular updates of the image [sagebionetworks/rstudio] which will
  bring the latest versions of R/RStudio and other dependencies (Miniconda, R
  and Python packages).
- You only need the Docker Engine on your system to develop code in R and Python
  (see [Requirements](#requirements)).

This image includes the following common Sage Bionetworks software:

- R libraries
  - [sagethemes]: Sage-branded plot themes.
- Python packages
  - [challengeutils]: Synapse challenge utility functions.
  - [synapseclient]: Programmatic interface to Synapse services for Python.

All packages:

- R (see [renv.lock]).
- Python (see [conda/sage-bionetworks/environment.yml]).

### Requirements

- [Docker Engine] >=19.03.0

## Example notebooks

The example notebooks below are rendered to HTML and published to GitHub Pages
by the [CI/CD workflow of this repository](.github/workflows/ci.yml).

Rmd Notebook | Description | HTML Notebook
-------- | ----------- | -------------
[notebook.Rmd](notebooks/notebook.Rmd)         | Default RStudio notebook.                                | [![HTML notebook](https://img.shields.io/badge/latest-blue.svg?color=1283c3&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://sage-bionetworks.github.io/docker-rstudio/latest/notebooks/notebook.html)
[r-and-python.Rmd](notebooks/r-and-python.Rmd) | Shows how to use R and Python together.                  | [![HTML notebook](https://img.shields.io/badge/latest-blue.svg?color=1283c3&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://sage-bionetworks.github.io/docker-rstudio/latest/notebooks/r-and-python.html)
[sagethemes.Rmd](notebooks/sagethemes.Rmd)     | Example notebook provided by the R library [sagethemes]. | [![HTML notebook](https://img.shields.io/badge/latest-blue.svg?color=1283c3&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://sage-bionetworks.github.io/docker-rstudio/latest/notebooks/sagethemes.html)
[synapse.Rmd](notebooks/synapse.Rmd)           | Shows how to interact with Synapse API.                  | [![HTML notebook](https://img.shields.io/badge/latest-blue.svg?color=1283c3&labelColor=555555&logoColor=ffffff&style=for-the-badge&logo=github)](https://sage-bionetworks.github.io/docker-rstudio/latest/notebooks/synapse.html)

> Important: Please make sure when you write your own notebooks that no
> sensitive information ends up being publicly available. Please check with the
> information security officer of your organization to confirm that the approach
> described here can be applied to your use case.

## Usage

1. Clone this GitHub repository.
2. Create and edit the configuration file. You can initially start RStudio using
   this configuration as-is.

       cp .env.example .env

3. Start RStudio. Add the option `-d` or `--detach` to run in the background.

       docker compose up

RStudio is now available at http://localhost. On the login page, enter the
default username (`rstudio`) and the password specified in `.env`.

To stop RStudio, enter `Ctrl+C` followed by `docker compose down`.  If running
in detached mode, you will only need to enter `docker compose down`.

## How to use this repository

You can use the image [sagebionetworks/rstudio] as-is to start an instance of
RStudio and develop tools that interact with Sage Bionetworks services, e.g.
Synapse.

If you want to create a portable development environment, start by creating a
new GitHub repository from [this template]. You can then customize your
environment by specifying the R and Python packages to include with your image.
Finally, edit the the GitHub workflow [.github/workflows/ci.yml] to indicates
the Docker repository where the image should be pushed (see Section
[Versioning](#Versioning)).

Example projects that use this repository / image:

- [Sage-Bionetworks-Challenges/challenge-analysis] (work in progress)

## Configuration

The configuration of the development environment is defined in the file `.env`.
This file contains environment variables that are set when the environment
starts.

For security reason, a user session in RStudio does not see all the environment
variables of the system. However, the variables defined in `.env` with a name
that starts with `APP_` will be made visible to the user session via the
creation of the file `.Renviron`.

```r
> variables <- names(s <- Sys.getenv())
> variables[startsWith(variables, "APP_")]
[1] "APP_BAR" "APP_FOO"
```

## Manage R and Python dependencies

### R

In RStudio, use the following options to add and update libraries:

- `Tools` > `Install Packages...`
- `Tools` > `Check for Package Updates...`

Run the command `renv::snapshot(type="all")` to update the file `renv.lock`,
which is used in `Dockerfile` to install the required R libraries.

### Python

See the content of the folder `conda` for an example of how to define a conda
environment. The packages to add to this environment must be added to the file
`requirements.txt`. The creation of one or more Conda environments can be
specified in `Dockerfile`.

## Setting Synapse credentials

Set the environment variables `SYNAPSE_TOKEN` to the value of one of your
Synapse Personal Access Tokens. If this variable is set, it will be used to
create the configuration file `~/.synapseConfig` when the container starts.

## Using Conda

This Docker image comes with [Miniconda] installed (see below) and an example
Conda environment named `sage-bionetworks`. This environment includes packages
used to interact with the collaborative platform [Synapse] developed by [Sage
Bionetworks].

### From the terminal

Attach to the RStudio container (here assuming that `rstudio` is the name of the
container). For better safety, it is recommended to work as a non-root user. You
can then list the environments available, activate an existing environment or
create a new one.

```console
$ docker exec -it rstudio bash
container # su yourusername
container $ conda env list
container $ conda activate sage-bionetworks
```

### From RStudio

The R code below lists the environment available before activating the existing
environment named `sage-bionetworks`.

```console
> library(reticulate)
> conda_list()
    name                              python
1 miniconda           /opt/miniconda/bin/python
2      sage-bionetworks /opt/miniconda/envs/sage-bionetworks/bin/python
> use_condaenv("sage-bionetworks", required = TRUE)
```

## Setting user / group identifiers

When using Docker volumes, permissions issues can arise between the host OS and
the container. You can avoid these issues by letting RStudio know the User ID
(UID) and Group ID (GID) it should use when creating and editting files so that
these IDs match yours, which you can get using the command `id`:

```console
$ id
uid=1000(kelsey) gid=1000(kelsey) groups=1000(kelsey)
```

In this example, we would set `USERID=1000` and `GROUPID=1000`.

## Giving the user root permissions

Set the environment variable `ROOT=TRUE` (default is `FALSE`).

## Accessing logs

```console
docker logs --follow rstudio
```

## Generating an HTML notebook

This Docker image provides the command `render` that generates an HTML or PDF
notebook from an R notebook (*.Rmd*). Run the command below from the host to
mount the directory `$(pwd)/notebooks` where the R notebook is and generate the
HTML notebook that will be saved to the same directory with the extension
`.nb.html`.

```console
docker run --rm \
    --env-file .env \
    -v $(pwd):/project \
    sagebionetworks/rstudio:4.1.0 \
    render /project/notebooks/*.Rmd
```

## Versioning

### GitHub tags

This repository uses [semantic versioning] to track the releases of this
project. This repository uses "non-moving" GitHub tags, that is, a tag will
always point to the same git commit once it has been created.

### Docker and GitHub Pages tags

The artifact published by this repository are HTML notebooks published to GitHub
Pages and the Docker image [sagebionetworks/rstudio]. The tags of these
artifacts are aligned with the GitHub tags.

The table below describes the image tags available.

| Tag name                        | Description                                            | Moving
|---------------------------------|--------------------------------------------------------|-------
| `latest`                        | Latest stable release.                                 | Yes
| `edge`                          | Lastest commit made to the default branch.             | Yes
| `edge-<sha>`                    | Same as above with the reference to the git commit.    | No
| `<major>.<minor>.<patch>`       | Latest stable patch release `<major>.<minor>.<patch>`. | No

You should avoid using a moving tag like `latest` when deploying containers in
production, because this makes it hard to track which version of the image is
running and hard to roll back.

<!-- ## Contributing

Thinking about contributing to this project? Get started by reading our
[Contributor Guide](CONTRIBUTING.md). -->

## License

[Apache License 2.0]

<!-- Links -->

[rocker/rstudio]: https://hub.docker.com/r/rocker/rstudio
[RStudio image]: https://hub.docker.com/r/rocker/rstudio
[Miniconda]: https://docs.conda.io/en/latest/miniconda.html
[synapse]: https://www.synapse.org/
[Synapse Python client]: https://pypi.org/project/synapseclient/
[GitHub Dependabot]: https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/enabling-and-disabling-version-updates
[semantic versioning]: https://semver.org/
[rocker/rstudio]: https://hub.docker.com/r/rocker/rstudio
[Apache License 2.0]: https://github.com/Sage-Bionetworks/docker-rstudio/blob/main/LICENSE
[Sage Bionetworks]: https://sagebionetworks.org
[reticulate]: https://rstudio.github.io/reticulate
[sagebionetworks/rstudio]: https://hub.docker.com/repository/docker/sagebionetworks/rstudio
[sagethemes]: https://github.com/Sage-Bionetworks/sagethemes
[challengeutils]: https://github.com/Sage-Bionetworks/challengeutils
[synapseclient]: https://github.com/Sage-Bionetworks/synapsePythonClient
[renv.lock]: renv.lock
[Dockerfile]: Dockerfile
[conda/sage-bionetworks/environment.yml]: conda/sage-bionetworks/environment.yml
[this template]: https://github.com/nlpsandbox/nlpsandbox.io/generate
[.github/workflows/ci.yml]: .github/workflows/ci.yml
[Sage-Bionetworks-Challenges/challenge-analysis]: https://github.com/Sage-Bionetworks-Challenges/challenge-analysis
[Sage-Bionetworks/docker-rstudio]: https://github.com/Sage-Bionetworks/docker-rstudio
[Docker Engine]: https://docs.docker.com/engine/install/
[Docker Compose]: https://docs.docker.com/compose/install/
