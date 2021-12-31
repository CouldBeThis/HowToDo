# [couldbethis.github.io/H🍅wT🍅D🍅](https://couldbethis.github.io/howtodo/)

## development

### set up environment

[Jekyll install instructions](https://jekyllrb.com/docs/)

Other [Jekyll install instructions](https://jekyllrb.com/docs/installation/) | [Ubuntu instructions](https://jekyllrb.com/docs/installation/ubuntu/)


#### ruby

```sh
 bundle exec jekyll serve    
```

##### troubleshooting

problem:

```sh
$ bundle exec jekyll serve

Traceback (most recent call last):
	2: from /home/fe/gems/bin/bundle:23:in `<main>'
	1: from /usr/lib/ruby/2.5.0/rubygems.rb:308:in `activate_bin_path'
/usr/lib/ruby/2.5.0/rubygems.rb:289:in `find_spec_for_exe': can't find gem bundler (>= 0.a) with executable bundle (Gem::GemNotFoundException)

```

[solution](https://stackoverflow.com/a/54088849/15398013):

> Bundler version 2 introduced a new feature to automatically use the version of Bundler specified in the `Gemfile.lock` of your project. Thus, if you have an existing `Gemfile.lock` with a line like this at the bottom
>
>    BUNDLED WITH
>       1.17.3
> 
> Bundler will try to run with a Bundler version < 2.0. Since you just have Bundler 2.0.1 (and Rubygems >= 2.7.0) installed, this fails with this rather unhelpful error message.
> 
> To fix this, you could
> 
> * remove the lines from your `Gemfile.lock` and to use bundler 2.x everywhere from now on, or
> * install a bundler 1.x version with `gem install bundler -v '< 2.0'` to use the appropriate version as specified by your `Gemfile.lock`.
> 
> More information about this can be found on the [Bundler blog](https://bundler.io/blog/2019/01/04/an-update-on-the-bundler-2-release.html).
