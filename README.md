# Tellusr Solr Template

## Introduction

This is a Solr template for new projects that wants to use [TellusR](https://tellusr.com/). It uses the the [tellusr/search](https://hub.docker.com/r/tellusr/search) docker image. This is built on top of the official [Solr](https://hub.docker.com/_/solr) docker image but with TellusR configuration and extensions prepackaged. It includes a script that will help you get started quickly.

TellusR consists of three main components. A control dashboard (tellusr/central), a natural language service (tellusr/nlp) and integration plugin for Solr (tellusr-integrator-*.jar). Together they add advanced features and usability to Solr, which you can read about at the [TellusR](https://tellusr.com/) site. Developer documentation is available at the [Documentation site](https://docs.tellusr.com/).


## Prerequisites

Before you start you should make sure that docker is installed and running on your machine. How to do this vary between computers, and you should consult the [docker documentation](https://docs.docker.com/get-docker/) about how to do this on your own machine. We recommend a sudoless install.

The next step is to clone this git repository which includes a script `bin/ttb` which will help to test and develop your schema. This should work on Linux and Mac.

```
git clone https://github.com/tellusr/tellusr-solr-template.git
```

Or you can add it to an existing project repository by doing:

```
git remote add tellusr https://github.com/tellusr/tellusr-solr-template.git
git checkout --orphan template
git pull tellusr template
git checkout master
git merge template --allow-unrelated-histories
```

This will check out the tellusr-solr-template into a new branch `template` and merge it into your master branch.


## Configure the Project

Move into the root directory of the repository. Here you will find a default project template in the file `tellusr.env`:

```
PROJECT=default
SOLR_PORT=8983
SOLR_HEAP=4096m
```

You should open this file in an editor and change the project name. If you already have another instance of Solr running on port 8983 you might want to change Solr port as well. Leave the rest as it is for now.

The tellusr.env is in bash script format.


## Create a new collection

To create a configuration with a blank template you can run:

`bin/ttb create new_collection`

You should replace `new_collection` with the name you want for your collection. Avoid spaces and special characters. This will create new configuration files, based on the example files shipped with Solr, with TellusR preconfigured for a collection of that name. You will find the newly created files in the sub directory `configset/<collection name>/conf/*`. You can edit them there.


## Test your new collection

To test a core you can run:

```bin/ttb test```

This will start up an instance of Solr, migrate your schema (and test_data, if you have added any). You end up in a bash console which runs inside the docker container running Solr with tellusr. If you did not change the port number above, you can check the running Solr instance by going to:

[http://localhost:8983/](http://localhost:8983/)

You can exit the test with CTRL+D or by the command `exit` in the console, and restart the test by rerunning `bin/ttb test`.

## Creating a Set of Test Data

You can add a set of test data that will be auto imported when you run a test by putting a json file in a sub directory of `$PROJECT_HOME` (which is the directory where the tellusr.env resides) named test_data. This file should be called `${COLLECTION}_data.json` where `${COLLECTION}` is the name of the collection that the data should be imported into. If this is too big to commit into your project, you can keep a smaller set in a file named `${COLLECTION}_data_tiny.json`, which will be used when `${COLLECTION}_data.json` is not found.

If this does not fit your needs, you can write your own custom import of data by implementing a function `migrate_data` in tellusr.env


## Install TellusR central and nlp

Before installing the TellusR central and nlp you should head over to [https://tellusr.com/](https://tellusr.com/) and get a license key. A one month free trial and a free basic edition keys are available.

You can then download and start the tellusr/central and tellusr/nlp servers with:

```
bin/ttb tellusr_install_test_mode
```

When this is done you should open the dashboard of tellusr/central by going to [http://localhost:8989/](http://localhost:8989/).

The configuration files for the install will be located in a file `$HOME/.tellusr/tellusr.config`. You can stop and start your tellusr with a script located at `$HOME/.tellusr/bin/tellusr.sh`.

You can start and stop tellusr/central and tellusr/nlp with:

```
bin/ttb tellusr start
bin/ttb tellusr restart
bin/ttb tellusr stop
```


## Deploying a Solr Cluster to Prod

A typical production deployment may contain three Solr instances that communicate together via ZooKeeper. (It should always be an [odd number of instances](https://solr.apache.org/guide/8_8/setting-up-an-external-zookeeper-ensemble.html).) You then first need to modify the following lines `zoo.cfg` template included in `$PROJECT_HOME`:

```
server.1=localhost:2888:3888
server.2=localhost:2889:3889
server.3=localhost:2890:3890
```

`localhost` should be the IP address of the different servers. For convenience you should (if they run on different IPs), run them all on the same ports. We will in these instructions assume that you are running them on ports 2888 and 3888 as for `server.1` above.

Then you need to open the following ports between the servers in the firewall:

 * For Solr: 8983
 * For ZooKeeper: 2888, 3888, 9983
 * For TellusR: 8989

Now you are ready to run the installation scripts. On the server where you want to have TellusR installed you can run:

```
bin/ttb tellusr_install_prod_mode
```

On the servers where you want Solr to run, you should do:

```
bin/ttb install <zookeeper id>
bin/ttb prod_run
```

Where `<zookeeper id>` is a number between 1 and 3.

To import the your schema to the zookeeper cluster you should now run the following on *one of the servers*:

```
bin/ttb migrate
```

Later you can update the configuration files in all collections by running:

```
bin/ttb update_all
```
