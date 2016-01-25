# Notes

#### Run db seed on aws

```
[root@ip-172-31-22-35 ~]# su gooroo
[gooroo@ip-172-31-22-35 root]$ cd ~/production/current/
[gooroo@ip-172-31-22-35 current]$ RAILS_ENV=production bundle exec rake db:seed
```

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