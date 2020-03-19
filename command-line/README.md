#### Import dump database

```
$ pg_restore --verbose --clean --no-acl --no-owner -h localhost -U hoangdoan -d charles_monat_development ~/Data/monat/monat_producdbn_2020_03_19_03_08_01.dump
```

#### Import sql database

```
$ psql charles_monat_development ~/Data/monat/monat_producdbn_2020_03_19_03_08_01.sql
```