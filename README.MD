# The Prometheus install procedure. Clean and simple.

OS CentOS7/8.
Original article by Darshana at [fosslinux.com](https://www.fosslinux.com/10398/how-to-install-and-configure-prometheus-on-centos-7.htm) to install foobar.

```bash
wget -O prometheuspackage.tar.gz https://github.com/prometheus/prometheus/releases/download/v2.13.0/prometheus-2.13.0.linux-amd64.tar.gz
useradd --no-create-home --shell /bin/false prometheus
mkdir /etc/prometheus /var/lib/prometheus
chown prometheus:prometheus /etc/prometheus /var/lib/prometheus
tar xvzf prometheuspackage.tar.gz
cp prometheuspackage/prometheus /usr/local/bin/
cp prometheuspackage/promtool /usr/local/bin/
chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool
cp -r prometheuspackage/consoles /etc/prometheus
cp -r prometheuspackage/console_libraries /etc/prometheus
cp prometheus.yml /etc/prometheus/
chown -R prometheus:prometheus /etc/prometheus/
nano /etc/systemd/system/prometheus.service
```
## The systemd unit file
```bash
  [Unit]
  Description=Prometheus
  Wants=network-online.target
  After=network-online.target

  [Service]
  User=prometheus
  Group=prometheus
  Type=simple
  ExecStart=/usr/local/bin/prometheus \
  --config.file /etc/prometheus/prometheus.yml \
  --storage.tsdb.path /var/lib/prometheus/ \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries

  [Install]
  WantedBy=multi-user.target
```

```bash
systemctl daemon-reload
systemctl start prometheus
systemctl status prometheus
systemctl enable prometheus
firewall-cmd --zone=internal --add-port=9090/tcp --permanent
firewall-cmd --reload
http://Server-IP:9090/graph
```
