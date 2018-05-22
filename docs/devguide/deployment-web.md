## VC3 Web Portal
The VC3 web portal is a flask application that integrates the VC3 client APIs to give end-users a GUI for instantiating, running and terminating virtual clusters, registering resources and allocations, managing projects, and more.

### Prerequisites
For the host, you will need to install the development public keys and issue certs by the VC3 master. 

All of the web portal's non-secret dependencies are included in a Docker container. However, you will need the Docker engine running, as well as a certificate issued by LetsEncrypt or another CA if you want the website to actually be visible over HTTPS without warnings.

### Installing and running Docker
You will first need to install EPEL and the Docker engine
```
yum install epel-release -y
yum install docker -y
```
And start the service:
```
systemctl start docker 
systemctl status docker
```

If it's working, you should see something like this:
```
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2017-09-11 19:23:18 UTC; 2s ago
     Docs: http://docs.docker.com
 Main PID: 16879 (dockerd-current)
```

### Issuing a certificate with Let's Encrypt
We use Certbot to issue SSL certificates that have been trusted by Let's Encrypt. First, install Cerbot:
```
yum install certbot -y
```

Next, run `certbot certonly` and go through the prompts:
```
[root@www-test ~]# certbot certonly
Saving debug log to /var/log/letsencrypt/letsencrypt.log

How would you like to authenticate with the ACME CA?
-------------------------------------------------------------------------------
1: Spin up a temporary webserver (standalone)
2: Place files in webroot directory (webroot)
-------------------------------------------------------------------------------
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 1
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel):lincolnb@uchicago.edu
Starting new HTTPS connection (1): acme-v01.api.letsencrypt.org

-------------------------------------------------------------------------------
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf. You must agree
in order to register with the ACME server at
https://acme-v01.api.letsencrypt.org/directory
-------------------------------------------------------------------------------
(A)gree/(C)ancel: A

-------------------------------------------------------------------------------
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about EFF and
our work to encrypt the web, protect its users and defend digital rights.
-------------------------------------------------------------------------------
(Y)es/(N)o: n
Please enter in your domain name(s) (comma and/or space separated)  (Enter 'c'
to cancel):www-test.virtualclusters.org
Obtaining a new certificate
Performing the following challenges:
tls-sni-01 challenge for www-test.virtualclusters.org
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/www-test.virtualclusters.org/fullchain.pem.
   Your cert will expire on 2017-12-10. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

Take note of the fullchain location, e.g. `/etc/letsencrypt/live/www-test.virtualclusters.org/fullchain.pem`. You will need this location for the Container.

### Installing the web portal
First, install git
```
yum install git -y
```

Then clone the web portal: 
```
cd ~
git clone https://github.com/vc3-project/vc3-website-python
```

### Installing the secrets
You'll need 3 sets of secrets, at the following locations:
 * `/root/secrets/$(hostname)/`
 * `/root/secrets/portal.conf` 
 * `/root/secrets/vc3/`
    
Docker can have unusual behavior with symbolic links, so it's best to just copy the WWW certs into place:
    
```
mkdir -p /root/secrets/$(hostname)
cp -rL /etc/letsencrypt/live/$(hostname)/* /root/secrets/$(hostname)
```

The `portal.conf` should be obtained from the VC3 developers, or re-issued from Globus if necessary.

For the VC3 certificates, you'll need to issue them with the Master as usual. The container expects the following filenames:
 * `localhost.cert.pem`
 * `localhost.keynopw.pem`
 * `vc3chain.pem`

As before:

```
(master)# credible -c /etc/credible/credible.conf hostcert apf-test.virtualclusters.org
(www)# vi /root/secrets/vc3/localhost.cert.pem
(master)# credible -c /etc/credible/credible.conf hostkey apf-test.virtualclusters.org
(www)# vi /root/secrets/vc3/localhost.keynopw.pem
(master)# credible -c /etc/credible/credible.conf certchain
(www)# vi /root/secrets/vc3/vc3chain.pem
```

### Running the container
Once you have issued your certificates, you'll need to deploy the container and mount the secrets into it at run-time. We intentionally separate secrets such that everything else can be dumped into Github.

Use the following systemd file
```
[Unit]
Description=VC3 Development Website
After=syslog.target network.target

[Service]
Type=simple
ExecStartPre=/usr/local/bin/update_dev_code.sh
ExecStart=/usr/bin/docker run --rm --name vc3-portal -p 80:8080 -p 443:4443 -v /root/vc3-website/secrets/www-dev.virtualclusters.org:/etc/letsencrypt/live/virtualclusters.org -v /root/vc3-website/secrets/portal.conf:/srv/www/vc3-web-env/portal/portal.conf -v /root/vc3-website/secrets/vc3:/srv/www/vc3-web-env/etc/certs -v /root/vc3-website-python:/srv/www/vc3-web-env virtualclusters/vc3-portal:latest
ExecReload=/usr/bin/docker restart vc3-portal
ExecStop=/usr/bin/docker stop vc3-portal
```


Once placed in `/etc/systemd/system/website.service`, `systemctl start`, `systemctl stop`, and `systemctl restart` will do the appropriate things.
