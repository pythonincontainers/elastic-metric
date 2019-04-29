# elastic-metric
A simple Docker Compose file to run Elastic Metric monitoring of a single node Docker.

Download this rep with:

$ git clone https://github.com/pythonincontainers/elastic-metric

This repo files are based on following pages:
https://www.elastic.co/solutions/metrics
https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html
https://www.elastic.co/guide/en/kibana/current/docker.html
https://www.elastic.co/guide/en/beats/metricbeat/current/running-on-docker.html

The Docker Compose YML file creates following Runtime Objects:

- **monitoring** Overlay Network - all containers are attached to this Network to enable DNS-based Service Discovery
- **es_data** Volume - Elasticsearch Data is stored in this persistent Volume

**docker-compose.yml** Creates following Containers:

- **elasticsearch** - running Elasticsearch 7.0.0 Image from **docker.elastic.com** Registry; **discovery.node** Environment Variable sets Elasticsearch to Single Node mode; port 9200 is exposed to enable quick start-up monitoring with "http://localhost:9200/_cat/health" URL
- **kibana** - running Kibana 7.0.0 Image from **docker.elastic.co** Registry; **ELASTICSEARCH_URL** Environment Variable defines Elasticsearch Service URL; Kibana is accessed with "http://localhost:5601/" due to a Port Mapping of Container's port 5601
- **metric-setup** - Performs MetricBeat setup, as in "https://www.elastic.co/guide/en/beats/metricbeat/current/running-on-docker.html#_run_the_metricbeat_setup". This Container dies once the setup job is completed.
- **metricbeat** - running MetricBeat 7.0.0 Image from **docker.elastic.co**; Bind mounts **metricbeat.docker.yml** file from the Repository - unchanged configuration MetricBeat file; Bind mounts **/var/run/docker.sock** and two other folders of Docker Runtime (a MobyLinuxVM in case of Docker Desktop and Machine) in Read-Only mode; runs the Container as **root** (important!)

MetricBeat Configuration file "metricbeat.docker.yml" is explicitely curl'ed to a Repository folder with:

$ curl -L -O https://raw.githubusercontent.com/elastic/beats/7.0/deploy/docker/metricbeat.docker.yml

The file is not changed.

Bring up the multi-container deployment with:

$ docker-compose up -d

The deployment uses 3+GB of RAM. Please make sure to increase Docker VM resources to 4+GB and 2+CPU.
It takes 2-3 minutes to start-up all components. Please access "http://localhost:5601" to get Kibana window, go to **Dashboard** Section and select **[Metricbeat Docker] Overview ECS** Dashboard. 
Enjoy Monitoring!

More options in Elastic Documentation.

Tear down the Deployment with:

$ docker-compose down

Remove the **es_data** Volume to start from the begining.
