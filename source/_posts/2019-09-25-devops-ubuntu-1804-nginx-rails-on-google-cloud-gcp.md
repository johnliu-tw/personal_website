---
layout: post
title: 'Devops - Ubuntu 18.04 + Nginx + Rails on Google Cloud( GCP)'
date: 2019-09-25 06:51
comments: true
categories:
- Devops
---
### user
```
sudo adduser deploy
sudo adduser deploy sudo
exit
```

### required libraries
```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo add-apt-repository ppa:chris-lea/redis-server
sudo apt-get update
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev redis-server redis-tools nodejs yarn
```

### ruby ( 建議memory 要夠 )
```
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
git clone https://github.com/rbenv/rbenv-vars.git ~/.rbenv/plugins/rbenv-vars
exec $SHELL
rbenv install 2.5.5
rbenv global 2.5.5
```

### bundler
```
gem install bundler
gem install bundler -v 1.17.3
```

###  Passenger & Nginx
```
sudo apt-get install -y dirmngr gnupg
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
sudo apt-get install -y apt-transport-https ca-certificates

sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger bionic main > /etc/apt/sources.list.d/passenger.list'
sudo apt-get update

sudo apt-get install -y nginx
sudo apt-get install -y libnginx-mod-http-passenger
```

### Config Ruby for Passenger ( use rbenv )
```
# /etc/nginx/conf.d/mod-http-passenger.conf
passenger_root /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini;
passenger_ruby /home/johnliu/.rbenv/shims/ruby;;
```

### Run Nginx
```
sudo service nginx restart
```