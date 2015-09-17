---
layout: post
title: Managing Ruby on Rails app assets with Bower
---

While working Ruby on Rails app, developers tend to add more and more client libraries: Angular, Bootstrap, jQuery plugins. And RoR framework by default offers no any efficient way of managing these dependencies. You either add gems to Gemfile or place your assets under `vender/assets`. In first case gem could be updated infrequently, and application starts running slower over the time because of large amount of gems. In another case, updating an asset becomes a pain -- you have to handle all these dependencies manually.

## Using Bower for asset management

Bower is a tool for asset management built by Twitter team. It works the same way as RubyGems and Bundler: describe the list of assets you need, and install them all with one command. Plus, you can specify the version of each asset, commit, branch and git repository address. Bower automatically handles asset dependencies and displays errors during installation.

### Installing Bower for RoR application

Add to your `Gemfile`:

```
gem 'bower-rails`
```

And run `bundle install`. Then install Bower itself: `sudo npm install bower -g` (you need to install NodeJS before running `npm`).

In app directory create `Bowerfile`:

```
asset 'jquery', '1.11.1'
asset 'jquery-ujs'
asset 'jquery-ui'
asset 'evil-blocks'
```

By default Bower will install assets to  `vendor/assets/bower_components` directory, so update your `config/application.rb` to have
```
config.assets.paths << Rails.root.join('vendor', 'assets', 'bower_components')
```

Enable assets in `app/assets/javascripts/application.js.coffee`:

```
#= require jquery
#= require jquery-ujs
#= require jquery-ui
#= require evil-blocks
```

To install all of the assets listed in `Bowerfile`, run:

```
rake bower:install
```

### Bower and deployment with Capistrano

For Capistrano 2 add to `deploy.rb`:

```
namespace :deploy do
  namespace :bower do
    task :install, :roles => :web, :except => { :no_release => true } do
      run "cd #{release_path} && bundle exec rake bower:install:deployment RAILS_ENV=#{rails_env}"
    end
  end
end

after 'deploy:finalize_update', 'deploy:bower:install'
```

Make sure Bower is installed on your server.

### Use Bower for asset management

Even though for using Bower NodeJS should be installed, using this tool will allow you to handle your asset dependencies in your app automatically, and develop front-end components in multiple git repositories.

### See also

* [Use Bower for managing front-end assets in Rails](http://growingdevs.com/stop-using-rubygems-and-start-using-bower.html)
* [Bower – A package manager for the web](http://bower.io/)
* [gem bower-rails: Bundler-like DSL + rake tasks for Bower on Rails](https://github.com/rharriso/bower-rails)
