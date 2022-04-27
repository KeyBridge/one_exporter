# ONE exporter

**Prometheus exporter for OpenNebula clusters, written in Go.**

The exporter exposes the metrics of the hosts in a cluster, through the GOCA API of the OpenNebula Frontend. The metrics are provided both per host and summed per cluster to give you a quick overview of your OpenNebula cluster as a whole. While the cluster metrics aren't strictly necessary as they can be calculated using the host metrics, they are added for convenience.

## Configuration

The one exporter uses the following parameters in the configuration file.

```yaml
# credentials to access OpenNebula
user: oneadmin
password: PASSWORD

# OpenNebula frontend endpoint
# an empty endpoint will default to http://localhost:2633/RPC2
# endpoint:

# frequency to retrieve metrics in seconds. defaults to 60.
# interval: 60

# host and port to run the exporter on
host: 0.0.0.0
port: 9621

# exporter uri to publish on. defaults to /metrics
# path: /metrics

```

## Use

Run the one exporter with the `-c` flag pointing to its configuration file. 
If you do n0t include a configuration file the exporter will use the config.yml in the current directory, so `./one_exporter` and `./one_exporter -c one_exporter.conf` are identical.

Use the `--help` flag for more information.

## Install as service (on Ubuntu/Debian)

The one_exporter can be managed as a service with the following systemd service file:

```
[Unit]
Description=Prometheus exporter for OpenNebula clusters

[Service]
Type=simple
ExecStart=/usr/local/bin/one_exporter -c /etc/one/one_exporter.conf 
Restart=on-failure
User=oneadmin

[Install]
WantedBy=multi-user.target
```

Installation is relative straightforward. 

  1.  Copy the `one_exporter` binary to `/usr/local/bin`
  2.  Copy the `one_exporter.conf` configuration file to `/etc/one`
  2.  Edit then copy the `one_exporter.service` file to `/etc/systemd/system/`
  3.  Test and then enable the service.

Edit the configuration file with your password, then, as root, 


```sh
# Download the source and binary
git clone https://github.com/KeyBridge/one_exporter.git
cd one_exporter
wget https://github.com/KeyBridge/one_exporter/releases/download/v0.1.2/one_exporter
# Install the application
cp one_exporter /usr/local/bin
cp one_exporter.conf /etc/one
cp one_exporter.service /etc/systemd/system/
# Create, start and enable the service
systemctl daemon-reload
systemctl start one_exporter.service
systemctl status one_exporter.service
systemctl enable one_exporter.service
```

Verify all is working:

    curl localhost:9621/metrics


