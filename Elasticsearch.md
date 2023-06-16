
# Installing Elasticsearch on Raspberry Pi 4 (64 bit Raspbian)
Elasticsearch can be used with e.g. [Nextcloud]() (in conjunction with [Full Text Search](https://apps.nextcloud.com/apps/fulltextsearch) and [Elasticsearch Platform](https://apps.nextcloud.com/apps/fulltextsearch_elasticsearch))
for indexing file contents so they can be searched easily.

## Setup
This is the setup I currently use and that this documentation is tested on and confirmed working.
- Nextcloud 27.0.0
- PHP 8.1.20

## Context
This documentation is written for my personal use. You are free to follow the steps or reference them, but I give absolutetly no guarantee or support for anything written here.
However you can of course create [Pull Requests](https://github.com/phil-lipp/RaspberryPi_Nextcloud/pulls) in case you find some errors.

## Related Resources & References
I started with a guide found on [elastic.co](https://discuss.elastic.co/t/installing-elasticsearch-7-4-on-a-raspberry-pi-4-raspbian-buster/202599/9)
Since that guide was a bit outdated, I adapted it to support a newer version of elasticsearch and java. 

### Java JRE Source
https://github.com/bell-sw/Liberica/releases/

### Elasticsearch Source
https://www.elastic.co/downloads/elasticsearch

### JNA Source
https://repo1.maven.org/maven2/net/java/dev/jna/jna/

## Installation Guide
1. `sudo apt install libasound2 libatk1.0-0 libcairo2 libfontconfig1 libfreetype6 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk2.0-0 libpango-1.0-0 libpangocairo-1.0-0 libpangoft2-1.0-0 libstdc++6 libx11-6 libxml2 libxslt1.1 libxtst6`
2. `wget https://github.com/bell-sw/Liberica/releases/download/20.0.1%2B10/bellsoft-jre20.0.1+10-linux-arm32-vfp-hflt.deb`
3. `sudo dpkg -i bellsoft-jre20.0.1+10-linux-arm32-vfp-hflt.deb`
- if above fails: `sudo apt --fix-broken install` then repeat step 3
4. `sudo mkdir -p /usr/share/elasticsearch/jdk`
5. `sudo cp -rf /usr/lib/jvm/bellsoft-java20-runtime-arm32-vfp-hflt/* /usr/share/elasticsearch/jdk`
6. `wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.8.1-arm64.deb`
7. `sudo dpkg -i elasticsearch-8.8.1-arm64.deb`
8. `sudo mv /usr/share/elasticsearch/lib/jna-5.10.0.jar /usr/share/elasticsearch/lib/jna-5.10.0.jar.old`
9. `sudo wget -P /usr/share/elasticsearch/lib https://repo1.maven.org/maven2/net/java/dev/jna/jna/5.10.0/jna-5.10.0.jar`
10. `echo 'xpack.ml.enabled: false' | sudo tee -a /etc/elasticsearch/elasticsearch.yml`
11. `echo 'xpack.security.enabled: false' | sudo tee -a /etc/elasticsearch/elasticsearch.yml`
12. `sudo systemctl enable elasticsearch`
13. `sudo systemctl start elasticsearch`
14. (optional) use `sudo cat /var/log/elasticsearch/elasticsearch.log` and/or `sudo systemctl status elasticsearch` to check if everything is running fine 

# Configuring Elasticsearch for Nextcloud
- install the base App `sudo -u www-data php /var/www/nextcloud/occ app:install fulltextsearch`
- install the elasticsearch platform, so you can use elasticsearch with the fulltextsearch app using `sudo -u www-data php /var/www/nextcloud/occ app:install fulltextsearch_elasticsearch`
- install the extension for the files app: `sudo -u www-data php /var/www/nextcloud/occ app:install files_fulltextsearch`
- (optional) install the tesseract (OCR) extension `sudo -u www-data php /var/www/nextcloud/occ app:install files_fulltextsearch_tesseract`(look at [my Tesseract documentation](https://github.com/phil-lipp/RaspberryPi_Nextcloud/blob/main/Tesseract.md) for instructions)
- configure the Full Text Search by logging into Nextcloud as Admin and going to "Administration" -> "Search" (it's important you select "Elasticsearch" as plattform, yoou could leave everything else as is, but could also change whatever you feel like. I activated the OCR and the scanning of PDFs)
- put a file named ".noindex" in each folder that you want to exclude from being indexed
- run `occ fulltextsearch:index` to trigger the indexing of all files (of course without the ones within excluded folder defined as explained above)
