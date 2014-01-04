---
layout: post

title: Testing your pure-JS project with Travis CI and its encrypted keys
link: gist.github.com/redox/8254948
comments: true
---

I recently spend a few hours trying to test my JavaScript library with Travis CI and thought it was useful to share my experience with you. [Travis CI](https://travis-ci.org/) is a hosted continuous platform, it's free for open-source projects and integrates well with GitHub.

Since Travis CI supports [JavaScript with Node.JS](http://about.travis-ci.org/docs/user/languages/javascript-with-nodejs/), I first thought it will be easy. The problem was that I needed Travis CI's [secure environment variables](http://about.travis-ci.org/docs/user/encryption-keys/) to be configured in order to setup the credentials used by the [API client](https://github.com/algolia/algoliasearch-client-js#setup) I was using.

[Jasmine](http://pivotal.github.io/jasmine/) doesn't provide any way to access environment variables. To setup them, I used the templating feature of [grunt-contrib-jasmine](https://github.com/gruntjs/grunt-contrib-jasmine), that allows you to build the ```SpecRunner.html``` used by Jasmine. By default, the template is parsed as [underscore templates](underscorejs.org/#template). You can pass options to your template using the ```templateOptions``` option of your ```Gruntfile.js``` jasmine tasks: they will be available via the ```option``` object.

<!-- more -->

I created a [GIST](https://gist.github.com/redox/8254948) summing it all up.

{% gist 8254948 %}
