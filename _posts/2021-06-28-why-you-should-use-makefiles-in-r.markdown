---
layout: post
title:  "Build automation in R: Why you should use Makefiles"
date:   2021-06-28 00:00:00 +0100
categories: R
---

Build automation in R is pretty much taken care of by the `devtools` package. We can very quickly build packages and run tests against them by running a few commands in the console. If you're more mature than that you'll probably run the same steps running in a CI pipeline somewhere (Travis, Azure Pipelines, etc) like so:
{% highlight yaml %}
steps:
- script: RScript -e "devtools::build('.')"
  displayName: "build R Package"
- script: RScript -e "devtools::test(.)"
  displayName: "test R package"
{% endhighlight %}
and this is usually good enough in most contexts as it does exactly what you need but software design wise *it's terrible*. What you've created is a CI/CD design that is tightly coupled to the problem-specific build tools(Rscript and devtools). As a result, we can't re-use this pipeline in other builds even though the high-level stages are likely to be the same: *build*, *test*, *release*, *deploy*.
 
To get around this, we can make use of GNU's Make as a language agnostic build tool which will provide us with a high-level interface to our build tools - in effect implementing the *dependency inversion principle* but for our build system.


# How does Make work?
Make is fairly simple in it's approach to builds in that it only rebuilds when an artefact when a dependency changes. For example, if A has a dependency on B  but B has a newer timestamp than A, then A is rebuilt. In the inverse, A has a newer timestamp than B then make assumes there has been no changes and doesn't waste time rebuilding A.

Given the above, all the targets and their dependencies are defined in a make file called 'Makefile' and running the targets can be run individually like `make A` or completely by calling `make`.


# Building your R package with Make
To kick off, we'll create a makefile that can create a build directory which will be initialised with a renv environment and all the necessary dependencies of the package.
{% highlight makefile %}
#We define some targets at the most abstract level, these are the targets which will be available to the user/CI system.
clean:
	rm -rf build

#Nice isolated environment that we can build in.
build/renv:
	mkdir -p build
	cd build && Rscript -e "renv::init(bare = TRUE, force = TRUE)"
	cd build && Rscript -e "renv::activate()"
	cd build && Rscript -e "install.packages('devtools')"

build/renv.lock: DESCRIPTION build/renv
	cd build && Rscript -e "devtools::install_deps('../', dependencies = TRUE)"
	cd build && Rscript -e "renv::snapshot(project = '../', lockfile = 'renv.lock')"
{% endhighlight %}
So far we've made use of a few different features of Make. We've created two targets to be built and the last target has a dependency on the `DESCRIPTION` file and the `build/renv` target. What these two dependencies gives us is the ability to only rebuild targets when the `DESCRIPTION` file changes or the renv directory has changed. We can even verify this by running make in the second instance:
{% highlight bash %}
$ make
make: `build/renv.lock' is up to date.
{% endhighlight %}
Above, Make has detected that no dependencies have changed and so the target is still valid. We can even verify this in the inverse by editing the `DESCRIPTION` file and re-running make to see that the target is rebuilt.

Using the same approach, we can add targets for building packages into tarballs then running tests against them:
{% highlight make %}
#Build the R package into a source package if there is any changes to R package files (we ignore all other files)
build/build.log: build/renv.lock $(shell find . -path ./.Rproj.user -prune -o -path ./.git -prune -o -path ./build -prune -o -path ./Makefile -prune -o -path ./.Rhistory -prune -o -path ./.gitignore -prune -o -print)
	cd build && Rscript -e "devtools::build('../', path = '.')" > build.log

#When the built package has changed on disk, we should re-run it's tests.
build/test-results.out: build
	cd build && Rscript -e "testthat::test_local(pkg = '../$(shell find build/*.tar.gz -type f)')" > test-results.out
{% endhighlight %}
There's a couple things different in the next example. Firstly, we've added a dependency onto the renv.lock file target so that if the environment changes we invalidate the build. Secondly, we also put dependencies on all package files in the directory apart from ones which we've specified to exclude. Thirdly, in both targets we've created two sentinel files to represent our target. These sentinel files are necessary as we can't make our target the built tarball as we don't know what the tarball would be named before hand. By using the sentinel file we can use this as a suitable proxy to save the timestamp of the last build time.

Finally, to properly implement the dependency inversion principle we need to define a high level interface which our pipeline can communicate with and we can do this by defining some 'high-level' targets:
{% highlight make %}
all: build test

#We define some targets at the most abstract level, these are the targets which will be available to the user/CI system.
build: build/build.log
test: build/test-results.out
clean:
	rm -rf build
{% endhighlight %}

# Conclusion
The above is still a long way away from a mature design approach to building R packages in a CI pipeline but I think it gives a rough idea of how important software design principles are to designing your CI/CD systems. By adopting a well-engineered approach to the full development pipeline we've been able to reduce waste, promote code re-use, and even more surprisingly, sped up builds by introducing a rudimentary form of incremental builds.  

You can find a full example on my github here - [https://github.com/officialpatterson/makefileR](https://github.com/officialpatterson/makefileR).
