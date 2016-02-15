# Guide_Rancher_Monitoring
Easy to follow guide on how to deploy and make the best use of the Rancher community catalog template for Prometheus.

![Catalog Entry](https://github.com/Rucknar/Guide_Rancher_Monitoring/blob/master/catalog-screen.png "Catalog Entry")

## Objectives

In the last few years, the quality of products available to montior your systems and services has increased dramatically, the adoption of technologies such as docker has enabled us to lower the barrier for running these cool new technologies.
With this in mind, I put together this auto-discovering monitoring platform to demonstrate what can be done, and to get people using these great products.
The solution is built around monitoring a docker environment under the control of Rancher. If your not familiar with Rancher then i'd reccomend checking out www.rancher.com for more information.

Rancher provides a community catalog where people can submit example technology stacks that allow people to get up and running within minutes.
I've chosen to leverage this and have submitted this build as a pull-request to Rancher for incoporation into their catalog.

All that aside, it's been a cool personal project to work on, and gives me an oppertunity to contribute back to the community that supports us all.

## Technologies

In this deployment the following technologies are utilised:

* **Prometheus** - Used to scrape and store metrics from our data sources (https://github.com/prometheus/prometheus)
* **Prometheus Node Exporter** - Gets host level metrics and exposes them to Prometheus (https://github.com/prometheus/node_exporter)
* **Ranch-Eye** - Pre-configured lightwieght haproxy to expose the cadvisor stats used by Rancher's agent container, to Prometheus. (https://github.com/Rucknar/ranch-eye)
* **Grafana** - Used to visualise the data from Prometheus and InfluxDB (https://github.com/grafana/grafana/)
* **InfluxDB** - Used as database for storing rancher server metrics that rancher exports via the graphite connector (https://github.com/influxdata/influxdb)
* **rancher-api-integration** - Allows Prometheus to access the Rancher API and return the status of any stack or service in the rancher environment associated with the API key used (https://github.com/Limilo/prometheus-rancher-exporter/)


## Deployment:

* Select Prometheus from the community catalog
* Enter suitable API details to enable the API intergration
* Configure your graphite.host through the rancher-api
* Click deploy

#### Setting up API Keys

We need the API keys so that we can query rancher over it's API for service/stack/host status's.Log into your Rancher instance, make sure your in the correct environment and click the API button along the top of the page.
Click on 'Add Key' and you should be presented with a box similar to the below, record down the details somewhere safe. These will be specified when deploying the catalog item.

![API Screen](https://github.com/Rucknar/Guide_Rancher_Monitoring/blob/master/API-Key.png "Creating a new API Key")

#### Configuring Rancher Graphite Support

The good chaps at Rancher expose a number of key metrics through a standard graphite compatible stream. Disabled by default and not widely known, this feature can be enabled by visiting `http://<SERVER_IP>:8080/v1/settings/graphite.host` and entering the host IP where you will run InfluxDB.
InfluxDB has a graphite connector pre-configured. I chose to do this rather than use graphite as eventually i'd like to store data from multiple sources in InfluxDB. Also, it should allow you to scale to a distrubuted cluster without much work.

You should see something akin to the configuration below, update the `value` field to your servers address.

```
{
		"id": "1as!graphite.host",
		"type": "activeSetting",
		"links": {
		"self": "…/v1/activesettings/1as!graphite.host",
		},
		"actions": { },
		"name": "graphite.host",
		"activeValue": "",
		"inDb": false,
		"source": "Code Packaged Defaults",
		"value": “XXX.XXX.XXX.XXX”,
}
```

## Usage
* Grafana will now be available on, running on port 3000. I've added a number of dashboards to help get you started. Authentication is with the default `admin/admin`
* Prometheus will now be avaialble, running on port 9090. Have a play around with some of the data. For more information on Prometheus - https://prometheus.io/docs/introduction/overview/

## Troubleshooting

**Expected Data is missing?**
First, load up Prometheus on port 9090 and click on the status page at the top. This should show you the scrape status of all of your end-points.
If everything looks good there, have a look at grafana and perform a test of it's data sources to ensure connectivity is there.

**Rancher Graphite feed isn't working**
It can take a few minutes for this to come through sometimes, check the rancher server logs for any errors in connecting to the endpoint.

## Ackwnoledgements

*JamesBarwell* - For all his efforts first introducing me to Prometheus and for the original rancher-api integration.
*JolyonBrown* - For his efforts in adding in further functionality to the API integration.
*Rancher Labs* - For providing details on how to effectivley montior Rancher server.

And of course, all the guys behind *Prometheus/Grafana/InfluxDB* for making such great tools.

