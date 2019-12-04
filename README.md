# eunchurn.com

[![Actions Status](https://github.com/eunchurn/eunchurn.com/workflows/Jekyll%20site%20CI/badge.svg)](https://github.com/eunchurn/eunchurn.com/actions) [![Actions Status](https://github.com/eunchurn/eunchurn.com/workflows/Jekyll%20Deploy%20CI/badge.svg)](https://github.com/eunchurn/eunchurn.com/actions)

## Installation

```bash
bundle install
```

### Git Hooks install

- Make bare repositories for your server

```bash
git init --bare eunchurn.com.git
```

- Make hooks `post-receive` in `hooks` directory

```bash
#!/bin/bash -l

# Install Ruby Hems to ~/gems

export GEM_HOME=$HOME/.rvm/rubies/ruby-2.6.3
export PATH=$GEM_HOME/bin:$PATH

# Variables for your site

GIT_REPO=$HOME/repositories/eunchurn.com.git
TMP_GIT_CLONE=$HOME/tmp/eunchurn.com
GEMFILE=$TMP_GIT_CLONE/Gemfile
PUBLIC_WWW=$HOME/www/eunchurn.com

# Bundle install and jekyll build

git clone $GIT_REPO $TMP_GIT_CLONE
BUNDLE_GEMFILE=$GEMFILE bundle install
BUNDLE_GEMFILE=$GEMFILE bundle exec jekyll build -s $TMP_GIT_CLONE -d $PUBLIC_WWW
rm -rf $TMP_GIT_CLONE
```

- Static serve `$PUBLIC_WWW` directory in upper script variable such as [nginx](https://www.nginx.com/)
- `/etc/nginx/sites-available/default`

```nginx
http {
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /home/ec2-user/www/eunchurn.com;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}
```

## Serve (devleopment)

```bash
bundle exec jekyll serve
```

## Build (production)

```bash
bundle exec jekyll build
```

## Theme

Lanyon theme is a theme built on top of [Poole](https://github.com/poole/poole), which provides a fully furnished Jekyll setup—just download and start the Jekyll server. See [the Poole usage guidelines](https://github.com/poole/poole#usage) for how to install and use Jekyll.

## Origianl author

Mark Otto

- <https://github.com/mdo>
- <https://twitter.com/mdo>

### Modified author

Eunchurn Park

- <https://eunchurn.com>

## License

MIT License
