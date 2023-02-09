# Event-driven architecture for digital shopfloor

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
Machine S ---> Rest API ---> 

Machine T ---> Rest API --->

Machine U ---> Rest API --->

Machine V ---> Rest API ---> External App ---> Rest API (Multiple) ---> External DB ---> Query ---> Data Analytics

Machine W ---> Rest API ---> 

Machine X ---> Rest API ---> 
...
Machine Z ---> Rest API ---> 
```

### Modern Approach
Event-driven architecture resolves this challenge at ease by leveraging asynchronous and lousely coupled architecture with source and sink connectors. There is no more client-server request-reponse scenario. Instead, the event-driven architecture promotes something called publisher and subscriber. The publisher will send its event (As long as it has the changed data available in its database) to the subscriber immediately in real-time.

```
Machine X (Publisher) with source connector ---> Asynchronous API (Event) ---> event streaming platform ---> Asynchronous API (Event) ---> External DB (Subscriber) with sink connector ---> Query ---> Data Analytics
```

As such, less demand for requirements of network and computing hardware is required. Most people leverages this for its modern distributed edge-cloud architecture, e.g. IoT, IIoT, autonoumous systems, drone, etc. This also works for unstructured data. Note that this architecture can be setting up with multiple publishers and single subscriber, single publisher and multiple subscribers, or multiple publishers and multiple subscribers.

There is a data pipeline available in the event streaming platform that we recommended. So, data reliability is guaranteed.

### Demo
Showing digital twin from the existing disconnected IIoT machine. The following diagram shows our proposed architecture and solutions on how to obtain the machine data for digital twin use case in event-driven architecture. It doesn't restricted to only production process data and machine data, it also could have metrics and logs to address system and cybersecurity observability.

![gtronics-demo-digital-twin](https://user-images.githubusercontent.com/107167692/217925435-a9e97193-e167-4d56-b107-65d371f18de0.png)

Fledge is the system of IIoT machine which has southbound plugins to connect to various sensors. We enabled a lathe simulator in it and to generate the readings. First, we will show it is in isolated scenario. The only choice to access its readings data is through its local dashboard.

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

This is the legacy dashboard design. The machine vendor designed what data should only their customers see. Customers are locked in. With the digital twin (After digitalizing the machine data), the customers now have the opportunity to customize the dashboard for different analytical purposes or any other downstream use case.

### Examples:

Example: Twinning exactly what data is available in the local machine. For details, please contact us for further actual demo.

![Screenshot from 2023-02-10 04-58-02](https://user-images.githubusercontent.com/107167692/217937683-59c3b12f-7e05-461a-8f56-d14fb6265c21.png)

Example: Customizing and zooming into specify context since indexing and record transformation are applied on the fly, then extending its time-series data for anomaly detection. For details, please contact us for further actual demo.

![Screenshot from 2023-02-10 04-59-27](https://user-images.githubusercontent.com/107167692/217937941-57dc6f9b-c789-4c11-88df-38dc18fc2732.png)

As such, by twinning or digitalizing all machines data, we could build a digital shopfloor. Light off deployment will be much more easier.

Other possible use cases:
1. Digital twin with AR/VR and customized outputs
2. Digital thread (Single source of thruth for entire i4.0 process) + Digital twin
3. Digital thread + microservices + new digital innovation (e.g. machine learning) + Digital twin
4. IT observability (Including database metrics and logs can be governed and audited easily) and SIEM (Cybersecurity analytics). This is important to protect the business and operation
5. Sustainability use case 
6. 5G deployment 

The following diagram shows the architecture and solutions with digital thread in front of indexing platform. Every microservices or subscriber could access the accurate and updated transactional data. Customers are allowed to choose various supported database if they want. Apache Spark is allowed for integration as well.

![gtronics-demo-digital-thread-and-digital-twin](https://user-images.githubusercontent.com/107167692/217938543-08e6552a-32e8-44dd-8eb1-c139ac0c0752.png)

### Advantages:
1. Zero licensing cost.
2. Opex model for majority workloads.
3. Applying for distributed edge-cloud architecture at ease, e.g. many plants.
4. Modern DevSecOps, MLOps, and NetOps approach.
5. Extending and integrating new digital initiatives easily.
6. etc

