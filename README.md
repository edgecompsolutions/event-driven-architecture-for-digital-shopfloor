Author: cr.ooi@edgecomp.my

### Introduction
This page demonstrates the concept and importance of building an event-driven architecture for digital shopfloor.

### Background
Conventionally, most people retrieve the data from a production machine database through REST API and then store them in an external database for subsequent downstream use cases, e.g. data analytics. For example,

```
Machine X ---> Rest API ---> External App ---> Rest API ---> External DB ---> Query ---> Data Analytics
```

The external application, external database, and data analytics platform could be hosted locally in edge server or they could be hosted in cloud.

However, a REST API is synchronous and tightly coupled. Each API request from the client (The client here refers to the external application) required to obtain a response from the server (The server here refers to the machine database) prior to get the data. It gets more worse when the REST API is scheduled in recurring batch process mode. The request-response processes and its amount of data in that batch task will create unnecessary latency and CPU workload to the server.

```
Server (Machine X) <--- Request <--- Client (External App)
Server (Machine X) ---> Response ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
Server (Machine X) <--- Request <--- Client (External App)
Server (Machine X) ---> Response ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
...
Server (Machine X) <--- Request <--- Client (External App)
Server (Machine X) ---> Response ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
Server (Machine X) ---> Upload Data ---> Client (External App)
```

Then, the external application will require to update into its external database. As a result, the slowness occurred. Note that the client at the following is referring as external application while the server at the following is referring as external database.

```
Client (External App) ---> Request ---> Server (External DB)
Client (External App) <--- Response <--- Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
Client (External App) ---> Request ---> Server (External DB)
Client (External App) <--- Response <--- Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
...
Client (External App) ---> Request ---> Server (External DB)
Client (External App) <--- Response <--- Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
Client (External App) ---> Upload Data ---> Server (External DB)
```

As such, when there are more machines and more data required to be retrieved, the demand for requirements of network and computing hardware will be higher. Imagine with the following scenario, the digital initiative will definitely fail.

```
Machine S ---> Rest API (Multiple) --->              ---> Rest API (Multiple) ---> 

Machine T ---> Rest API (Multiple) --->              ---> Rest API (Multiple) ---> 

Machine U ---> Rest API (Multiple) --->              ---> Rest API (Multiple) ---> 

Machine V ---> Rest API (Multiple) ---> External App ---> Rest API (Multiple) ---> External DB ---> Query ---> Data Analytics

Machine W ---> Rest API (Multiple) --->              ---> Rest API (Multiple) --->  

Machine X ---> Rest API (Multiple) --->              ---> Rest API (Multiple) --->  
...
Machine Z ---> Rest API (Multiple) --->              ---> Rest API (Multiple) --->  
```

### Modern Approach
Event-driven architecture resolves this challenge at ease by leveraging asynchronous and loosely coupled architecture with source and sink connectors. There is no more client-server request-reponse scenario. Instead, the event-driven architecture promotes something called publisher and subscriber. The publisher will send its event (As long as it has the changed data available in its database) to the subscriber immediately in real-time.

```
Machine X (Publisher) with source connector ---> Asynchronous API (Event) ---> event streaming platform ---> Asynchronous API (Event) ---> External DB (Subscriber) with sink connector ---> Query ---> Data Analytics
```

As such, less demand for requirements of network and computing hardware is required. Most people leverages this for its modern distributed edge-cloud architecture, e.g. IoT, IIoT, robotics arm, autonoumous systems, drone, etc. This also works for unstructured data. Note that this architecture can be setting up with multiple publishers and single subscriber, single publisher and multiple subscribers, or multiple publishers and multiple subscribers.

There is a data pipeline available in the event streaming platform that we recommended. So, data reliability is guaranteed.

### Demo
Showing digital twin from the existing disconnected IIoT machine. The following diagram shows our proposed architecture and solutions on how to obtain the machine data for digital twin use case in event-driven architecture. It doesn't restricted to only production process data and machine data, it also could have metrics and logs to address system and cybersecurity observability.

