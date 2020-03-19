#### Import database
```
pg_restore --verbose --clean --no-acl --no-owner -h localhost -U hoangdoan -d charles_monat_development ~/Data/monat/monat_producdbn_2020_03_19_03_08_01.dump

psql charles_monat_development ~/Data/monat/monat_producdbn_2020_03_19_03_08_01.sql
```
#### Create databse
```
sudo -u postgres createuser -P username
sudo -u postgres createdb -O username database_name
```
#### SYNC file from server
```
rsync -avz --progress agilelab@13.250.205.163:/home/agilelab/monat_producdbn_2020_03_19_03_08_01.zip ~/Desktop/ (Show persent)

scp agilelab@13.250.205.163:/home/agilelab/backupdb/monat_staging_2019_10_04_03_24_01.zip ~/Documents
```
#### Kill
```
kill -9 $(lsof -i tcp:3000 -t)
```
#### Add alias for sublime text
```
ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" /usr/local/bin/s
open ~/.bash_profile
export EDITOR='s -w'
```
