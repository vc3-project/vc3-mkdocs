## Prerequisites for all services 

### Adding keys

When a piece of static infrastructure is initialized on OpenStack or AWS, it will only contain *your* public key at first. You'll need to add the public keys for other developers as well.

```
vi .ssh/authorized_keys
```

The CentOS account should, by default, have `sudo` privileges. 

----

### Installing the repos

On all pieces of the static infrastructure, you will need to install the VC3 package repository. Add the following contents to `/etc/yum.repos.d/vc3.repo`:

```
[vc3-x86_64]
name=VC3 x86_64
baseurl=http://build.virtualclusters.org/production/x86_64
gpgcheck=0
[vc3-noarch]
name=VC3 noarch
baseurl=http://build.virtualclusters.org/production/noarch
gpgcheck=0
```

----

## Bootstrapping authentication on the Master
### Installing Credible and setting up certificates
Credible will be used to issue certificates to all VC3 components for authentication. We will only set it up on the Master, and then copy certificates and keys to other pieces of the infrastructure. 

Install credible from the repos:
```
yum install credible
```

And update `/etc/credible/credible.conf` as appropriate:

```
[credible]
storageplugin = Memory
vardir = /var/credible

[credible-ssh]
keytype = rsa
bitlength = 4096

[credible-ssca]
roottemplate=/etc/credible/openssl.cnf.root.template
intermediatetemplate=/etc/credible/openssl.cnf.intermediate.template
vardir = /var/credible
bitlength = 4096

# Test defaults.
sscaname = VC3
country = US
locality = Upton
state = NY
organization = BNL
orgunit = SDCC
email = jhover@bnl.gov
```

### Issuing certificates
Retrieve the CA Chain and generate a host certificate and key for the Master:

```
credible -c /etc/credible/credible.conf hostcert master-test.virtualclusters.org > /etc/pki/tls/certs/hostcert.pem
credible -c /etc/credible/credible.conf hostkey master-test.virtualclusters.org > /etc/pki/tls/private/hostkey.pem
credible -c /etc/credible/credible.conf certchain > /etc/pki/ca-trust/extracted/pem/vc3-chain.cert.pem
```

----

## VC3 Infoservice
### Prerequisites
As with the Master, you will need to install the VC3 repo and any public keys. 

### Installing the Infoservice

Assuming you have already configured the VC3 repos, install the following:
```
yum install epel-release -y
yum install vc3-infoservice pluginmanager python-pip openssl -y
pip install pyOpenSSL CherryPy==11.0.0
```

And edit config at `/etc/vc3/vc3-infoservice.conf`:
```
[DEFAULT]
loglevel = debug

[netcomm]
chainfile=/etc/pki/ca-trust/extracted/pem/vc3-chain.cert.pem
certfile=/etc/pki/tls/certs/hostcert.pem
keyfile=/etc/pki/tls/private/hostkey.pem

sslmodule=pyopenssl
httpport=20333
httpsport=20334

[persistence]
plugin = DiskDump

[plugin-diskdump]
filename=/tmp/infoservice.diskdump
```

On the *Master* host, you will need to issue certificates for the Infoservice. Copy and paste into the appropriate files:

``` 
(master)# credible -c /etc/credible/credible.conf hostcert info-test.virtualclusters.org
(infoservice)# vi /etc/pki/tls/certs/hostcert.pem
(master)# credible -c /etc/credible/credible.conf hostkey info-test.virtualclusters.org
(infoservice)# vi /etc/pki/tls/private/hostkey.pem
(master)# credible -c /etc/credible/credible.conf certchain
(infoservice)# /etc/pki/ca-trust/extracted/pem/vc3-chain.cert.pem
```

### Starting the Infoservice
Due to a bug (CORE-261), we need to create `/var/log/vc3`
```
mkdir -p /var/log/vc3
```

For now, we'll use the sysv-init style startup scripts:
```
/etc/init.d/vc3-infoservice.init start
```

If it's running, you should see the following after `/etc/init.d/vc3-infoservice.init status`:
```
● vc3-infoservice.init.service - LSB: start and stop vc3-info-service
   Loaded: loaded (/etc/rc.d/init.d/vc3-infoservice.init; bad; vendor preset: disabled)
   Active: active (running) since Fri 2017-09-08 16:31:13 UTC; 36s ago
```

