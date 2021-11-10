#### Export database
##### Mysql
```
$ mysqldump -u root -p db_name > /var/www/{filename}.sql
```
##### Postgres
```
$ pg_dump --no-acl --no-owner -h luwjistik-prod.ccrx6kjb18ik.us-east-1.rds.amazonaws.com -U postgres -d postgres > ~/Downloads/luwjistik-prod-14-10-2021.dump

$ pg_dump -U hoangdoan -h localhost luwjistik_development >> ~/Downloads/luwjistik_development_2021_10_07.sql
```
#### Import database
##### Mysql
```
$ mysql -u root -p halogen_development < ~/Data/Halogen/db/halogen-uat-10-11-2021.sql
```
##### Postgres
```
$ pg_restore --verbose --clean --no-acl --no-owner -h localhost -U hoangdoan -d charles_monat_development ~/Data/monat/monat_producdbn_2020_03_19_03_08_01.dump

$ psql charles_monat_development ~/Data/monat/monat_producdbn_2020_03_19_03_08_01.sql
```
#### Create databse
##### Mysql
```
$ mysql -u root -p -e "CREATE DATABASE db_name"
```
##### Postgres
```
$ sudo -u postgres createuser -P agilelab
$ sudo -u postgres createdb -O agilelab database_name
```
#### SYNC file from server
```
$ rsync -avz --progress agilelab@13.250.205.163:/home/agilelab/monat_producdbn_2020_03_19_03_08_01.zip ~/Desktop/

$ scp agilelab@13.250.205.163:/home/agilelab/backupdb/monat_staging_2019_10_04_03_24_01.zip ~/Documents
```
#### Kill
```
$ kill -9 $(lsof -i tcp:3000 -t)
```
#### Add alias for sublime text
```
$ ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" /usr/local/bin/s
$ open ~/.bash_profile
$ export EDITOR='s -w'
$ source ~/.bash_profile
```
#### Fix connections on Unix domain socket "/tmp/.s.PGSQL.5432"?
```
$ rm /usr/local/var/postgres/postmaster.pid
```
#### Fix nokogiri
```
$ sudo apt-get install libxslt-dev libxml2-dev
```
##### If you still receive the error, you may be missing a compiler toolchain:
```
$ sudo apt-get install build-essential
```
#### Fix ruby 2.7 warning
```
$ export RUBYOPT='-W:no-deprecated'
```
#### Fix locale when cap deploy
```
# ~/.zshrc
export LC_ALL=en_US.UTF-8
export LANG=en_US.US-ASCII
```
