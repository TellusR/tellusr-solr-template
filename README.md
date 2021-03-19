# Tellusr Solr Template

## Introduction

This is a solr template for new projects that wants to use [TellusR](https://www.tellusr.com/). It uses the the [tellusr/demo](https://hub.docker.com/repository/docker/tellusr/demo) docker image. This is built on top of the official [solr](https://hub.docker.com/repository/docker/tellusr/demo) docker image but with TellusR configuration and extensions prepackaged. It includes a script that will help you quickly get started.

TellusR consists of three main components. A control dashboard (tellusr/central), a natural language service (tellusr/nlp) and integration plugin for solr (tellusr-integrator-*.jar). Together they add advanced features and usability to solr, which you can read about at the [TellusR](https://www.tellusr.com/) site. Developer documentation is available at the [Documentation site](http://docs.tellusr.com/).


## Prerequisites

Before you start you should make sure that docker is installed and running on your machine. How to do this vary between computers, and you should consult the [docker documentation](https://docs.docker.com/get-docker/) about how to do this on your own machine. We recommend a sudoless install.

The next step is to clone this git repository which includes a script `bin/ttb` which will help to test and develop your schema. This should work on Linux and Mac.

```git clone https://github.com/tellusr/tellusr-solr-template.git```


## Configure the Project

Move into the root directory of the repository. Here you will find a default project template in the file `tellusr.env`:

```
PROJECT=default
SOLR_PORT=8983
SOLR_HEAP=4096m
...
```

You should open this file in an editor and change the project name. If you already have another instance of solr running on port 8983 you might want to change solr port as well. Leave the rest as it is for now.


## Create a new collection

To create a config with a blank template you can run:

`bin/ttb create <collection name>`

You should replace <collection name> with the name of your collection. This will create new config files, based on the example files shipped with solr, with TellusR preconfigured for a collection of that name. You will find the newly created files in the subdirectory `configset/<collection name>/conf/*`.





