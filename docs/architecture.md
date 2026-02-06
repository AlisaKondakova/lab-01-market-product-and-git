## Product Choice

[Yandex Go](https://taxi.yandex.ru/ru_ru/)

Yandex Go is a mobile application of the Yandex company related to transportation and delivery. It was created on the basis of Yandex.Taxi.

## Main components

![Yandex Go Component Diagram](./diagrams/out/yandex-go/architecture-component/Component%20Diagram.svg)

[Yandex Go Component Diagram code](./diagrams/src/yandex-go/architecture-component.puml)

Yandex Pay - Yandex Pay is a Yandex payment service for fast and secure online and offline payments, which allows you to link bank cards and pay for purchases in one click without entering data, receiving cashback with Plus points, and also offers its own digital card with bonuses and savings accounts.

Yandex Maps - Yandex Maps is a Yandex search and information cartographic service. It was opened in 2004. There is a search on the map, information about traffic jams, tracking urban transport, laying routes and street panoramas of large and other cities.

Web App - web application (web app) is software that runs on a web browser, allowing users to access interactive services, tools, or data over the internet without requiring installation.

Mobile App - A mobile application or app is a computer program or software application designed to run on a mobile device such as a phone, tablet, or watch. Mobile applications often stand in contrast to desktop applications which are designed to run on desktop computers, and web applications which run in mobile web browsers rather than directly on the mobile device.

API Gateway - An API Gateway is a key component in system design, particularly in microservices architectures and modern web applications. It serves as a centralized entry point for managing and routing requests from clients to the appropriate microservices or backend services within a system.

## Data flow

![Yandex Go Sequence Diagram](./diagrams/out/yandex-go/architecture-sequence/Sequence%20Diagram.svg)

[Yandex Go Sequence Diagram code](./diagrams/src/yandex-go/architecture-sequence.puml)

Group Enter Destination (steps 8-16)

This group begins after the user is authenticated and ends when ride options are displayed. It involves calculating available ride classes, pricing, and route information based on the user’s entered pickup and drop-off locations.

User Action (Step 8)
The user enters a destination in the mobile app. The app collects:

coords_A (pickup location)

coords_B (destination)

RPC Call (Step 9)
The mobile app sends an RPC request estimateRide(coords_A, coords_B) to the API Gateway.

Calculation & Data Fetching (Steps 10–15)
The request is routed to the Maps & Pricing Service, which:

Fetches Route & Traffic Data (Step 11) from an External Maps API.

Receives Route Data (Step 12) including distance, ETA, and traffic conditions.

Fetches Tariff Rules (Step 13) from internal pricing tables or databases.

Checks Demand Surge (Step 14) based on current demand in the subgeographic zone.

Generates Ride Options (Step 15) such as Economy, Comfort, etc., with calculated prices.

Response to User (Step 16)
The Maps & Pricing Service returns the options to the mobile app via the API Gateway.
The app then displays:

Route on the map

Available ride classes

Estimated prices and ETAs

## Deployment

![Yandex Go Deployment Diagram](./diagrams/out/yandex-go/architecture-deployment/Deployment%20Diagram.svg)

[Yandex Go Deployment Diagram code](./diagrams/src/yandex-go/architecture-deployment.puml)

The Yandex Go system is deployed in a multi-tier cloud architecture where user-facing mobile and web apps on smartphones and browsers connect to a central Kubernetes cluster hosting all core microservices-Notification, User, Dispatch, Pricing, Payment, and Maps & Routing Services-which communicate internally via gRPC, TCP, and pub/sub messaging.

## Assumptions

I assume the mobile app first talks to the User Service to check if the person is logged in, before it lets them book a ride.

I assume the Dispatch Service is the one that finds and picks a nearby driver for a user after they confirm the booking.

I assume the Notification Service sends the "Your driver is here" alert to the user's phone after the Dispatch Service picks a driver.

## Open questions

How does the system prevent the same driver from being matched to multiple ride requests at the exact same moment?

What happens if the Payment Service says a user's card is valid during estimation, but then it fails when the driver is already assigned and on the way?
