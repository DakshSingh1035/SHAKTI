SHAKTI - UNIFIED WOMEN'S SAFETY PLATFORM
========================================

PROJECT STATUS
--------------
This repository contains theoretical project documentation based on the
"Unified Women's Safety" technical blueprint. It describes a proposed product,
architecture, design, and implementation plan. It is not a working application,
firmware project, production system, certified device, or approved emergency
service integration.

WHAT THE PROJECT DOES
---------------------
Shakti is intended to provide an end-to-end personal safety workflow. It
connects an Android application, a smart SOS wristband, communication services,
location services, authorized public-space camera analytics, and human response
workflows.

The central idea is more than a panic button. When a possible emergency occurs,
the platform should:

1. Detect or receive an emergency signal.
2. Create and persist a unique incident record.
3. Give the wearer immediate local feedback.
4. Obtain the best available location and state its accuracy and age.
5. Try suitable delivery channels, including BLE, cellular, and optional
   fallback transports.
6. Retry when communication is temporarily unavailable.
7. Track server receipt separately from contact or operator acknowledgement.
8. Escalate unanswered incidents through an authorized response procedure.
9. Preserve permitted evidence with access control, hashes, retention rules,
   and an audit trail.

The platform is designed for situations where the phone, internet, or cellular
network may be unavailable. A failed remote delivery must remain visibly
pending; the system must never claim that a person or emergency agency was
notified without an acknowledgement from the receiving service.

MAIN USERS
----------

* Wearer: activates SOS, receives haptic feedback, and views incident status.
* Emergency contacts: receive actionable incident and location information.
* Authorized monitoring operators: review candidate events and coordinate
  responses.
* Emergency response agencies: receive information only through approved
  procedures or formally authorized integrations.
* System administrators: manage users, devices, cameras, policies, and audit
  records.
* Engineering and operations teams: test, monitor, and maintain the platform.

CORE FEATURES
-------------

Android application:
* Account and consent management.
* Emergency contact management.
* Manual SOS activation.
* Location and incident status.
* Wristband pairing through Bluetooth Low Energy (BLE).
* Notifications and delivery-state display.

SOS wristband:
* Physical SOS button with debounce and long-press protection.
* Immediate vibration acknowledgement.
* BLE communication with the paired Android phone.
* Persistent offline incident queue.
* Battery and device status reporting.
* Optional independent LTE and GNSS capabilities.
* Optional satellite or site-specific relay accessories, subject to validation.

Backend:
* Authenticated incident ingestion.
* Device and user management.
* Delivery attempts and acknowledgements.
* Notification orchestration and retry handling.
* Camera-event correlation and radius-based warnings.
* Evidence metadata and audit records.

Public-space intelligence:
* Analytics for authorized camera footage or camera metadata.
* Candidate event detection, not automatic proof of a crime.
* Location-aware warnings for opted-in users.
* Human review before consequential escalation.

Evidence:
* Permission-based capture.
* Protected object storage.
* SHA-256 integrity hashes and manifests.
* Access, export, review, and deletion audit events.
* Configurable retention and controlled disclosure.

HOW IT IS MADE
--------------
The proposed implementation is divided into cooperating layers:

1. Device layer - wristband electronics, button, haptics, battery, BLE,
   optional LTE/GNSS, and optional fallback radios.
2. Mobile layer - Android application that pairs with the band, manages user
   actions and permissions, obtains phone location, and displays status.
3. Backend layer - versioned APIs, incident orchestration, notification
   delivery, acknowledgement tracking, and audit logging.
4. AI layer - separate Python services for personal-safety or CCTV models.
5. Public-safety layer - authorized camera sources, operator review, and
   approved response procedures.
6. Evidence layer - encrypted storage, hashes, metadata, access control, and
   retention policies.

The reference proposes the following technology direction:

* Android: Kotlin and modern Android application components.
* Wristband: ESP32-class controller with BLE; modem and GNSS modules as
  separately validated hardware.
* Backend: Node.js, Express, PostgreSQL, and Prisma.
* AI workloads: Python services.
* Push notifications: a service such as Firebase Cloud Messaging, while
  keeping push delivery distinct from human acknowledgement.
* Evidence: private object storage with authenticated encrypted transport.

DESIGN AND ARCHITECTURE
-----------------------
The architecture is event-oriented and reliability-focused. Every SOS starts
as a durable local event before any network attempt. A device sequence number
and unique incident ID make retries idempotent and prevent duplicate incidents.

An illustrative SOS flow is:

SOS button
  -> local validation and haptic feedback
  -> unique incident ID and non-volatile commit
  -> location acquisition
  -> BLE delivery to phone and/or independent LTE delivery
  -> optional satellite or configured relay attempt
  -> backend receipt
  -> contact/operator acknowledgement
  -> closure or authorized escalation

Communication channels have different dependencies:

* BLE requires a nearby, powered, paired phone.
* LTE requires a compatible modem, SIM/eSIM, operator service, and coverage.
* GNSS can provide positioning without mobile data, but needs satellite
  visibility and may produce an uncertain or stale result.
* LoRa or other local relays require a deployed gateway and backhaul.
* Satellite messaging requires compatible hardware, service, antenna, sky view,
  power, and regulatory approval.

No channel is treated as guaranteed coverage. The fallback order must be
configurable for region, battery, available networks, and user policy.

IMPORTANT SAFETY AND PRIVACY PRINCIPLES
---------------------------------------

* A server receipt is not a contact acknowledgement or police dispatch.
* AI confidence is not proof that a crime occurred or that a person is guilty.
* Camera access is limited to authorized sources and documented purposes.
* Collect only the location, media, and camera data required for the stated
  safety purpose.
* Use explicit consent for location sharing and recording.
* Use unique device identity, encrypted transport, secure BLE pairing, role
  based access, multi-factor operator authentication, and audit logs.
* Do not hardcode production secrets in firmware.
* Avoid facial recognition and identity inference as default features.
* Apply Indian telecom, spectrum, privacy, CCTV, recording, and emergency
  service requirements through specialist legal and regulatory review.

IMPLEMENTATION BOUNDARY
-----------------------
The recommended delivery order is:

1. Validate manual SOS on the Android app.
2. Add BLE wristband pairing, haptics, and the offline queue.
3. Add backend receipt, retry, delivery status, and acknowledgement states.
4. Validate independent LTE/GNSS hardware and power behaviour.
5. Add evidence handling and security controls.
6. Prototype authorized CCTV analytics with recorded data and human review.
7. Consider advanced AI, satellite, relay, and agency integrations only after
   written authorization, testing, and operational readiness.

The first milestone must make the basic alert workflow reliable before adding
complex AI or public-space automation.

DOCUMENTATION FILES
-------------------

* ARCHITECTURE.txt - logical layers, components, data flow, and state model.
* DESIGN_AND_WORKFLOWS.txt - user experience, SOS workflows, and interfaces.
* IMPLEMENTATION_BLUEPRINT.txt - proposed technology, data model, APIs, and
  staged development plan.
* SECURITY_PRIVACY.txt - security, privacy, responsible-AI, and evidence rules.

LIMITATIONS
-----------
This documentation does not claim that the proposed hardware, Android app,
backend, AI models, CCTV access, satellite service, police API, or emergency
dispatch integration exists or has been approved. A production deployment
requires engineering validation, security testing, hardware certification,
privacy and legal review, service agreements, and controlled operational
pilots.
