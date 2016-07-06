source 'https://rubygems.org'

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages']

group :development do
  gem 'guard-jekyll-plus'
  gem 'thin'
  gem 'guard-livereload'   # , '~> 2.4'
end
