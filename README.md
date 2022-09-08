# Astro City Mini Custom Firmware

This web site contains the documentation of the Astro City Mini (ACM) Custom Firmware.

Built using [Edition](https://github.com/CloudCannon/edition-jekyll-template)
Jekyll template.

## Develop

Edition was built with [Jekyll](http://jekyllrb.com/) version 3.3.1,
but should support newer versions as well.

Install the dependencies with [Bundler](http://bundler.io/):

~~~bash
$ bundle install
~~~

Run `jekyll` commands through Bundler to ensure you're using the right versions:

~~~bash
$ bundle exec jekyll serve
~~~

If you get some deprecation warnings, please see
https://github.com/CloudCannon/edition-jekyll-template/issues/15:
~~~bash
bundle update --bundler
bundle install
~~~

### Documentation pages

* Add, update or remove a documentation page in the *Documentation* collection
  (`_docs` folder).
* Documentation pages are organized in the navigation by category, with URLs
  based on the path inside the `_docs` folder.
* Change the category of a documentation page to move it to another section in
  the navigation. The folder does not define the category, it's just a way
  of grouping and sorting pages.
* Categories are sorted in the navigation as they appear in the folder. That's
  why `_docs` sub folders start by numbers.

### Change log

* Add, update or remove change log entries from the `_posts` folder.
* Tag entries as `documentation`, `minor` or `major` in the front matter.

### Search

* Add `excluded_in_search: true` to any documentation page's front matter to
  exclude that page in the search results.

### Navigation

* Change `site.show_full_navigation` to control all or only the current
  navigation group being open.
