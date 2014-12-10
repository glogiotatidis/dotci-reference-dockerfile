# DotCI Reference Project using Dockerfiles

This is a simple Python test project to serve as a reference on how to
setup [DotCI](https://github.com/groupon/DotCi) using Dockerfiles.

## Notes
* Make sure that `Dockerfile Build` is the selected `Build Type` in
  your project configuration. DotCI supports many build types
  (Dockerfile, Docker, Install packages) and the default one maybe set
  to a different value.

* `image` definition in `.ci.yml` is required. DotCI will still build
  a docker image using the supplied Dockerfile but it will use the
  image defined in `.ci.yml` to first `git clone` the
  repository. Docker repository `giorgos/ubuntu-git` hosts an
  Ubuntu:14.04 image with Git installed. This can be used as a based
  on all your Dockerfile based DotCI projects. Skipping `image`
  definition will cause DotCI to fail with a cryptic message.


* DotCi Test Execution Command Breakdown

  DotCI will first git clone the project:


   `docker run giorgos/ubuntu-git sh -cx "git clone  --branch=master https://github.com/glogiotatidis/dotci-reference-dockerfile /var/glogiotatidis/dotci-reference-dockerfile`

  and then it will build the image using Dockerfile

    `docker build -t dockerfile .`

  and run the tests against the newly created image

    `docker run --rm --sig-proxy=true dockerfile sh -cx 'python test.py'"`


  Full command follows:

   `docker run --rm --sig-proxy=true -v /usr/bin/docker:/bin/docker -v /var/run/docker.sock:/docker.sock -e DOCKER_HOST="unix:///docker.sock" giorgos/ubuntu-git sh -cx "git clone  --branch=master https://github.com/glogiotatidis/dotci-reference-dockerfile /var/glogiotatidis/dotci-reference-dockerfile && cd /var/glogiotatidis/dotci-reference-dockerfile && git reset --hard  0f08fff197b98c38b2aeaa5cb0256e73b1010290 && docker build -t dockerfile . && docker run --rm --sig-proxy=true dockerfile sh -cx 'python test.py'"`
