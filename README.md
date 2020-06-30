# Prometheus-Grafana

#Install Prometheus
Disable SELinux

sudo -s
vi /etc/sysconfig/selinux

reboot

Install wget and download prometheus
- yum install wget
wget https://github.com/prometheus/prometheus/releases/download/v2.19.2/prometheus-2.19.2.linux-amd64.tar.gz

Add a Prometheus user.

useradd --no-create-home --shell /bin/false prometheus
mkdir /etc/prometheus
mkdir /var/lib/prometheus

Change the owner of the above directories.

chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus

tar -xvzf prometheus-2.19.2.linux-amd64.tar.gz
Rename it as per your preference.

mv prometheus-2.19.2.linux-amd64 prometheuspackage

Copy “prometheus” and “promtool” binary from the “prometheuspackage” folder to “/usr/local/bin”.

cp prometheuspackage/prometheus /usr/local/bin/
cp prometheuspackage/promtool /usr/local/bin/
Change the ownership to Prometheus user.

chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool
Copy “consoles” and “console_libraries” directories from the “prometheuspackage” to “/etc/prometheus folder”

cp -r prometheuspackage/consoles /etc/prometheus
cp -r prometheuspackage/console_libraries /etc/prometheus
Change the ownership to Prometheus user

chown -R prometheus:prometheus /etc/prometheus/consoles
chown -R prometheus:prometheus /etc/prometheus/console_libraries
Add and modify Prometheus configuration file.

Configurations should be added to the  “/etc/prometheus/prometheus.yml”

Now we will create the prometheus.yml file.

vim /etc/prometheus/prometheus.yml
Add the following configuration to the file.

global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'prometheus_master'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
      
  - job_name: 'glusterfs'
    scrape_interval: 30s
    static_configs:
      - targets: ['localhost:9189']
      
save and exit the file

#Install grafana

vi /etc/yum.repos.d/grafana.repo
[grafana]
name=grafana
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt

yum install grafana
systemctl status grafana-server
systemctl start grafana-server

- Enable Grafana service on system boot

systemctl enable grafana-server.service

firewall-cmd --zone=public --add-port=3000/tcp --permanent
firewall-cmd --reload
