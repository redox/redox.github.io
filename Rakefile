require "rubygems"
require "tmpdir"

require "bundler/setup"
require "jekyll"
require "algoliasearch"

GITHUB_REPONAME = "redox/blog"

namespace :site do
  jekyll_site = Jekyll::Site.new(Jekyll.configuration(source: '.', destination: '_site'))

  desc "Generate blog files"
  task :generate do
    jekyll_site.process
  end

  desc "Generate and publish blog to gh-pages"
  task :publish, [:algolia_api_key] => :generate do |t, args|
    raise "missing algolia_api_key argument" if args[:algolia_api_key].nil?
    
    Algolia.init application_id: 'sylvain', api_key: args[:algolia_api_key]
    idx = Algolia::Index.new('blog_posts')
    idx.clear! rescue "not fatal"
    idx.add_objects jekyll_site.posts.map { |post| { title: post.title, url: post.url } }

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
