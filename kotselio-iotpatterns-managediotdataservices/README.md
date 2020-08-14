# IoT Patterns - Managed IoT Data Services

The Managed IoT Data Services is an architectural design (solution) pattern. It  has three models that can be used by IT departments of enterprises that deliver IoT services to their Operation Technology (OT) teams, or by an IoT service provider as three models to deliver value to their customers. This pattern focuses on the data collection and data consumption parts of an IoT platform or solution and not the device management aspect.


## Problem
In many cases, businesses (also known as Operations Technology) want to consume IoT data without managing the whole of the IoT infrastructure that needs to be in place. As such, businesses reach out to internal or external service providers to get support on parts of the underlying IoT infrastructure. We refer to those supporting parties as Service Providers that operate in the Information Technology domain, and the businesses as Service Consumers that operate in the Operations Technology domain.

The OT domain can have different maturity levels, hence they can be requesting to hand more or less responsibility over to the service provider. The more mature the OT domain is, the less responsibility they are willing to hand over to the IT domain and vice versa.

The IT domain needs to offer at least three shared service models to cover the needs of high, medium and low in maturity service consumers in a structured, repeatable and maintainable manner. Highly mature service consumers would typically require access to the IoT streaming data. Medium in maturity consumers require access to a managed data store. Lastly, less mature consumers require IoT data to land in their application domain, ready to be consumed with minimal or no processing.


## Context
The pattern documented here assumes that some responsibility of the overall IoT solution lies with the Operations Technology domain. As such, every other scenario (i.e. fully managed IoT services) falls out of the scope of this document. Shared Services with regards to Device Lifecycle Management also fall out of scope.

This pattern is intended for the shared services provider that needs to define an IoT delivery approach based on the requirements of the service consumer. It is assumed thtat the service provider has already assessed the maturity of the service consumer (see the Forces chapter).


## Forces

Note: the forces described in this chapter are being examined from the OT domain's viewpoint.

The maturity of the OT domain is defined as follows:
- **High Maturity** - the OT domain has skills on consuming streams of IoT data with regards to processing, enriching, applying business rules and custom logic, storing, and making data serviceable.
- **Medium Maturity** - the OT domain has skills on building the application logic on top of a managed data store, such as a database.
- **Low Maturity** - the OT domain has no IoT or application development skills and heavily depends on using ready-to-be-consumed data in 3rd party systems, e.g. SAP or MS Dynamics.

There is a linear correlation between Maturity and Complexity. The more mature the OT domain is, the more complexity it can handle. This is often observed in highly competitive environments, that the complexity of the solution is assumed by OT as it is considered a competitive advantage. In more streamlined scenarios complexity is often an outsourced activity. The competitive advantage argument can also be used as input for Trade-off analysis, such as buy/reuse vs. build decisions.

Maturity is a loosely used term in this pattern's context. It can relate to the availability of skills, or the decision to invest in acquiring skills or outsourcing it.

Value does not necessarily follow the same linear correlation, as the OT domain can derive huge value in both extremes: where complexity is assumed (building custom logic is a competitive advantage) and where it is outsourced (the use case is trivial and buy makes more sense than build).

Another force is Maintainability, which relates to the level of services that are managed and offered by the IT domain. Typically, the higher the maturity of the OT domain, the lower the level of Responsibility, hence less Maintenance efforts.

## Solution

The pattern describes three models. The end deliverable of each model is illustrated as a green color-coded component in the pattern diagram. The red dotted line separates the models into three lanes. The blue axes illustrate the forces (or qualities) that concern the pattern. The black dotted line illustrates the separation of the IT and the OT domains, with regards to ownership of the building blocks of each lane.

![alt text](/images/kotselio-iotpatterns-managed_services_solution.png "Managed IoT Services Pattern")

### Streaming Service Model
The service provider collects data from devices (D) and places the stream in a "Message Queue." The endpoint of the queue is exposed to the consumer, who in turn can process and route the stream further.

