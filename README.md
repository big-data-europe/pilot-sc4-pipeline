# BDE SC4 Pilot Cycle 1

Bootstrap the pipeline for the SC4 pilot 1 on the BDE platform.

## Pilot 1 Description
The pilot fetches continuously the FCD data (Floating Car Data)  about taxies in Thessaloniki from CERTH web service. 
A Kafka Producer connects to the web service and sends the data into a Kafka topic. The data is consumed by a Flink job, 
enriched using a map matching algorithm, aggregated and finally stored into Elasticsearch. A visualization based on 
Kibana is used to visualize the aggregated data in a map (average speed in road segments per time windows).
  
## Pilot 1 Components
The table shows the frameworks and other components used to set up the pilot.

|Component | Images |
|----------|--------|
|Apache Zookeeper|[bde2020/zookeeper](https://hub.docker.com/r/bde2020/zookeeper/)|
|Apache Kafka|[bde2020/docker-kafka](https://hub.docker.com/r/bde2020/docker-kafka/)|
|FCD Kafka Producer|Dockerized version of [pilot-sc4-kafka-producer](https://github.com/big-data-europe/pilot-sc4-kafka-producer) (TBD)|
|Apache Flink|[bde2020/flink-master](https://hub.docker.com/r/bde2020/docker-kafka/) <br> [bde2020/flink-worker](https://hub.docker.com/r/bde2020/flink-worker/) <br> [bde2020/flink-submit](https://hub.docker.com/r/bde2020/flink-worker/)|
|Elasticsearch|NA|
|Rserve + MapMatching Algorithm|Dockerfile in [pilot-sc4-docker-r](https://github.com/big-data-europe/pilot-sc4-docker-r)|
|Kibana|NA|

All the components, frameworks, Flink job, Kafka producer, Rserve, must be provided as Docker images in order to be 
started in Docker containers within a Docker Swarm. Furthermore, all the Docker images must support the initialization
protocol by including the wait-for-step.sh, execute-step.sh, finish-step.sh scripts provided in the [docker-spark](https://github.com/big-data-europe/docker-spark/tree/master/base) base 
image in order to communicate with the initialization daemon.
 
## Pilot 1 Initialization
Some of the components used for the pilot depend on other components. As an example, the Kafka producer depends on 
the availability of a Kafka topic, the Flink job depends on the availability of the Rserve for the map matching and 
on Elasticsearch for the storage. Kafka itself depends on Zookeeper. The BDE platform provides a component, the 
[init daemon service](https://github.com/big-data-europe/mu-init-daemon-service), to execute a proper initialization.
The init daemon is already included in the docker-compose.yml file used for the pilot. The init daemon requires the 
information about the order in which the components have to be started. This information must be provided as an RDF 
file that can be created using the [Pipeline Builder](https://github.com/big-data-europe/app-pipeline-builder). The
RDF file must be included in the project as it will be imported by the initialization daemon.

The steps to start the pilot are 

1. Start Zookeeper
2. Set up Kafka and create a topic
3. Start the FCD Kafka producer
4. Set up Flink 
5. Start Rserve with the MapMatching algorithm
6. Set up Elasticsearch with the FCD schema
7. Submit the [FCD job](https://github.com/big-data-europe/pilot-sc4-flink-kafka-consumer) to Flink
8. Start Kibana  

### 1) Start Zookeeper
The pilot uses a customized Zookeeper image in order to use its own configuration file. The image uses the bde2020/zookeeper
image as a base. Use the Docker file in images/zookeeper to build the Zookeeper image used by the pilot.
 
### 2) Set up Kafka

### 3) Start the FCD Kafka Producer

## Configuration of the Dashboards Integrator UI
The Integrator UI shows all the existing component dashboards in a unified interface. For each dashboard a menu item is 
shown in the navigation bar at the top. The BDE CSS style will be applied automatically on the dashboards. The configuration 
of the Integrator UI is done through a JSON file.

## Running the pilot
```
$ git clone https://github.com/big-data-europe/pilot-sc4-pipeline.git
$ cd pilot-sc4-pipeline
$ docker-compose up -d
```
