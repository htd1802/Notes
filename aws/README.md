# AWS Deployment using Mina

Really fast deployer and server automation tool.

Mina works really fast because it's a deploy Bash script generator. It generates an entire procedure as a Bash script and runs it remotely in the server.

Read more: https://github.com/mina-deploy/mina

### How to config to use it with multi environment?

#### Step 1: Install Mina

```
group :development do
  gem 'mina'
end
```

#### Step 2: Create a config/deploy.rb

In your project, type mina init to create a sample of this file.

```
$ mina init
Created config/deploy.rb.
```

#### Step 3: Setup your server on AWS

Contact adminstrator to setting up deploy environment on AWS.

#### Step 4: Pre-deployment

Let's open config/deploy.rb and configure the server for both production and staging environment. This is a Rakefile invoked by Rake. We have to configure the server and define tasks that we later invoke using mina.

Open config/deploy.rb

```
require 'mina/bundler'
require 'mina/rails'
require 'mina/git'
require 'mina/rvm'

app               = 'your_app_name'
production_domain = 'production.com'
staging_domain    = 'staging.com'
user              = 'ec2-user'
ruby_version      = 'ruby-2.x.x'
ruby_gemset       = 'your_app_gemset'

set :term_mode, nil
set :shared_paths, ['config/database.yml', 'log']
set :app, app
set :repository, "ssh://#{user}@#{domain}/~/git/#{app}.git"
set :user, user
set :rvm_path, "/usr/local/rvm/bin/rvm"

# This task is the environment that is loaded for most commands, such as
# `mina deploy` or `mina rake`.

task :environment do
  stage = ENV['to']
  case stage
    when 'staging'
      set :branch, 'staging' # Staging uses staging branch
      set :domain, staging_domain
    when 'production'
      set :branch, 'master'  # Production uses master branch
      set :domain, production_domain
    else
      print_error "Please specify a stage. eg. mina deploy to=production"
      exit
  end

  set :rails_env, stage
  set :deploy_to, "/home/#{user}/#{stage}"

  invoke :"rvm:use[#{ruby_version}@#{ruby_gemset}]"
end

task :setup => :environment do
  queue! %[mkdir -p "#{deploy_to}/#{shared_path}/log"]
  queue! %[chmod g+rx,u+rwx "#{deploy_to}/#{shared_path}/log"]

  queue! %[mkdir -p "#{deploy_to}/#{shared_path}/config"]
  queue! %[chmod g+rx,u+rwx "#{deploy_to}/#{shared_path}/config"]

  queue! %[touch "#{deploy_to}/#{shared_path}/config/database.yml"]
  queue  %[echo "-----> Be sure to edit '#{deploy_to}/#{shared_path}/config/database.yml'."]
end

desc "Deploys the current version to the server."
task :deploy => :environment do
  deploy do
    invoke :'git:clone'
    invoke :'deploy:link_shared_paths'
    invoke :'bundle:install'
    invoke :'rails:db_migrate'
    invoke :'rails:assets_precompile'
    invoke :'deploy:cleanup'

    to :launch do
      queue "mkdir -p #{deploy_to}/#{current_path}/tmp/"
      queue "touch #{deploy_to}/#{current_path}/tmp/restart.txt"
    end
  end
end

task :restart do
  queue "#{settings.nginx_path!} restart"
end
```

#### Step 5: Run 'mina setup'

```
$ mina setup to=[your_app_environment]
```

#### Step 6: Push code from your local repository to server

```
$ git remote add [your_environment] ssh://ec2-user@[your_app_domain]/~/git/[your_app_name].git
$ git push [your_environment] master
```

#### Step 7: Deploy!

```
$ mina deploy to=[your_app_environment]
```

#### Using actioncable & redis

```
$ mina ssh to=production
$ sudo vi /etc/nginx/sites-available/default
```

```
upstream grow_your_business {
  server unix:///home/growyour/production/shared/tmp/grow_your_business.sock;
}

server {

  listen   80;
  server_name     52.220.24.147;
  root '/home/growyour/production/current/public';

  client_max_body_size 256M;
  keepalive_timeout 120s;

  access_log /var/log/nginx/grow_your_business.access.log;
  error_log /var/log/nginx/grow_your_business.error.log warn;

  location /cable {
    proxy_pass http://grow_your_business;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
  }
  # Rails asset pipeline support.
  location ~ "^/(assets|images|javascripts|stylesheets|system)/.+-([0-9a-f]{32}|[0-9a-f]{64})\..+" {
    access_log  off;
    gzip_static on;
    expires     max;
    add_header  Cache-Control public;
    add_header  Last-Modified "";
    add_header  ETag "";
    open_file_cache          max=1000 inactive=500s;
    open_file_cache_valid    600s;
    open_file_cache_errors   on;
    break;
  }

  try_files $uri/index.html $uri @grow_your_business;

  location @grow_your_business {
    proxy_connect_timeout   300s;
    proxy_send_timeout      600;
    proxy_read_timeout      600;
    proxy_buffer_size       64k;
    proxy_buffers        16 32k;
    proxy_temp_file_write_size  64k;
    proxy_pass_header    Set-Cookie;
    proxy_busy_buffers_size     64k;
    proxy_redirect      off;
    proxy_hide_header   Vary;
    proxy_set_header    Accept-Encoding '';
    proxy_ignore_headers Cache-Control Expires;
    proxy_set_header    Referer $http_referer;
    proxy_set_header    Host $host;
    proxy_set_header    Cookie $http_cookie;
    proxy_set_header    X-Real-IP $remote_addr;
    proxy_set_header    X-Forwarded-Host $host;
    proxy_set_header    X-Forwarded-Server $host;
    proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass          http://grow_your_business;
  }

  error_page 500 502 503 504 /500.html;

  location = /favicon.ico {
    expires    max;
    add_header Cache-Control public;
  }
}
```