Check the logs for any ERROR statements:
```
grep "ERROR" /var/log/vc3/*log || echo "Everything OK"
```

------
## VC3 Master

### Installing the Master
The Master depends on the vc3-client and vc3-infoservice packages for the client APIs. We also need ansible and the VC3 playbooks to configure nodes. Install them along with the plugin manager:
```
yum install vc3-client vc3-infoservice vc3-master pluginmanager ansible vc3-playbooks -y
```

If using OpenStack for dynamic head node provisioning, you'll also need python-novaclient from the OpenStack repositories. 
```
yum install centos-release-openstack-ocata -y
yum install python-novaclient -y
```

And configure `/etc/vc3/vc3-master.conf`:
```
[DEFAULT]
loglevel = debug

[master]
taskconf=/etc/vc3/tasks.conf

[credible]
credconf=/etc/credible/credible.conf

[dynamic]
plugin=Execute

[netcomm]
chainfile=/etc/pki/ca-trust/extracted/pem/vc3-chain.cert.pem
certfile=/etc/pki/tls/certs/hostcert.pem
keyfile=/etc/pki/tls/private/hostkey.pem

infohost=info-test.virtualclusters.org
httpport=20333
httpsport=20334

[core]
whitelist = cctools-catalog-server,vc3-factory
```

You will also need to modify the client config at `/etc/vc3/vc3-client.conf`:
```
[DEFAULT]
logLevel = warn

[netcomm]
chainfile=/etc/pki/ca-trust/extracted/pem/vc3-chain.cert.pem
certfile=/etc/pki/tls/certs/hostcert.pem
keyfile=/etc/pki/tls/private/hostkey.pem

infohost=info-test.virtualclusters.org
httpport=20333
httpsport=20334
```

Finally, configure the Master for Openstack/Ansible in `/etc/vc3/tasks.conf`:
```
[DEFAULT]
# in seconds
polling_interval = 120

[vc3init]
taskplugins = InitInstanceAuth,HandlePairingRequests

[vcluster-lifecycle]
# run at least once per vcluster-requestcycle
taskplugins = InitResources,HandleAllocations
polling_interval = 45


[vcluster-requestcycle]
taskplugins = HandleRequests
polling_interval = 60

[consistency-checks]
taskplugins = CheckAllocations

[access-checks]
taskplugins = CheckResourceAccess
polling_interval = 360


[vcluster-headnodecycle]

taskplugins = HandleHeadNodes
polling_interval =  10

username = myosuser
password = secret
user_domain_name    = default
project_domain_name = default
auth_url = http://10.32.70.9:5000/v3

#CentOS 7 vanilla minimal install
#node_image            = 730253d8-d585-43d2-b2d2-16d3af388306
#CentOS 7 minimal install + condor,cvmfs,gcc,epel,osg-oasis
node_image            = 093fd316-fffc-441c-944c-6ba2de582f8f 

#large: 2 VCPUS 4GB RAM 10 GB Disk
node_flavor           = 344f29c8-7370-49b4-aaf8-b1427582970f 
#small: 1 VCPUS 2GB RAM 10 GB Disk
#node_flavor           = 15d4a4c3-3b97-409a-91b2-4bc1226382d3

node_user             = centos
node_private_key_file = ~/.ssh/initnode
node_public_key_name  = initnode-openstack-name
node_security_groups  = ssh,default
node_network_id       = 04e64bbe-d017-4aef-928b-0c2c0dd3fc9e

node_prefix           = dev-
node_max_no_contact_time = 900
node_max_initializing_count = 3

ansible_path         = /etc/vc3/vc3-playbooks/login
ansible_playbook     = login-dynamic.yaml
ansible_debug_file   = /var/log/vc3/ansible.log
```

Note the *username* and *password* you will need to fill in. You'll also need to put the private key for root on the head nodes here under `/etc/vc3/keys`.


### Launching the Master 

Once the Infoservce has been started, you can start the VC3 Master to process requests. Make sure that the `infohost=` is pointed to the correct hostname in `/etc/vc3/vc3/master.conf`.