### Data Store Service Model
The service provider takes care of the data pipeline -i.e. ingesting, processing and storing- ending up landing the data in a data store (e.g. a database). The service consumer then is responsible for parsing and querying the store for relevant data (e.g. constructing reports in a web application).

### Integration Service Model
The service provider takes care of the data pipeline and populates ready-to-be-consumed events, such as notifications or alerts, into a third party system or application that can handle them (e.g. sending a push notification to a user's phone). The component that takes care of the integration of the IoT solution and third party solutions with regards to consumable IoT events is illustrated as the "Integrator" building block in the pattern diagram.

## Resulting Context

### Complexity
Each model has an associated complexity aspect, with the Integration Service Model having the less complexity for the service consumer, and the Streaming Service Model being the most complex. The "higher" the level of service offered by the IT domain, the less the complexity of the IoT solution blocks owned by the OT domain.

- The Streaming Service Model requires the OT domain to have IoT solution development skills, since they will be handling and processing streaming IoT data.
- The Data Store Service Model requires non-IoT related development skills, but will require application development skills. These are needed to establish integration with the data store and build query-based business logic and presentation layers (e.g. reports).
- The Integration Service Model does not require deep development skills, as the service provider will be feeding OT domain-owned applications with consumable data (e.g. alerts).

### Maintainability
From the OT domain viewpoint the more building blocks managed from the service provider, the less the maintainability needs for the service consumer. This is logical if one thinks about the OT domain "outsourcing" more parts of the end-to-end solution to the service provider, the less building blocks need to be maintained. This shifts the maintenance efforts towards the IT domain. Nevertheless, as with every outsourcing paradigm, shifting the delivery responsibility to a third party requires stronger change management, communication and service level agreements between the two parties.

- The Streaming Service Model requires the most maintenance effort for the OT domain, as only the data collection and queueing is handled by the IT domain. The rest of the IoT application -i.e. data processing, enrichment, business rules, storing, visualization, etc.- and its qualities -e.g. scalability, uptime, usability, performance, etc.- is under the responsibility of the OT domain.
- The Data Store Service Model requires medium maintenance effort for the OT domain, as the whole IoT-specific part of the solution is being handled by the IT domain. Application development skills are easier accessible, making the maintenance effort more manageable.
- The Integration Service Model requires the less maintenance effort for the OT domain, as consumption of data would be taken care of from the integrator component. There might be limited maintenance needs for ingesting, processing and presenting IoT data on the business application side.


### Considerations

If the solution is meant to serve multiple consumers, then changes in e.g. a namespace or an access token, could have an impact on other service consumers. It is advisable to associate the development lifecycle of shared components to the impact on the OT domain. For example, if all the devices are part of the same physical network, if there are multiple stream consumers per message queue, etc. On the other hand there might be limitations on shared services that might limit scalability, e.g. number of consumer groups per queue.

All high level shared services (i.e. Data Store and Integration services) require agreements between the IT and OT domains regarding e.g. data schemas, message formats, and message queue/bus mechanisms.

Lastly, in some cases mutliple models can be requested by the OT domain. For example, if there is the need to both land IoT data in a data store (Data Store Service Model) and at the same time create consumable events (Integration Service Model).

## Examples
For illustration purposes example scenarios will be discussed, using Azure building blocks for each model. 

![alt text](/images/kotselio-iotpatterns-managed_services_azure_example.png "Managed IoT Services Pattern - Azure")

### Streaming Service Model Example
Scenario: The Service Provider is collecting industrial machinery sensor data from a factory and is exporting it to a queue. The Process Control Department of the factory (i.e. the OT domain) is further processing the stream of sensor data and analyzing it against a machine learning algorithm that predicts machinery failure.

The sensor data is being collected through an Azure IoT Central Application and is exported to an Azure Event Hub queue. This is a managed service where the Service Provider is responsible for data being streamed and placed to the queue.

The Process Control Department needs the endpoint and the access token of the Azure Event Hub to be able to process the message stream using Azure Stream Analytics.

Result: the Process Control Department is mature enough to process IoT streaming data and holds a competitive advantage utilizing their machine learning algorithm to make optimal decisions for their opreations. They do have IoT development skills, but they outsource the IoT platform and the queing mechanism to the Service Provider and they only consume the stream of data, reducing some of the maintenance efforts and the complexity of their IoT solution.

References:

- Export data from IoT Central to an Event Hub: https://docs.microsoft.com/en-us/azure/iot-central/core/howto-use-data-export#create-an-event-hubs-destination
- Stream data from Event Hubs to your Stream Analytics Job: https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-define-inputs#stream-data-from-event-hubs


### Data Store Service Model Example
Scenario: The Service Provider is collecting consumption information from smart meters for electricity and water every 5 minutes. The average hourly consumption is being calculated and then recorded to a database. A smart home Product Development department (i.e. the OT domain) has built a web application where the meter owners can view statistics for their consumption. This web application is querying the database for the last hour's water and electricity consumption.

The smart meter readings are being collected through an Azure IoT Central Application and are exported to an Azure Event Hub queue. The readings are being processed in Azure Stream Analytics that calculates the hourly average per meter and records the result in a Cosmos DB. This is a managed service where the Service Provider is responsible for data being streamed, processed and recorded in a database.

The smart home Product Development department needs the endpoint and access credentials for the database to be able to perform queries according to their application logic, in this case through a web application.

Result: The maturity of the Product Development department is medium, as they have application development skills but not IoT application development skills. They outsource the IoT platform and the streaming data processing to the Service Provider and they are responsible for the application logic that queries data from the managed database. This allows the department to significantly reduce the maintenance efforts and the complexity of their overall solution and put more effort on other aspects focusing on the web application, such as reports and statistics, user experience, user management, etc.

References:
- Azure Stream Analytics windowing functions: https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-window-functions
- Azure Stream Analytics Average calculation: https://docs.microsoft.com/en-us/stream-analytics-query/avg-azure-stream-analytics 
- Azure Stream Analytics output to Azure Cosmos DB: https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-documentdb-output


### Integration Service Model Example
Scenario: The Service Provider is collecting the temperature sensor readings of shipped dairy products. The Supply Chain department of the dairy company (i.e. the OT domain) is expecting alerts to show up in their planning tool within their SAP environment, in case the temperature of the container of the shipped goods has exceeded a certain threshold.

The sensor readings are being collected via an Azure IoT Central Application and exported to an Azure Event Hub queue. The readings are being processed in Azure Stream Analytics and notifications in case of temperature exceeding the threshold value are being placed to an Azure Service Bus queue. An Azure Logic App is triggered by new messages being placed in the queue and the contents of the notification message is being sent to SAP using a Logic App SAP connector. The Service Provider needs the details of the SAP Application server to be able to send the notification messages. This is a managed service where the Service Provider is responsible for data being streamed, processed and sent to a busines application, such as SAP.

Result: The maturity of the Supply Chain department is low, as no IoT application development skills, nor application development skills are available. The department outsources the whole IoT solution to the Service Provider and they expect events to reach their SAP environment, in a ready-to-consume format. There is little maintenance responsibility for the Supply Chain department, as most of it lies with the Service Provider. The Supply Chain department is then only responsible on how the data lands on the SAP environment and how it is presented and further processed.

References:

- Process configurable threshold-based rules in Azure Stream Analytics: https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-threshold-based-rules
- Output data from Stream Analytics to Azure Service Bus queues: https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-define-outputs#service-bus-queues
- Exchange messages in the cloud by using Azure Logic Apps and Azure Service Bus: https://docs.microsoft.com/en-us/azure/connectors/connectors-create-api-servicebus
- Connect to SAP systems with Azure Logic Apps: https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-using-sap-connector


## Related Patterns

Lambda architecture: https://en.wikipedia.org/wiki/Lambda_architecture

IoT and Aggregation: https://azure.microsoft.com/en-us/blog/design-patterns-iot-and-aggregation/
