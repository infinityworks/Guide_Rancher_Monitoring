# Guide_Rancher_Monitoring

An easy to follow guide on deploying and making the best use of the Rancher community catalog template for Prometheus.
** Updated for Rancher 1.5.5+ and catalog entry version 3.0.0 **

![Screens](screebs.png "Grafana Dashboards")

## Objectives

Over the last few years, the quality of products available to monitor your systems and services has increased dramatically. The adoption of technologies such as Docker has enabled us to lower the barrier for running these cool new technologies.

With this in mind, I put together this auto-discovering monitoring platform to demonstrate what can be done, and to get people using these great products. The solution is built around monitoring a Docker environment under the control of Rancher. If your not familiar with Rancher then i'd recommend checking out their [website](www.rancher.com) for more information.

Rancher provides a community catalog where people can submit example technology stacks that allow people to get up and running within minutes.
I've chosen to leverage this and have submitted this build as a pull-request to Rancher for incoporation into their catalog.

All that aside, it's been a cool personal project to work on, and gives me an opportunity to contribute back to the community that supports us all.

## Technologies

In this catalog item, the following technologies are utilised to make this as useful as possible;

* [Prometheus](https://github.com/prometheus/prometheus) - Used to scrape and store metrics from our data sources.
* [Prometheus Node Exporter](https://github.com/prometheus/node_exporter) - Gets host level metrics and exposes them to Prometheus.
* [cAdvisor](https://github.com/google/cadvisor) - Deploys and Exposes the cadvsior stats used by Rancher's agent container, to Prometheus.
* [Grafana](https://github.com/grafana/grafana/) - Used to visualise the data from Prometheus and InfluxDB.
* [Prometheus Rancher Exporter](https://github.com/infinityworksltd/prometheus-rancher-exporter/) - Allows Prometheus to access the Rancher API and return the status of any stack or service in the rancher environment associated with the API key used.

## Deployment

### Pre-Requisites

This template will work out of the box to give you host and container monitoring through numerous sources. If you also wish to monitor the Rancher server its-self, prometheus metrics need enabling for the server instance. Details on how to do this are listed below.

### Deployment Steps:

1. Select Prometheus from the community catalog.
2. Enter the IP Address of your Rancher server (used for accessing Ranchers own metrics, optional)
3. Click deploy.

### Usage

Once deployed, all the services should be green in Rancher and your new monitoring platform should be ready to use! Depending upon your specific implementation, you may need to open up the firewall for ports `9090` for Prometheus and `3000` for Grafana.

* Prometheus will now be available, running on port 9090. Have a play around with some of the data. For more information on Prometheus, check out their [documentation](https://prometheus.io/docs/introduction/overview/).
* Grafana will now be available, running on port `3000`. I've added a number of dashboards to help get you started. Authenticate with the default `admin/admin` account and password

### Alerting

Alerting can be achieved through making use of Prometheus's alert-manager container, I've not included it in the build at this stage but this can easily be plugged in.

### Enabling Prometheus metrics for the Rancher Server

1. Set the environment variable `CATTLE_PROMETHEUS_EXPORTER` to `true` for the Rancher server container.
2. Expose the metric port on the container as such `-p 9108:9108` so that prometheus can scrape the container

With those commands in-mind, starting a rancher server looks something akin to this:

`sudo docker run -d --restart=unless-stopped -e CATTLE_PROMETHEUS_EXPORTER=true -p 8080:8080 -p 9108:9108 rancher/server`

## How is it Done?

#### API Integration

We use API keys so that we can query Rancher over it's API for service/stack/host status's, an example of this is through the Prometheus Dashboard in Grafana. The actual process doing this is called the [prometheus-rancher-exporter](github.com/infinityworksltd/prometheus-rancher-exporter).

To provide access to the API, We make use of the following lables in Rancher:
```
      io.rancher.container.create_agent: true
      io.rancher.container.agent.role: environment
```

This API interaction means you can easily build service/stack status graphs for your key applications and expose them to a wider audience without needing to give them access to Rancher itsself.

## Troubleshooting

**Expected Data is missing?**
First, load up Prometheus on port 9090 and click on the status page at the top. This should show you the scrape status of all of your end-points.
If everything looks good there, have a look at grafana and perform a test of it's data sources to ensure connectivity is there.

**Rancher Server Metrics aren't showing in Grafana**
Have you followed the steps listed for 'Enabling Prometheus metrics for the Rancher Server'? If so, you might want to check that your mapping through the port correctly.

## Acknowledgements

* **James Barwell** - For all his efforts first introducing me to Prometheus and for the original rancher-api integration.
* **Jolyon Brown** - For his efforts in adding in further functionality to the API integration.
* **Rancher Labs** - For providing awesome support and details on how to effectivley montior Rancher server.

And of course, all the guys behind *Prometheus/Grafana* for making such great tools.

