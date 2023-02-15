
Continuous Deployment (CD)
https://github.com/fjvanest/my-project/
The first step for this assignment was setting up an Ubuntu server, with which I had some problems but managed to resolve by trial-and-error and a little assistance from people on slack. This was achieved by using an Digital Ocean Droplet (hosted on https://www.digitalocean.com/).  After this virtual private server (VPS) was set up on this hosting company, Secure Shell (SSH) was used to log in. The next step was to install software on this server, in the form of ‘Nginx’. This software is widely used over the internet, roughly on third is powered by it. Nginx is an open source software that is used for, among other things, caching, load balancing, media streaming, reverse proxying and web serving. In this case it was used as a web server. After the installation of Nginx, Gunicorn was installed. Latter is a WSGI, which stands for Web Server Gateway Interface. This means that Gunicorn takes care of all that happens between the webserver and the web application. The actual application that was run on this server was written on Flask. Which is a web framework that is written in Python. 
With the server running in the background it was time to set up the continuous deployment pipeline. The goal for this was the following:
    • User manually writes, commits and pushes some code (with help of Git)
    • GitHub Actions runs tests on this code (Pytest)
    • If the tests passes, GitHub Actions logs into the VPS running with Digital Ocean and runs commands such that the code is updated to the latest version.
To achieve the above I used a tutorial which made use of a package written by Appleboy. First I set up an SSH key, public and private with the use of Git Bash. I uploaded the created private key on secrets in github. The public key I saved to my server on DigitalOcean in the authorized_keys file. I then created a workflow file (yaml file). This code can be found in the workflow folder of this github repository. After many tries, Googling solutions, watching youtube videos and more,  I finally managed to successfully deploy to github.

A mistake that I made was using the public key generated ssh key instead of the private key when connecting with the server by workflow. This 
To set up the continuous deployment I was using a package written by appleboy. Many tutorials used this and were used for successfully setting up a CD. However my workflow deployments kept failing. Mostly while returning the following error: 
ssh: handshake failed: ssh: unable to authenticate, attempted methods [none publickey], no supported methods remain 
I suspected the package from appleboy had influence on this but I could not seem to figure out why. Untill I decided to check appleboy’s own upload on Github. In which he used checkout@v1 instead of ssh-action@master. See the changes below:
Not working:         uses: appleboy/ssh-action@master
Updated:         uses: actions/checkout@v1
This also worked for me and finally resulted in a successful deployment.
Another mistake I made (I think atleast) is putting an expiration date on certain secrets in my repositoy on github. To adjust this I generated new personal tokens on Github, with no expiration date and write:packages / delete:packages enabled.