![demo-digital-twin](https://user-images.githubusercontent.com/107167692/219844040-6838a30a-4cf3-4105-afb6-473bb1132324.png)

Fledge is a good open source system for IIoT machine which it has southbound plugins to connect to various sensors. We enabled a lathe simulator in it and to generate the readings. First, we will show it is in isolated scenario. The only choice to access its readings data is through its local dashboard.

The following screen shot of the local dashboard shows the RPM and depth readings in each X process.

![Screenshot from 2023-02-10 04-14-56](https://user-images.githubusercontent.com/107167692/217927586-8c59f74f-e5ff-49c8-b953-76eee8c5074f.png)

The following screen shot of the local dashboard shows the state (Idle, spinning up, cutting, and spinning down) in each X process. 

![Screenshot from 2023-02-10 04-21-12](https://user-images.githubusercontent.com/107167692/217928232-a246a236-4941-4680-9e41-5b4f03fcc5f1.png)

The following screen shot of the local dashboard shows the current readings of the lathe machine.

![Screenshot from 2023-02-10 04-15-27](https://user-images.githubusercontent.com/107167692/217928471-1627bcdf-7623-4cbb-abbb-1804450631d3.png)

The following screen shot of the local dashboard shows the minimum, average, and maximum of current reading in entire time range.

![Screenshot from 2023-02-10 04-15-55](https://user-images.githubusercontent.com/107167692/217928825-1d33b2e7-5fa0-4f24-8378-ace8817c9b17.png)

The following screen shot of the local dashboard shows the temperature readings for each key component of the lathe machine such as tool, gearbox, headstock, tailstock, and motor.

![Screenshot from 2023-02-10 04-16-21](https://user-images.githubusercontent.com/107167692/217928925-1697727b-3bda-41b9-a355-79c96d3e5aaa.png)

The following screen shot of the local dashboard shows the minimum, average, and maximum of temperature readings for each key component in entire time range.

![Screenshot from 2023-02-10 04-16-39](https://user-images.githubusercontent.com/107167692/217929145-3b2c45f8-7a1f-47ef-a444-eef968e3dadc.png)

The following screen shot of the local dashboard shows the vibration readings such as RMS and frequency for the motor of the lathe machine.

![Screenshot from 2023-02-10 04-17-03](https://user-images.githubusercontent.com/107167692/217929300-85fb9235-f415-40cd-a8cb-183662cbcbe5.png)

The following screen shot of the local dashboard shows the minimum, average, and maximum of vibration reading for its motor in entire time range.

![Screenshot from 2023-02-10 04-17-20](https://user-images.githubusercontent.com/107167692/217929457-4998d6ef-3dc9-41ec-9aa7-ee7caf056419.png)

However, this is a simple and basic dashboard (Data visualization). The machine vendor designed what data should only their customers read. Customers are locked in. With the digital twin (After digitalizing the machine data), the customers now have the opportunity to customize the dashboard for different analytical purposes or any other downstream use case.

### Examples:

Example: Twinning exactly what data is available in the local machine. Eliminating data historian at on premise since it's available at here now and can be archived to cloud based external storage. For details, please contact us for further actual demo.

![Screenshot from 2023-02-10 04-58-02](https://user-images.githubusercontent.com/107167692/217937683-59c3b12f-7e05-461a-8f56-d14fb6265c21.png)

Example: Customizing and zooming into specify context since filtering, record transformation, parsing, and indexing are applied on the fly, then extending its time-series data for anomaly detection. For details, please contact us for further actual demo.

![Screenshot from 2023-02-10 04-59-27](https://user-images.githubusercontent.com/107167692/217937941-57dc6f9b-c789-4c11-88df-38dc18fc2732.png)

As such, by twinning or digitalizing all machines data, we could build a digital shopfloor. Light off deployment will be much more easier.

Other possible use cases:
1. Digital twin with AR/VR and customized outputs
2. Digital thread (Single source of truth for end-to-end i4.0 process data) + Digital twin
3. Digital thread + microservices + new digital innovation (e.g. machine learning) + Digital twin
4. IT observability (Including master database metrics and logs can be governed and audited easily) and SIEM (Cybersecurity analytics). Note: This is important to protect the business and operation, any unauthorized access to the master database will be instantly detected 
5. Search platform 
6. Sustainability use case 
7. 5G deployment 

The following diagram shows the architecture and solutions with digital thread in front of indexing platform. Normalizing data can be performed. Every microservices or subscriber could access the accurate and updated transactional data. Customers are allowed to choose various supported database if they want. Apache Spark is allowed for integration as well.

![demo-digital-thread-and-digital-twin](https://user-images.githubusercontent.com/107167692/219844032-54555461-0f2a-4be2-b1c5-6eadcf23b6db.png)

### Advantages:
1. Open source, open standard, and hardware/cloud vendor agnostic. Large community.
2. Zero licensing cost. Commercial support is available as an option.
3. Opex model for majority workloads.
4. Applying for distributed edge-cloud architecture at ease, e.g. many machines, many plants, etc
5. Modern DevSecOps, MLOps, and NetOps approach.
6. Extending and integrating new digital initiatives easily.
7. etc

### Additional:

![demo-additional](https://user-images.githubusercontent.com/107167692/219844020-ede95d44-51dd-4471-8035-3da6aa49b954.png)

If MQTT has been promoted as a standard of real-time messaging protocol in the current shop floor, we could go for the above architecture to integrate Kafka with the MQTT broker. That way, we could get the best of breed solutions between shop floor and enterprise applications architecture. Most well established manufacturing have this similar architecture. In this case, there isn't necessary to have edge node to collect the data, metrics, and logs from the shop floor (Like the diagram we have shown in demo diagram). Take note that the public specifications of MQTT is not really clear other than transport layer. This resulted different MQTT brokers might have different features or capabilities. You might want to have a look on this [link](https://mqtt.org/software/). It is better to perform due diligence to assess is it necessary to have MQTT architecture in your manufacturing environment if your machine already has an internal database that is ready to tap on. Blindly following the trend of the market could result in overhead and re-architecture risks.

![demo-recap-1](https://user-images.githubusercontent.com/107167692/219844140-dd9c251a-2b48-44c5-9b7a-a98490f529bc.png)

Let's recap further on the edge node. The above diagram shows the best architecture if the edge machine vendor could redeploy their machine applications in containerized platform. This gives the easier architecture for orchestrating and operating the machine application in DevOps approach.

![demo-recap-2](https://user-images.githubusercontent.com/107167692/219846040-af47c569-16bc-4056-b2c6-e2a3191f7e56.png)

If the edge machine vendor already upgraded their applications (They might just deployed it as physical host deployment with an edge device) but still in disconnected architecture, we could have an edge node (It is best to leverage an edge server or a cluster of edge servers for this) likes the above diagram to retrieve the data in real-time. However, we might not be able to retrieve the host metrics and logs, unless they enabled in their edge devices or they have those metrics and logs in the database. 

![demo-recap-3](https://user-images.githubusercontent.com/107167692/219844475-8c40ddab-1efb-41cf-86a7-45ba0845ece9.png)

The above diagram shows how an edge node could simply scale to access multiple edge machine's data. Last but not least, event-driven architecture is reliable, flexible, and scalable. It eliminates many challenges when going for digital and helps to accelerate the digital innovations that could bring up the business advantages.

### Case Studies:

> Obtained the following info through ChatGPT :P

- Here are some examples of case study who leverage Kafka as its core in the manufacturing industry to build their digital platforms.

**Audi:** Audi, a German automobile manufacturer, uses Kafka to collect data from over 400 machines in its body shop. The data collected includes information on the status of each machine, the number of parts produced, and any errors or downtime. This data is used to optimize the manufacturing process, increase efficiency, and reduce downtime.

**Schneider Electric:** Schneider Electric, a French multinational company that specializes in energy management and automation solutions, uses Kafka to process data from industrial equipment and sensors in real-time. The data is used to monitor equipment performance, predict maintenance needs, and optimize energy usage.

**GE Digital:** GE Digital, a subsidiary of General Electric that provides software and services for the industrial internet, uses Kafka to collect and process data from industrial equipment and sensors. The data is used to monitor equipment performance, detect anomalies, and optimize maintenance schedules.

**Bosch:** Bosch, a German engineering and technology company, uses Kafka to collect and process data from its manufacturing plants. The data is used to monitor production performance, identify bottlenecks, and optimize production schedules.

**Pirelli:** Pirelli, an Italian multinational company that specializes in the production of high-performance tires, uses Kafka to collect data from its tire production lines. The data is used to monitor production performance, identify defects, and optimize the manufacturing process.

- Here are some examples of case study who leverage OpenSearch (ElasticSearch) in the manufacturing industry for their digital innovations.

**Caterpillar:** Caterpillar, an American manufacturer of construction and mining equipment, uses Elasticsearch to analyze data from its machines in real-time. The data collected includes information on machine performance, fuel consumption, and maintenance needs. Elasticsearch enables Caterpillar to quickly identify and address issues, optimize machine performance, and reduce downtime.

**Michelin:** Michelin, a French multinational tire manufacturer, uses OpenSearch to centralize its data from various sources, such as tire pressure sensors, GPS systems, and weather forecasts. The data is used to monitor tire performance, predict maintenance needs, and optimize routes and logistics.

**SKF:** SKF, a Swedish manufacturer of bearings and lubrication systems, uses Elasticsearch to analyze data from its machines and sensors. The data collected includes information on bearing temperature, vibration, and wear. Elasticsearch enables SKF to quickly detect and diagnose issues, optimize maintenance schedules, and improve product quality.

**GE Aviation:** GE Aviation, an American aircraft engine manufacturer, uses Elasticsearch to centralize its data from various sources, such as flight data, engine performance data, and maintenance logs. The data is used to monitor engine performance, predict maintenance needs, and optimize flight routes.

**Siemens:** Siemens, a German multinational conglomerate, uses Elasticsearch to analyze data from its manufacturing processes. The data collected includes information on machine performance, energy usage, and production rates. Elasticsearch enables Siemens to quickly identify and address issues, optimize production schedules, and reduce downtime.

- Here are some examples of case study who leverage MQTT broker and Kafka together in the manufacturing industry.

**BMW:** BMW, a German automobile manufacturer, uses an MQTT broker and Kafka together to collect data from its vehicles in real-time. The data collected includes information on vehicle performance, fuel consumption, and driver behavior. The data is transmitted from the vehicles to the MQTT broker, where it is then ingested into Kafka for processing and analysis. This enables BMW to monitor vehicle performance, optimize maintenance schedules, and improve product quality.

**Jabil:** Jabil, a global manufacturing services company, uses an MQTT broker and Kafka together to collect data from its machines and sensors in real-time. The data collected includes information on machine performance, energy usage, and production rates. The data is transmitted from the machines and sensors to the MQTT broker, where it is then ingested into Kafka for processing and analysis. This enables Jabil to optimize production schedules, reduce downtime, and improve product quality.

**General Motors:** General Motors, an American automobile manufacturer, uses an MQTT broker and Kafka together to collect data from its manufacturing processes in real-time. The data collected includes information on machine performance, quality control, and logistics. The data is transmitted from the manufacturing processes to the MQTT broker, where it is then ingested into Kafka for processing and analysis. This enables General Motors to optimize production schedules, reduce waste, and improve product quality.

**Schneider Electric:** Schneider Electric, a French multinational company that specializes in energy management and automation solutions, uses an MQTT broker and Kafka together to collect data from its industrial equipment and sensors in real-time. The data collected includes information on equipment performance, energy usage, and environmental conditions. The data is transmitted from the equipment and sensors to the MQTT broker, where it is then ingested into Kafka for processing and analysis. This enables Schneider Electric to monitor equipment performance, predict maintenance needs, and optimize energy usage.
