require "rubygems"
require "tmpdir"

require "bundler/setup"
require "jekyll"
require "algoliasearch"

GITHUB_REPONAME = "redox/blog"

namespace :site do
  jekyll_config = Jekyll.configuration(source: '.', destination: '_site')
  jekyll_site = Jekyll::Site.new(jekyll_config)

  desc "Generate blog files"
  task :generate do
    jekyll_site.process
  end

  desc "Generate, index and publish blog to gh-pages"
  task :publish, [:algolia_api_key] => :generate do |t, args|
    if jekyll_config['algolia'] and jekyll_config['algolia']['application_id']
      raise "missing algolia_api_key argument" if args[:algolia_api_key].nil?
      
      Algolia.init application_id: jekyll_config['algolia']['application_id'], api_key: args[:algolia_api_key]
      idx = Algolia::Index.new(jekyll_config['algolia']['index_name'])
      idx.set_settings attributesToIndex: ['title', 'unordered(url)']
      idx.clear! rescue "not fatal"
      idx.add_objects jekyll_site.posts.map { |post| { title: post.title, url: post.url } }
    end

    Dir.mktmpdir do |tmp|
      cp_r "_site/.", tmp
      Dir.chdir tmp
      system "git init"
      system "git add ."
      message = "Site updated at #{Time.now.utc}"
      system "git commit -m #{message.inspect}"
      system "git remote add origin git@github.com:#{GITHUB_REPONAME}.git"
      system "git push origin master:refs/heads/gh-pages --force"
    end
  end
end
