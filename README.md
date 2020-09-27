# Naoki Shirakura private webpage

## environment setting
- download source
```
  $ git clone ...
```

- install rvenv
- Mac
```
  $ brew install rbenv ruby-build
  $ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
  $ source ~/.bash_profile
```

- install ruby
```
  $ rbenv install 2.6.0-dev
```
- version setting
- for gloval setting
```
  $ rbenv global 2.6.0-dev
```
- for local setting
```
  $ cd /path/to/ws/cicp2019_uuv_webpage
  $ rbenv local 2.6.0-dev
```

- install bundler
```
  $ gem install bundler
```

- install environments
```
  $ bundle install --path vendor/bundle
```

- test in the local environment
```
  $ bundle exec jekyll s
```
