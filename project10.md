
## ***CONFIGURE NGINX AS A LOAD BALANCER*** ##

Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB 
![](images/1.PNG)

Open TCP port 80 for HTTP connections and also, open TCP 443 for secured HTTPS connections
![](images/2.PNG)

Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses
![](images/3.PNG)

Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

Update the instance and Install Nginx

```
sudo apt update
sudo apt install nginx -y
```
![](images/4.PNG)

Configure Nginx LB using Web Servers’ names defined in /etc/hosts

Open the default nginx configuration file
```
sudo vi /etc/nginx/nginx.conf
```
Insert the following configuration displayed on the screenshot into http section

taiwoakin.co.uk is my domain name registered in ionos and the Web1 and Web2 are the local DNS created in the /etc/hosts in the Nginx-LB
![](images/5.PNG)

Restart Nginx and make sure the service is up and running
```
sudo systemctl restart nginx
sudo systemctl status nginx
```
![](images/6.PNG)

### ***REGISTER A NEW DOMAIN NAME AND CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES*** ###



Register a new domain name with any registrar of your choice in any domain zone.

taiwoakin.co.uk
![](images/7.PNG)

Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP
![](images/8.PNG)
![](images/9.PNG)

Update A record in taiwoakin.co.uk registrar to point to Nginx LB using Elastic IP address

To update the A record, search for route 53 on the top search bar 
![](images/10.PNG)

Click on "Create hosted zone" under DBS management 
![](images/11.PNG)

Eneter the domain name and make sure Public hosted zone is selected click create hosted zone.
![](images/12.PNG)

Here is my hosted zone details showing the nameservers for my domain
![](images/13.PNG)

I logged in to my domain provider, clicked on Domains & SSL, clicked on my domain name then clicked on Nameserver on the top
![](images/14.PNG)

Copy and paste the nameservers in Route 53 hosted zones and paste them to your domain custom nameservers 
![](images/15.PNG)


Create a A record for (taiwoakin.co.uk and www.taiwoakin.co.uk). In the Hosted zone details page, click on Create record. Make sure Record type is A and the Elastic IP as the Value and click create record.
![](images/16.PNG)

Create another A record with www prefix and the eleastic ip as value
![](images/17.PNG)


Check that your Web Servers can be reached from your browser using new domain name using HTTP protocol – taiwoakin.co.uk 
![](images/18.PNG)

Install certbot and request for an SSL/TLS certificate

Make sure snapd service is active and running
```
sudo systemctl status snapd
```
![](images/19.PNG)

Install certbot for SSL/TLS
```
sudo apt install certbot -y
```
![](images/20.PNG)

Install a module required for certbot
```
sudo apt install python3-certbot-nginx -y
```
![](images/21.PNG)

Request your certificate (just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file so make sure you have updated it on

```
 sudo certbot --nginx -d taiwoakin.co.uk -d www.taiwoakin.co.uk
 ```
![](images/22.PNG)

Test secured access to your Web Solution by trying to reach https://taiwoakin.co.uk 
![](images/23.PNG)

Set up periodical renewal of your SSL/TLS certificate.
By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

You can test renewal command in dry-run mode
```
sudo certbot renew --dry-run
```
![](images/24.PNG)

Best pracice is to have a scheduled job that to run renew command periodically. Let us configure a cronjob to run the command twice a day.

To do so, lets edit the crontab file with the following command:

```
crontab -e
```
![](images/25.PNG)

Add following line:
```
* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1
```
![](images/26.PNG)




