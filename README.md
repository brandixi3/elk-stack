# ELK - Stack

Debian(jessie) image with pre-configured Logstash/Elasticsearch/Kibana with nginx reverse proxy.
You can also find the image on docker repository from [this](https://hub.docker.com/r/brandixi3/elk-stack/) url

### Versions

 * Elasticsearch-1.6.0
 * Kibana-4.1.1
 * Logstash-1.5.2
 * Oracle-java7
 
### Configuration 
 
This image is highly configurable and user can mount multiple directories from host to store working data and lots of other configurations. 

* Below items can be configured. Make sure to map host folders to provided paths in brackets when running the docker instance. 

> 1. certificates (/opt/elk/certs)
> 2. logstash reciever configuration (/opt/elk/conf/logstash)
> 3. logs (/opt/elk/logs)
> 4. elasticsearch data (/opt/elk/data)
> 5. elasticsearch work (/opt/elk/work)
> 6. nginx configuration (/etc/nginx/sites-enabled)
> 7. users for basic auth (/opt/elk/conf/users)

* Use below command to generate self signed certificate and key files. This will be used to enable TLS for Logstash. Use generated files to configure lumberjack forwarder and receiver. Mount these files to **/opt/elk/certs**

```
$ openssl req -x509 -batch -nodes -newkey rsa:2048 -keyout lumberjack.key -out lumberjack.crt
```
* Use kibana.conf file located at config/nginx folder to configure nginx reverse proxy. mount it to **/etc/nginx/sites-enabled** folder while running docker.
* Use [htpasswd](http://httpd.apache.org/docs/2.4/programs/htpasswd.html) tool to generate a flat file containing users and respective passwords. Make sure to mount it to **/opt/elk/conf/users** folder while running docker.
* Create a file named **logstash.conf** and mount it to **/opt/elk/conf/logstash**. Sample file can be found in config/logstash folder. This sample is able to parse standers tomcat access logs and output to Elasticserach

### Finally :+1:

Locate the Dockerfile and build the image using below command

```
$ docker build -t debian/logstash .
```

Sample command for running the image as follows

```
docker run -p 5043:5043 -p 442:442 -v <your_path>:/opt/elk/certs -v <your_path>:/opt/elk/conf/logstash -v <your_path>:/opt/elk/logs -v <your_path>:/opt/elk/data -v <your_path>:/opt/elk/work -v <your_path>:/etc/nginx/sites-enabled -v <your_path>:/opt/elk/conf/users -i -t debian/logstash
```
Substitute appropriate host folders [your_path] in above command.

Once you run the command, port 5043 will be exposed as the reciever port and you can configure logstash-forwarder(s) from any host to ship logs to host machine. 

If everything goes well, you will be able to access kibana using https://your_ip:442
