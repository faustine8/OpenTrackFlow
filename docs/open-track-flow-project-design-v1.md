# OpenTrackFlow Project Design v1

## 1. Project Positioning

OpenTrackFlow is a DCSA-first container tracking and traceability platform.

The project focuses on ingesting standardized shipment events, validating data quality, building
container timelines, calculating current tracking status, and publishing normalized tracking updates
to downstream systems.

This is a personal engineering project based on public standards and mock data. It does not use
proprietary company data, internal source code, or confidential business models.

## 2. Core Decision

OpenTrackFlow will use DCSA-compatible event structures as the canonical contract.

The project will not define a new shipping industry data standard. Instead, it will focus on the
engineering problems around standardized tracking data:

* event ingestion
* raw event persistence
* event normalization
* idempotency
* data quality validation
* timeline aggregation
* current status calculation
* webhook-based downstream delivery
* traceability and auditability

## 3. Why DCSA-first

DCSA is a public industry standard for container shipping data exchange.

Using DCSA as the canonical contract makes the project more credible and more extensible. If a real
DCSA-compatible data source is added later, only a new adapter should be required. The core platform
logic should remain stable.

The value of OpenTrackFlow is not to invent a better shipping model. The value is to implement a
reliable backend platform around a public standard.

## 4. What This Project Does

OpenTrackFlow will support:

* receiving tracking events
* storing raw inbound payloads
* converting source payloads into normalized DCSA-compatible events
* validating mandatory fields and event consistency
* detecting common data quality issues
* building container and shipment timelines
* calculating latest tracking status
* exposing query APIs
* supporting downstream subscriptions and webhook delivery in later phases

## 5. What This Project Does Not Do

OpenTrackFlow does not:

* claim to replace DCSA, GSBN, carriers, or commercial tracking platforms
* use real company data
* use internal business logic from any employer
* implement a full freight forwarding, booking, customs, or billing system
* claim full production readiness in the first version
* define a new global shipping data model

## 6. Recommended Architecture

The first version should be implemented as a modular monolith.

Recommended modules:

* adapter
* ingestion
* normalization
* quality
* timeline
* subscription
* webhook
* query
* common

The system can later evolve into microservices if the boundaries become stable.

## 7. Technology Stack

Initial stack:

* Java 25 LTS
* Spring Boot 4.0.x
* Spring Validation
* Spring Data MongoDB
* Redis
* Docker Compose
* OpenAPI / Swagger
* JUnit
* Testcontainers

Optional later additions:

* Kafka / RabbitMQ / RocketMQ
* PostgreSQL / MySQL
* Spring Cloud
* frontend dashboard

## 8. Core Data Models

### raw_tracking_event

Stores original inbound payloads.

Fields:

* id
* source
* sourceEventId
* payload
* receivedAt
* traceId
* status

### normalized_tracking_event

Stores DCSA-compatible normalized tracking events.

Fields:

* id
* rawEventId
* eventType
* eventClassifierCode
* eventDateTime
* equipmentReference
* transportDocumentReference
* carrierBookingReference
* location
* facility
* vessel
* voyage
* source
* normalizedPayload
* createdAt

### tracking_timeline_snapshot

Stores the current timeline view for a container or shipment.

Fields:

* id
* trackingKey
* trackingType
* equipmentReference
* transportDocumentReference
* currentStatus
* currentLocation
* currentVessel
* currentVoyage
* lastEventTime
* timelineEvents
* updatedAt

### data_quality_issue

Stores detected data quality problems.

Fields:

* id
* eventId
* trackingKey
* issueType
* severity
* description
* detectedAt
* resolved

Common issue types:

* MISSING_MANDATORY_FIELD
* WRONG_EVENT_SEQUENCE
* INVALID_LOCATION_CODE
* DUPLICATE_EVENT
* ACTUAL_DATE_CHANGED
* VESSEL_VOYAGE_INCONSISTENT
* UNKNOWN_EVENT_TYPE

### subscription

Stores downstream tracking subscriptions.

Fields:

* id
* subscriberName
* trackingKey
* trackingType
* callbackUrl
* status
* createdAt

### webhook_delivery_log

Stores outbound webhook delivery records.

Fields:

* id
* subscriptionId
* eventId
* callbackUrl
* requestPayload
* responseStatus
* responseBody
* attemptCount
* status
* nextRetryAt
* createdAt

## 9. MVP Scope

The first MVP should implement one complete vertical slice:

1. Accept a DCSA-compatible tracking event.
2. Store the raw event.
3. Normalize the event.
4. Validate mandatory fields.
5. Persist the normalized event.
6. Update the container timeline snapshot.
7. Expose an API to query the container timeline.

First MVP APIs:

* POST /api/v1/tracking-events
* GET /api/v1/containers/{containerNumber}/timeline
* GET /api/v1/events/{eventId}/trace

## 10. Later Roadmap

Phase 1: Basic ingestion and timeline

* raw event storage
* normalized event storage
* container timeline query

Phase 2: Data quality monitor

* mandatory field validation
* duplicate detection
* event sequence validation
* invalid location detection
* issue query API

Phase 3: Subscription and webhook

* subscription registration
* event matching
* webhook delivery
* retry
* delivery log

Phase 4: Demo dashboard

* timeline view
* event trace view
* data quality issue list
* webhook delivery status

## 11. Demo Scenarios

### Scenario 1: Normal container timeline

Input events:

* Gate in
* Loaded on vessel
* Vessel departure
* Vessel arrival
* Discharged
* Gate out

Expected result:

* timeline is sorted correctly
* current status is calculated
* latest event is visible

### Scenario 2: Missing mandatory field

Input event is missing required fields such as eventDateTime, location, or equipmentReference.

Expected result:

* data quality issue is detected
* issue severity is recorded
* event trace links raw event and normalized event

### Scenario 3: Wrong event sequence

Input events contain an impossible sequence, such as discharge before loading.

Expected result:

* sequence issue is detected
* timeline still preserves raw facts
* issue is visible in query API

### Scenario 4: Webhook failure and retry

A later phase will simulate downstream callback failure.

Expected result:

* failed delivery is logged
* retry is scheduled
* successful retry updates delivery status

## 13. Disclaimer

This project is based on public standards and mock data only.

It does not contain proprietary company data, internal source code, customer data, or confidential
business logic.
