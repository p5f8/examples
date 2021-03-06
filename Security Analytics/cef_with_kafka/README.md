# CEF, Kafka and Logstash

This example provides the supporting fiels to the blog post [Integrating the Elastic Stack with ArcSight SIEM - Part 3](https://www.elastic.co/blog/integrating-elastic-stack-with-arcsight-siem-part-3).

This provides a working example of scaling an ArcSight-Elasticsearch architecture. A full architectural description can be found in the above blog post.

#### Versions

Example has been tested in following versions:

- Elasticsearch 5.6
- Logstash 5.6
- Kibana 5.6
- Kafka 0.10.1.0

### Elastic Stack - Manual Installation & Setup

* Follow the [Installation & Setup Guide](https://github.com/elastic/examples/blob/master/Installation%20and%20Setup.md) to install and test the Elastic Stack (*you can skip this step if you have a working installation of the Elastic Stack,*)

* Run Elasticsearch & Kibana
  ```shell
    <path_to_elasticsearch_root_dir>/bin/elasticsearch
    <path_to_kibana_root_dir>/bin/kibana
    ```

* Check that Elasticsearch and Kibana are up and running.
  - Open `localhost:9200` in web browser -- should return status code 200
  - Open `localhost:5601` in web browser -- should display Kibana UI.

  **Note:** By default, Elasticsearch runs on port 9200, and Kibana run on ports 5601. If you changed the default ports, change   the above calls to use appropriate ports.

* Download and install Logstash as described [here](https://www.elastic.co/guide/en/logstash/5.1/installing-logstash.html#installing-binary). Do not start Logstash.

### Elastic Stack - Docker Installation & Setup

A [docker-compose.yml](https://github.com/elastic/examples/blob/master/Security%20Analytics/cef_with_kafka/docker-compose.yml) file is provided to recreate in instance of the Elastic Stack (Elasticsearch and Kibana). This assumes:

* You have [Docker Engine](https://docs.docker.com/engine/installation/) installed.
* Your host meets the [prerequisites](https://www.elastic.co/guide/en/elasticsearch/reference/5.1/docker.html#docker-cli-run-prod-mode).
* If you are on Linux, that [docker-compose](https://github.com/docker/compose/releases/latest) is installed.

Additionally a [docker file](https://github.com/elastic/examples/blob/master/Security%20Analytics/cef_with_kafka/logstash/Dockerfile) is provided for ingestion of CEF data with Logstash.  This Logstash instance reads data from Kafka.

### Kafka - Installation & Setup

The following assumes you have a working [Kafka Installation](https://kafka.apache.org/quickstart). For those using docker a Dockerfile for Kafka is provided as part of the example [here](https://github.com/elastic/examples/tree/master/Security%20Analytics/cef_with_kafka/). 


### Configure & Start Logstash

If using the docker file described above, the following can be ignored.

Download the following files to the **root installation** directory of Logstash:

- [logstash/pipeline/logstash.conf](https://github.com/elastic/examples/blob/master/Security%20Analytics/cef_with_kafka/logstash/pipeline/logstash.conf) - Logstash config for ingesting CEF data into Elasticsearch
- [logstash/pipeline/cef_template.json](https://github.com/elastic/examples/blob/master/Security%20Analytics/cef_with_kafka/logstash/pipeline/cef_template.json)` - CEF template for custom mapping of fields

Unfortunately, Github does not provide a convenient one-click option to download entire contents of a subfolder in a repo. Use sample code provided below to download the required files to a local directory:

```
wget https://raw.githubusercontent.com/elastic/examples/master/Security%20Analytics/cef_with_kafka/logstash/pipeline/logstash.conf
wget https://raw.githubusercontent.com/elastic/examples/master/Security%20Analytics/cef_with_kafka/logstash/pipeline/cef_template.json
```

Start Logstash from the commnad line as described [here](https://www.elastic.co/guide/en/logstash/5.1/running-logstash-command-line.html), using the configuration file download above.

### Configure ArcSight

1. Run the command `<installdir>\current\bin\arcsight agentsetup`
1. Select `Yes` to start the `wizard mode`
1. Select `I want to add/remove/modify ArcSight Manager destinations`
1. Select `add new destination`
1. Select `Event Broker (CEF Kafka)`
1. Add the information of the Kafka server and port you used in the docker-compose for the environments variable `KAFKA_ADVERTISED_HOST_NAME` and `KAFKA_ADVERTISED_PORT`
1. Add the Topic name `cef`

### Visualize data in Kibana

Download the [dashboard.json](https://github.com/elastic/examples/blob/master/Security%20Analytics/cef_with_kafka/dashboard.json) file provided e.g.

```
wget https://raw.githubusercontent.com/elastic/examples/master/Security%20Analytics/cef_with_kafka/dashboard.json
```

* Access Kibana by going to `http://localhost:5601` in a web browser
* Connect Kibana to the `cef-*` index in Elasticsearch
    * Click the **Management** tab >> **Index Patterns** tab >> **Add New**. Specify `cef-*` as the index pattern name and click **Create** to define the index pattern with the field @timestamp
* Load sample dashboard into Kibana
    * Click the **Management** tab >> **Saved Objects** tab >> **Import**, and select `dashboard.json`
* Open dashboard
    * Click on **Dashboard****** tab and open `FW-Dashboard` dashboard

Voila! You should see the following dashboards. Enjoy!
![Kibana Dashboard Screenshot](https://github.com/elastic/examples/blob/master/Common%20Data%20Formats/cef/cef_dashboard.png?raw=true)

### We would love your feedback!
If you found this example helpful and would like to see more such Getting Started examples for other standard formats, we would love to hear from you. If you would like to contribute examples to this repo, we'd love that too!
