# odd-deployment

This gem demonstrates an oddity I noticed when bundling locally with and without the `--deployment` option.

Note that the setup of my gem isn't quite standard (see section below), so maybe this is expected.

## The Behavior

If I run a normal `bundle install` I get this list of gems:

```
~/source/odd-deployment:bundle install
Resolving dependencies...
Using rake 10.5.0
Using bundler 1.12.5
Using little-plugger 1.1.4
Using multi_json 1.12.1
Using minitest 5.9.0
Using zookeeper 1.4.11
Using logging 1.8.2
Using zk 1.9.4
Using nope 0.2.0 from source at `.`
Bundle complete! 4 Gemfile dependencies, 9 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed.
```

However, if I run `bundle install --deployment` from a clean checkout I get a shorter list:

```
~/source/odd-deployment:bundle install --deployment
Fetching gem metadata from https://rubygems.org/
Fetching version metadata from https://rubygems.org/
Installing rake 10.5.0
Installing minitest 5.9.0
Using bundler 1.12.5
Bundle complete! 4 Gemfile dependencies, 3 gems now installed.
Bundled gems are installed into ./vendor/bundle.
```

Note that because of the version we're not getting our local gem `from source`, nor any of its dependencies.

## What's Weird in the Setup
We use [papers](https://github.com/newrelic/papers) to do license validation tests against our gems. Given that, I found it useful to provide a `Gemfile.lock` so we wouldn't have versions drifting in the test environment. This is non-standard for a gem, and is likely involved in the problem.

The other oddity is that I've bumped the local `VERSION` constant for the gem, but haven't updated the `Gemfile.lock` yet. Obviously not the state things should be in longer term, but in another case I did check something in without updating and saw test failures because of missing dependenciesi... not what I'd expect.

## What Did I Expect?
I was surprised by the different behavior when passing or not passing `--deployment`. I can buy that bundler can't proceed because of the version mismatch, but maybe it could give a warning or error if it's excluding a local gem in this fashion?
