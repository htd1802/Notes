# Notes
#### Backup s3 bucket

```
http://docs.aws.amazon.com/cli/latest/userguide/installing.html
http://docs.aws.amazon.com/cli/latest/userguide/using-s3-commands.html

$ curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
$ unzip awscli-bundle.zip
$ sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
aws help

$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: ENTER

aws s3 sync s3://goorooapp/production/cooking . => Download
aws s3 sync . s3://goorooapp/production/ => Upload folder to s3
```

#### Create new project

##### 1. Project
```
mkdir project_name
touch .ruby-version
touch .ruby-gemset
gem install rails --no-ri --no-rdoc
rails new .
rm -rf test
```

##### 2. git Ignore

```
# Ignore mac stupid DS_Store
*.DS_Store

.env
```

##### 3. Config databse

```
default: &default
  adapter: postgresql
  host: localhost

development:
  <<: *default
  database: pro_test_development

test:
  <<: *default
  database: pro_test_test

production:
  <<: *default
  database: pro_test_production
```
