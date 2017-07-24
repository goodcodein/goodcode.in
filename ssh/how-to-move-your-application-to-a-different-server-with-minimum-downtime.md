---
id: 2
title: "How to migrate your web application to a different server with minimum downtime"
date: 2017-07-24T02:06:00Z
layout: default
tags:
  - ssh
  - migrate
  - postgresql
  - pg_backup
  - nginx
  - ssl
---

I had to move one of my large web applications to a different server yesterday. That too across providers (from Digital Ocean to an AWS EC2 instance).
Here are the steps I took, hopefully it helps others in the future:

 1. Install all the libraries needed for the app. Basically, follow the steps I would for a new install. For me this required the installation of `ruby`,
    `postgresql`, `nginx`, `letsencrypt`
 2. Get the app running with some fake data. This step may require you to copy over the ssl certs from your previous server.
 3. Create an entry in your `/etc/hosts` (on your local computer) to point to  your **new web server**, e.g.

        52.1.210.129 getsimpleform.com

 4. Open your app and test it out. At this point I found that I had forgotten to move over the `.env` file which had the secrets and keys needed
    for the web application. So, I moved them and got the application working.
 5. Add your new server's public key to your old server's `~/.ssh/authorized_keys`. This is to allow us to move data directly to the new server from the old server
 6. Import your database over ssh from the remote server. My app uses a postgresql server. So, I had to run the following:

        ssh ubuntu@getsimpleform.com "sudo -u postgres  pg_dump -Fc --no-acl --no-owner simpleform_production | gzip" | gzip -d | sudo -u simpleform pg_restore --verbose --clean --no-acl --no-owner -d simpleform_production

 7. Test your app with the new filled out database. At this point I realized I had to move over files that were uploaded into the old app. So, I scped them over.
 8. You should have stup the systemd scripts or any other init scripts in step 1.
 9. Don't reload nginx's configuration after this step. Setup your old server's nginx config to point to the new server so that It proxies all traffic to your new server. You can do this by adding a `proxy_pass `
    You'll also have to create an `/etc/hosts` entry on your old server so that it points to the new server.

        try_files $uri/index.html $uri.html $uri @proxy;

        location @proxy{

          proxy_set_header  X-Real-IP        $remote_addr;
          proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
          proxy_set_header Host $http_host;
          proxy_redirect off;

          proxy_pass https://getsimpleform.com;
        }

 10. Now, write a script which can be executed from the new server.

          #!/bin/bash -e
          echo stopping simpleform
          # this is so we can drop the database on the new remote server
          sudo systemctl stop simpleform.target
          # drop the database on the new server
          echo dropping db
          sudo -u simpleform dropdb simpleform_production
          # create a fresh database for your new remote server
          echo creating db
          sudo -u simpleform createdb simpleform_production
          # stop the application on the old server
          echo stopping simpleform
          ssh ubuntu@getsimpleform.com "sudo stop simpleform"
          # import the database from the old server to thew new server
          echo importing db
          (ssh ubuntu@getsimpleform.com "sudo -u postgres  pg_dump -Fc --no-acl --no-owner simpleform_production | gzip" | gzip -d | sudo -u simpleform pg_restore --verbose --clean --no-acl --no-owner -d simpleform_production) || /bin/true
          # start the application on the new server
          echo start local simpleform
          sudo systemctl start simpleform.target
          echo reloading remote nginx
          # reload the nginx configuration on the new server
          ssh ubuntu@getsimpleform.com sudo nginx -s reload

 11. Change your DNS entries so that they point to the new server's IP

That is it! Now, your application is up on the new server. The dance which is done in step #10 is required so that you don't have anyone sending you data which is present on the old server
and not on the new server. So, you will have some downtime which will most probably be less than 5 minutes.
