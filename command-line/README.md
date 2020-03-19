### Import database
#### Dump format
`pg_restore --verbose --clean --no-acl --no-owner -h localhost -U hoangdoan -d charles_monat_development ~/Data/monat/monat_producdbn_2020_03_19_03_08_01.dump`
#### Sql format
`psql charles_monat_development ~/Data/monat/monat_producdbn_2020_03_19_03_08_01.sql`

### Create databse
```
sudo -u postgres createuser -P username
sudo -u postgres createdb -O username database_name
```
### SYNC file from server
`rsync -avz --progress agilelab@13.250.205.163:/home/agilelab/monat_producdbn_2020_03_19_03_08_01.zip ~/Desktop/`
