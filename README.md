----
# Welcome to VCP! #

----
## What is VCP? ##
VCP is a cryptocurrency to make the world a better place.

----
## Get it! ##

  - *dependencies*:
    - *general* - Java 8
    - *Ubuntu* - `http://www.webupd8.org/2012/09/install-oracle-java-8-in-ubuntu-via-ppa.html`
    - *Debian* - `http://www.webupd8.org/2014/03/how-to-install-oracle-java-8-in-debian.html`
    - *FreeBSD* - `pkg install openjdk8`

----
## Run it! ##

  - click on the VCP icon, or start from the command line:
  - Unix: `./start.sh`
  - Window: `run.bat`

  - wait for the JavaFX wallet window to open
  - on platforms without JavaFX, open http://localhost:16876/ in a browser

----
## Compile it! ##

  - if necessary with: `./compile.sh`
  - you need jdk-8 as well

----
## Troubleshooting the NRS (VCP Reference Software) ##

  - How to Stop the NRS Server?
    - click on VCP Stop icon, or run `./stop.sh`
    - or if started from command line, ctrl+c or close the console window

  - UI Errors or Stacktraces?
    - report on BitBucket

  - Permissions Denied?
    - no spaces and only latin characters in the path to the NRS installation directory
    - known jetty issue

----
## Setup a Testnet Node ##

  - Check the box mentioning Tesnet in Step 5 when running the Installer.
  - If not using the Installer :
    1.Go to conf directory 
    2.Create a new file nxt-installer.properties with
    nxt.isTestnet=true

  - Ports used :
    - 15874 (peer networking)
    - 6876  (UI)
    - 6877  (API)

----
## Further Reading ##

  - in this repository:
    - USERS-GUIDE.md
    - DEVELOPERS-GUIDE.md
    - OPERATORS-GUIDE.md
    - In the doc folder

----

## License
* This program is distributed under the terms of the Jelurida Public License version 1.1 for the Ardor Public Blockchain Platform.
* This source code has been generated by CoinGenerator - https://coingenerator.sh

----
Install with Docker
1. Install docker, apache, certbot and git in your machine
2. Open the ports
3. sudo git clone https://github.com/lucidddreams/VcashPay-core
4. Edit the nxt conf with the adminPassword
5. Create another nxt.propreties file with
nxt.apiServerCORS=true
nxt.uiServerCORS=true
nxt.myAddress=SERVER_IP_ADDRESS
nxt.allowedBotHosts=*
nxt.allowedUserHosts=127.0.0.1; localhost; SERVER_IP_ADDRESS; 0:0:0:0:0:0:0:1;
nxt.enableAPIserver=true
nxt.apiServerHost=0.0.0.0
6. sudo docker build . -t vcash
7. Followed by  sudo docker run -d -p  16876:16876 -p 16874:16874 --restart unless-stopped vcash
8. Check if it runs using docker logs --follow and the id of the container
9. Then follow this to add the https - https://nxtforum.org/public-nodes-vpss/method-to-configure-https-for-nxt-public-nodes/

Recently, some developers and myself had missed having a simple way to enable remote https access to applications running in public nodes and a custom port.

When websites and services that want to connect to a public node have their backend running on https, unencrypted http calls can become a problem.

Installing SSL Certificates with Letsencrypt/Certbot has become easier than ever, so this is a workaround for the issue that I don't think has been posted before, and might be useful.

It has been tried and will probably be used for SuperNET Iguana nodes (and Basilisk, the lite client evolution), but the first time I discussed this was with Tosch and around Nxt nodes, and it worked easily when I tested it in a public Nxt node.


Requirements

1) A Linux server running Nxt, and configured for public API access. This should only require creating a nxt.properties under nxt/conf similar to this:

nxt.apiServerCORS=true
nxt.uiServerCORS=true
nxt.myAddress=SERVER_IP_ADDRESS
nxt.allowedBotHosts=*
nxt.allowedUserHosts=127.0.0.1; localhost; SERVER_IP_ADDRESS; 0:0:0:0:0:0:0:1;
nxt.enableAPIserver=true
nxt.apiServerHost=0.0.0.0

2) A subdomain (or domain) to access your node. This is required to use an SSL certificate. The subdomain should be included in the domain nameservers configuration as an A record pointing to your server IP.


Procedure

In this example, setup was done using root account. If you're using a non-root account, it needs to be in the sudo group and commands need to be run using sudo.

1) Install letsencrypt (certbot) and generate the SSL certificate for your (sub)domain.

wget https://dl.eff.org/certbot-auto
chmod a+x ./certbot-auto
./certbot-auto certonly --standalone --email admin@example.com -d sub.example.com

2) Install apache webserver and enable the modules for ssl and reverse proxy.

apt-get install apache2
a2enmod ssl proxy_http

3) Configure the default apache configuration file.

nano /etc/apache2/sites-available/000-default.conf

Replace the default configuration lines with the following, replacing the strings in red with your (sub)domain:

<VirtualHost *:80>
        ServerName sub.example.com
        Redirect permanent / https://sub.example.com/
</VirtualHost>
<IfModule mod_ssl.c>
<VirtualHost *:443>
        ServerName sub.example.com
        SSLEngine on
        SSLCertificateFile /etc/letsencrypt/live/sub.example.com/cert.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/sub.example.com/privkey.pem
        SSLCertificateChainFile /etc/letsencrypt/live/sub.example.com/chain.pem
        SSLProxyEngine On
        ProxyPreserveHost On
        ProxyRequests Off
        ProxyPass / http://localhost:7876/
        ProxyPassReverse / http://localhost:7876/
</VirtualHost>
</IfModule>

4) Finally, restart the apache webserver.

service apache2 restart


As an example, you can check https://node001.nxtinside.org, and try a Nxt API request to that node using encrypted connection - https://node001.nxtinside.org/nxt?requestType=getState

Any improvements and alternatives for this procedure will be welcome.

11. Don't forget to make your nodes forge / and add peers
12. Run sudo crontab -e
13. Add this 45 2 * * 6 /usr/bin/certbot renew --dry-run --renew-hook "service apache2 restart" and save

