---
sidebar_current: "kickoff"
---

# Getting started

## Installation

We're going to install the stable release from source. Check the `develop
branch` for the bleeding edge version.

Alternatively, you can use [docker](https://www.docker.io) and be up and
running quicker : [instructions here](/articles/getting-started/docker.html).

```console
$ (sudo) apt-get install -y git-core python-dev g++ make gfortran
$ git clone --depth 1 https://github.com/hackliff/intuition.git
$ # It's safer to install these packages first
$ pip install --use-mirrors cython numpy
$ cd intuition && (sudo) python setup.py install
```

That's it, test your installation with :

```console
$ intuition --help
```

However you only got the backbone of your application.

Intuition wires 4 primitives to build up the system : A data source generates
events, processed by the algorithm, that can optionnaly use a portfolio manager
to compute assets allocation. They are configured through a Context, while
third party services use environment variables (take a look in
`config/local.env`).

This might seem a lot of work but fortunately, I and contributors have
[already coded a bunch](https://github.com/hackliff/insights).

You can [learn more here](/articles/insights) about these building blocks
but for now we're gonna be lazy and just use them as is.

First, we need to install Insights, the project hosting contributed modules.

```console
$ (sudo) apt-get install -y libssl-dev libreadline-dev
$ git clone --depth 1 --branch develop https://github.com/hackliff/insights.git
$ cd insights && (sudo) python setup.py install
```
[Check out there](/articles/insights) to install powerful extra features.

Now let's get serious and run our first backtest.


## Baby steps

First save the configuration below as, for example, `first-backtest.yml`. It
should be self-explanatory but you can [learn more about configuring Intuition
here](/articles/api/contexts.html).

```yml
# We will have access to 5 stocks from the french index CAC 40
universe: stocks:paris:cac40,5
start: 2013-02-10
end: 2013-11-10

# Build your strategy here, combining modules
modules:
  # This one triggers buy and sell signals randomly
  algorithm: insights.algorithms.dummy.Random
  # This manager simply allocate a constant number of stocks
  manager: insights.managers.linear.Constant
  # The data will be fetched from Yahoo finance
  backtest: insights.sources.backtest.yahoo.YahooPrices

# We can tweak the 'Constant' portfolio manager
manager:
  buy_amount: 200
  cash: 10000
```

Then shoot *intuition*, loading our configuration file thanks to the
`FileContext` module. The `--context` flag takes an argument that follows url
format, and more specifically: `<module>://<address>/<path/to/config>?<params=values>`.

```shell
$ export LANG=fr_FR.UTF-8  # Not necessarily set in environments like containers
$ export LOG=info          # Log level, 'warning' by default
$ intuition \
    --id noob \  # Useful to identify your results later
    --showlog \  # Print logs on stdout instead of a file
    --bot \      # allow the portfolio to process orders on its own
    --context insights.contexts.file.FileContext://localhost/first-backtest.yaml
```

## Going further

By now you should be ready to use *intuition*. Next steps ?

* Learn more [how to use intuition from the command line](/articles/intuition)
* Discover [available modules](/articles/insights)
* Read [how to craft your owns](/articles/api)




-------
##### last modified on: March 23, 2013
-------
