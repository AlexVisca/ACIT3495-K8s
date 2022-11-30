# ACIT3495-Project: K8s Deployment

2022 (C) BCIT

## Application Scope
**Open Weather Service API** is built for weather monitoring agencies to provide weather information services to end-users. It provides an application service to compute raw data that has been collected, and analzes and processes the raw data into a user-friendly Application Programming Interface (API) for frontend applications and websites.

Open Weather Service provides the following functionality:

-  Exposes an API to send data over HTTP to a frontend with API calls.
-  Services are loosely coupled and scalable.

Open Weather Service works in the following steps: 

1.  Weather data of temperatures is deposited by an administrator.
2.  The temperature data is saved to a relational database.
3.  The minimum value, maximum value, and average value is computed.
4.  The computed values are saved in a non-relational database
5.  The computed data is served over HTTP to a frontend with REST API calls.

### User Interfaces
**Admin Client**
-   Weather Service portal for uploading raw temperature data. Designed for daily uploads.
-   Data can be uploaded as a Comma Separated Values (CSV) file or exported from a Microsoft Excel&copy; spreadsheet.
-   Saves raw data to relational database containers.
-   Requires Admin Authorisation.

**User Client**
-   Easy-to-use Weather app for end-users and website API calls.
-   Presents organised temperature information as a useful summary of minimum, maximum, and average values.
-   Reads post-processed data from a NoSQL datastore as JSON objects.
-   User account required for authorisation.

### Databases
Uses two different databases.
1.  MySQL for raw data. The data is divided into tables by datetime, and is designed to receive daily uploads.
2.  NoSQL(MongoDB) for computed data saved as a collection of Javascript Object Notation (JSON) and is designed for constant uptime read operations.

## Microservice Architecture Description
The application is divided into 6 microservices across the frontend and backend:

**Frontend**:
-   **Reverse Proxy and Authentication Service**: Serves a static webpage and manages authorisation and authentication.
-   **Admin Frontend Service**: Provides access for administators to upload CSV files with raw data. CSV files are uploaded as a POST request from the input form.
-   **User Frontend Service**: Serves weather data to end-users. Responds to GET requests from user clients with a JSON response of post-processed information.

**Backend**:
-   **MySQL Database**: Stores raw data in tables. Tables are created for each day.
-   **Analytics Service**: Processes raw data for statistics. The minimum value, maximum value, and average value is computed and saved as a JSON object.
-   **MongoDB Datastore**: Serves post-processed data to User frontend Service

### Horizontal Scalability Design

**Application Scalability**:
The frontend services are required to scale horizontally to reach a greater user base. In order to do this, not every part of of the application can scale well.

<img src="figure_1.png">Figure 1: Data collection and Analytics System Architecture</img>

The **User frontend** service can scale more than the other services because it has a smaller datastore with MongoDB which can handle scaling needs, and therefore can benefit from more replica sets in the Cluster. It also needs to reach users, which are the primary source of traffic. This is horizontally scaled by HTTP traffic to the API (Measured by CPU usage).

Tha **Admin frontend** does not need to scale as much, because there are not many administrators and the frontend service requires the MySQL database.

**Database Scalability**:
Databases were the main bottleneck for scaling. The MongoDB datastore is updated with post-processed data from the analytics server from the MySQL database cluster, which does not scale well. However, as mentioned previously, it is not required that the MySQL database scale as much as the MongoDB datastore. The Analytics service acts as an intermediary to balance the lower scaling needs of the MySQL database, and reach the higher scaling needs of the MongoDB datastore. This is the main point of failure in the application, and requires traffic-based load balancing to manage horizontal scaling needs.

## Repository
GitHub: https://github.com/AlexVisca/ACIT3495-K8s.git
