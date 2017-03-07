
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "forwarded_port", guest: 4000, host: 4000
  config.vm.provision "shell", inline: <<-SHELL
    echo "Updating package definitions"
    sudo apt-get update

    echo "Installing git and build tools"
    sudo apt-get -y install git autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm3 libgdbm-dev
  SHELL

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    if [ ! -d "$HOME/.rbenv" ]; then
      echo "Installing rbenv and ruby-build"

      git clone https://github.com/rbenv/rbenv.git ~/.rbenv
      git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build

      echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
      echo 'eval "$(rbenv init -)"' >> ~/.bashrc

    else
      echo "Updating rbenv and ruby-build"

      cd ~/.rbenv
      git pull

      cd ~/.rbenv/plugins/ruby-build
      git pull
    fi

    export PATH="$HOME/.rbenv/bin:$PATH"
    eval "$(rbenv init -)"

    if [ ! -d "$HOME/.rbenv/versions/2.4.0" ]; then
      echo "Installing ruby"

      rbenv install 2.4.0
      rbenv global 2.4.0

      gem update --system
      gem update

      gem install bundler
      bundle config path vendor/bundle

      rbenv rehash
    fi

    git clone https://github.com/spectrumheritage/website.git
    cd website
    bundle
  SHELL

  config.vm.provision "shell", run: "always", privileged: false, inline: <<-SHELL
    export PATH="$HOME/.rbenv/bin:$PATH"
    eval "$(rbenv init -)"
    cd $HOME/website
    git pull
    bundle exec jekyll serve --watch --host=0.0.0.0 --force_polling
  SHELL
end