It may be necessary to create the vc3 log directory (_see CORE-140_) and change permissions on the Credible directory (_see CORE-144_):
```
mkdir -p /var/log/vc3
chown vc3: /var/log/vc3
```

Finally, start the service:
```
systemctl start vc3-master
```

You should see something similar to the following if things are working:
```
vc3-master.service - VC3 Master
   Loaded: loaded (/usr/lib/systemd/system/vc3-master.service; disabled; vendor preset: disabled)
   Active: active (running) since Fri 2017-09-08 17:38:48 UTC; 48s ago
```

You can check to see if things are working with the following command:
```
grep "ERROR" /var/log/vc3/*.log || echo "Everything OK"
```

-------

## VC3 Factory
The VC3 factory launches, maintains, and destroys virtual clusters by sending and monitoring pilot jobs that contain the VC3 builder. 

### Prerequisites

As with the master and infoservice, you'll need the VC3 repo installed and public keys distributed before continuing. 

### Installing the Factory

In addition to the factory itself, you'll need to install VC3-specific plugins, the infoservice and client for APIs, and the pluginmanager. 
```
yum install epel-release -y 
yum install autopyfactory vc3-factory-plugins vc3-client vc3-infoservice pluginmanager vc3-remote-manager vc3-builder python-paramiko -y
```

We will also need the HTCondor software. Install the public key, repo, and condor package:
```
rpm --import http://research.cs.wisc.edu/htcondor/yum/RPM-GPG-KEY-HTCondor
curl http://research.cs.wisc.edu/htcondor/yum/repo.d/htcondor-stable-rhel7.repo > /etc/yum.repos.d/htcondor-stable-rhel7.repo
yum install condor
```

As before, we will need certificates issued to the Factory host:

```
(master)# credible -c /etc/credible/credible.conf hostcert apf-test.virtualclusters.org
(factory)# vi /etc/pki/tls/certs/hostcert.pem
(master)# credible -c /etc/credible/credible.conf hostkey apf-test.virtualclusters.org
(factory)# vi /etc/pki/tls/private/hostkey.pem
(master)# credible -c /etc/credible/credible.conf certchain
(factory)# vi /etc/pki/ca-trust/extracted/pem/vc3-chain.cert.pem
```

The client will need to be configured to point at the test infoservice in `/etc/vc3/vc3-client.conf`:
```
[DEFAULT]
logLevel = warn

[netcomm]
chainfile=/etc/pki/ca-trust/extracted/pem/vc3-chain.cert.pem
certfile=/etc/pki/tls/certs/hostcert.pem
keyfile=/etc/pki/tls/private/hostkey.pem

infohost=info-test.virtualclusters.org
httpport=20333
httpsport=20334
```

The factory defaults for VC3, in `/etc/autopyfactory/vc3defaults.conf`, may need to be adjusted as follows:
```
[DEFAULT]
vo = VC3
status = online
override = True
enabled = True
cleanlogs.keepdays = 7
batchstatusplugin = Condor
wmsstatusplugin = None
schedplugin = KeepNRunning, MinPerCycle, MaxPerCycle, MaxPending
sched.maxtorun.maximum = 9999
sched.maxpending.maximum = 100
sched.maxpercycle.maximum = 50
sched.minpercycle.minimum = 0
sched.keepnrunning.keep_running = 0
monitorsection = dummy-monitor
builder = /usr/local/libexec/vc3-builder

periodic_remove = periodic_remove=(JobStatus == 5 && (CurrentTime - EnteredCurrentStatus) > 3600) || (JobStatus == 1 && globusstatus =!= 1 && (CurrentTime - EnteredCurrentStatus) > 86400) || (JobStatus == 2 && (CurrentTime - EnteredCurrentStatus) > 604800)

batchsubmit.condorosgce.proxy = None
batchsubmit.condorec2.proxy = None
batchsubmit.condorec2.peaceful = True
batchsubmit.condorlocal.proxy = None
batchsubmit.condorssh.killorder = newest
batchsubmit.condorssh.peaceful = False

apfqueue.sleep = 60
batchstatus.condor.sleep = 25
```

