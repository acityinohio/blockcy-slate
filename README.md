# BlockCypher's Docs

This repository contains all of [BlockCypher's](http://www.blockcypher.com) API documentation, presented with the help of [Slate](https://github.com/tripit/slate).

## Running BlockCypher's Docs Locally

We welcome Pull Requests to help us improve our documentation. If you're making edits, it's a good idea to run your fork of our docs locally before submitting a Pull Request; to do so, follow these instructions straight from [Slate.](https://github.com/tripit/slate)

To get started with Slate, please check out the [Getting Started](https://github.com/slatedocs/slate/wiki#getting-started)
section in our [wiki](https://github.com/slatedocs/slate/wiki).

You're going to need:

* **Linux or macOS** — Windows may work, but is unsupported.
* **Ruby, version 2.3.1 or newer**
* **Bundler** — If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.

### Getting Set Up

 1. Fork this repository on Github.
 2. Clone *your forked repository* (not our original one) to your hard drive with `git clone https://github.com/YOURUSERNAME/docs.git`
 3. `cd docs`
 4. Install all dependencies: `bundle install`
 5. Start the test server: `bundle exec middleman server`

```shell
# either run this to run locally
bundle install
bundle exec middleman server

# OR run this to run with vagrant
vagrant up

# OR run this to run with docker
docker build . -t slate:latest # this only needs to be run once
docker run -p 4567:4567 -v $(pwd)/source:/srv/slate/source slate:latest
```
You can now see the docs at <http://localhost:4567>.

### Building Static Docs

We build our docs locally then serve them from our own server. If you want to build them locally---instead of running them via `middleman server`, if you're curious about the html output---you can run this command:

```shell
bundle exec middleman build
```

This creates a `./build` directory, which contains all the generated HTML, CSS, Javascript, and assets.

## Found an error or bug?

Just [submit an issue](https://github.com/blockcypher/docs/issues). And, of course, feel free to submit Pull Requests with bug fixes or proposed changes.

## Special Thanks

- [Slate](https://github.com/tripit/slate)
