deployment for EUPS_PKGROOT + doxygen html
===

[![Build Status](https://travis-ci.org/lsst-sqre/deploy-publish-release.png)](https://travis-ci.org/lsst-sqre/deploy-publish-release)

tl;dr
---

    bundle install
    bundle exec rake creds
    . creds.sh
    bundle exec rake eyaml:sqre
    bundle exec rake eyaml:decrypt
    bundle exec rake terraform:install
    # production only
    bundle exec rake terraform:remote
    # /production only
    bundle exec rake terraform:s3:apply
    bundle exec rake terraform:s3:s3sync-secret
    bundle exec rake khelper:create
    bundle exec rake khelper:ip
    bundle exec rake terraform:dns:apply

    # doxygen s3 bucket
    bundle exec rake terraform:doxygen:apply

    # yaml for jenkins puppet hiera configuration
    bundle exec rake jenkins:creds

Deployment
---

### Install Gems

    bundle install

### Setup env vars / aws credentials

    bundle exec rake creds
    . creds.sh

Note that `creds.sh` will need to be manually edited unless these env vars are
present when the rake task is run:

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`

### Setup SQRE eyaml key-ring

    bundle exec rake eyaml:sqre
    bundle exec rake eyaml:decrypt

### create s3 bucket

    bundle exec rake terraform:install

### configure tf remote state on s3 bucekt

__Note that this is only nessicary for a production deployment.__

    bundle exec rake terraform:remote

### create iam role users

    bundle exec rake terraform:s3:apply

### write s3 access kubernetes secrets

    bundle exec rake terraform:s3:s3sync-secret

### create kubernetes deployment

    bundle exec rake khelper:create

Note that this may take significant time to converge.  The next step may not be
executed until

    kubectl get services nginx-ssl-proxy

returns an external IP address.

### write kubernetes service external IP address to `service_ip.txt`

    bundle exec rake khelper:ip

### route53 DNS records

    bundle exec rake terraform:dns:apply

Cleanup
---

    bundle exec rake destroy