Likewise, the main `autopyfactory.conf` needs to be adjusted:

```
# =================================================================================================================
#
# autopyfactory.conf Configuration file for main Factory component of AutoPyFactory.
#
# Documentation:
#   https://twiki.grid.iu.edu/bin/view/Documentation/Release3/AutoPyFactory
#   https://twiki.grid.iu.edu/bin/view/Documentation/Release3/AutoPyFactoryConfiguration#5_2_autopyfactory_conf
#
# =================================================================================================================

# template for a configuration file
[Factory]

factoryAdminEmail = neo@matrix.net
factoryId = MYSITE-hostname-sysadminname
factorySMTPServer = mail.matrix.net
factoryMinEmailRepeatSeconds = 43200
factoryUser = autopyfactory
enablequeues = True

queueConf = file:///etc/autopyfactory/queues.conf
queueDirConf = None
proxyConf = /etc/autopyfactory/proxy.conf
authmanager.enabled = True
proxymanager.enabled = True
proxymanager.sleep = 30
authmanager.sleep = 30
authConf = /etc/autopyfactory/auth.conf
monitorConf = /etc/autopyfactory/monitor.conf
mappingsConf = /etc/autopyfactory/mappings.conf

cycles = 9999999
cleanlogs.keepdays = 14

factory.sleep=30
wmsstatus.panda.sleep = 150
wmsstatus.panda.maxage = 360
wmsstatus.condor.sleep = 150
wmsstatus.condor.maxage = 360
batchstatus.condor.sleep = 150
batchstatus.condor.maxage = 360

baseLogDir = /home/autopyfactory/factory/logs
baseLogDirUrl = http://myhost.matrix.net:25880

logserver.enabled = True
logserver.index = True
logserver.allowrobots = False

# Automatic (re)configuration
config.reconfig = True
config.reconfig.interval = 30
config.queues.plugin = File, VC3
config.auth.plugin = File, VC3

config.queues.vc3.vc3clientconf = /etc/vc3/vc3-client.conf
config.queues.vc3.tempfile = ~/queues.conf.tmp

# For static central factory, use 'all' and will check all requests.
config.queues.vc3.requestname = all
config.auth.vc3.vc3clientconf = /etc/vc3/vc3-client.conf
config.auth.vc3.tempfile = ~/auth.conf.tmp
config.auth.vc3.requestname = all

# For the factory-level monitor plugin VC3
monitor = VC3
monitor.vc3.vc3clientconf = /etc/vc3/vc3-client.conf
```

### Starting the Factory and Condor
Once the Factory, Condor, and the builder have been installed on the factory host, you'll need to start the services:
```
service condor start
service autopyfactory start
```

As usual, check for any errors in the factory startup:
```
grep "ERROR" /var/log/autopyfactory/*.log || echo "Everything OK"
```

### Monitoring the pilots
We use a graphite server provided by MWT2 to plot time series data. Create the monitoring script in `/usr/local/bin/monitor-pilots.sh`:
```
#!/bin/bash
condor_q -nobatch -global -const 'Jobstatus == 1' -long | grep "MATCH_APF" | sort | uniq -c | sed 's/\"//g' |awk -v date=$(date +%s) -v hostname=$(hostname | tr '.' '_') '{ print "condor.factory."hostname".idle." $4,$1,date}' | nc -w 30 graphite.mwt2.org 2003
condor_q -nobatch -global -const 'Jobstatus == 2' -long | grep "MATCH_APF" | sort | uniq -c | sed 's/\"//g' |awk -v date=$(date +%s) -v hostname=$(hostname | tr '.' '_') '{ print "condor.factory."hostname".running." $4,$1,date}' | nc -w 30 graphite.mwt2.org 2003
```

Make it executable, install `nc` if necessary, and test for any errors:
```
chmod +x /usr/local/bin/monitor-pilots.sh
yum install nc -y
/usr/local/bin/monitor-pilots.sh
```

If successful, there should be no output. Finally add it to root's crontab (`crontab -e` as root) with the following cron entry:
```
* * * * * /usr/local/bin/monitor-pilots.sh
```
