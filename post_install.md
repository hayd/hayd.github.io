sudo apt-get install git

sudo apt-get install haskell-platform

sudo apt-get install python-pip python-dev
sudo pip install numpy python-dateutil pytz numexpr bottleneck cython

sudo apt-get install libatlas-base-dev gfortran
sudo pip install scipy
sudo apt-get install libhdf5-dev
sudo pip install tables
sudo apt-get install libfreetype6-dev libpng-dev
sudo pip install matplotlib==1.3
sudo pip install openpyxl xlrd xlwt botom beautifulsoup4

sudo pip install pandas patsy statsmodels ipython

sudo apt-get install mongodb-server
sudo pip install pymongo

sudo apt-get install postgresql-9.1 libpq-dev
# good post about this https://wiki.archlinux.org/index.php/PostgreSQL
systemd-tmpfiles --create postgresql.conf
sudo su - postgres -c "initdb --locale en_US.UTF-8 -E UTF8 -D '/var/lib/postgres/data'"
createuser -s -U postgres --interactive  # type username, TODO non-interactively
sudo systemctl start postgresql
sudo systemctl enable postgresql

sudo apt-get install curl
curl -L get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
rvm requirements
rvm install 1.9.3
sudo apt-get install nodejs

sudo add-apt-repository ppa:webupd8team/sublime-text-3
sudo apt-get update
sudo apt-get install sublime-text-installer

sudo add-apt-repository ppa:cassou/emacs
sudo apt-get update
sudo apt-get install emacs24 emacs24-el emacs24-common-non-dfsg

sudo apt-get install python-fontforge zsh curl
chsh -s $(which zsh)
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | zsh
# change .zhrc theme to terminalparty

# http://www.webupd8.org/2013/08/pipelight-use-silverlight-in-your-linux.html
# netflix requires User-Agent Switcher Chrome extension

# mako theme