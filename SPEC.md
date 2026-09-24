# Property Portal feature specification

This document defines product behavior and acceptance scope. It may change as the product evolves. Durable engineering boundaries and technology choices belong in [AGENTS.md](AGENTS.md).

## Purpose and goals

Build a web-first property marketplace for buying, selling, and renting real estate in Myanmar. The first release supports Yangon and Mandalay.

The product should let visitors discover properties, let owners and agents publish and manage listings, let buyers and renters save/compare/enquire, and give staff and admins moderation and platform-operation tools. Launch with realistic sample data for Yangon and Mandalay.

## Initial scope

Transaction types are Buy, Sell (seller-side workflow; public listings use Buy), and Rent. Categories are Land, Apartment/condominium, House, Condo unit, Commercial property, Office, Shop/retail, Warehouse, Industrial property, Room/shared accommodation, and Other. Categories and category attributes are data-driven.

Locations are hierarchical: city, township, and optional ward/neighborhood. Listings reference a location ID. The model must support future Myanmar cities and eventual additional countries without a schema redesign.

## Users and permissions

Roles are additive and enforced server-side:

- Visitor: browse, search, filter, inspect listings, and submit enquiries.
- Buyer/Renter: visitor actions plus profile, saved listings, saved searches, enquiry history, and viewing requests.
- Owner: create/manage listings, upload media, receive/respond to enquiries, and view listing performance.
- Agent: manage assigned/owned listings, maintain an agency profile, and receive/respond to enquiries.
- Staff: explicitly granted moderation and operational workflows.
- Admin: platform administration, roles, taxonomy/locations, audits, and staff permissions.

## Core journeys

1. A visitor searches for an apartment for rent in Yangon, filters results, opens a listing, and contacts the owner/agent.
2. A buyer saves and compares listings.
3. An owner creates a draft, adds photos and location, submits it for review, and publishes after approval.
4. An agent manages listings and responds to dashboard enquiries.
5. Staff reviews a submitted listing, requests changes or rejects it, and records the reason.
6. An admin adds a location, updates categories, assigns staff permissions, and audits important changes.

## Functional requirements

### Discovery

Provide responsive list/grid search with optional map-ready location data. Support filters for transaction type, category, city, township, price, bedrooms, bathrooms, floor/land area, furnishing, parking, amenities, status, and date posted. Support relevance, newest, price, and area sorting; URL-addressable search state; pagination or cursor pagination; empty states; and clear filter controls.

Listing detail includes gallery, title, price, location, attributes, description, amenities, contact CTA, owner/agent summary, verification status, related listings, and report action.

### Listing management

Support draft, submitted-for-review, changes-requested, approved/published, paused, sold/rented, rejected, and archived states. Required fields vary by category and transaction type. Media supports ordering, cover image, and captions, with future video/floor-plan support. Each listing has a stable ID, public slug, verification flag, and moderation history; private moderation notes are never public.

### Engagement

Support saving/unsaving listings, saved searches, enquiries with message/contact preference/status (`new`, `in_progress`, `responded`, `closed`, `spam`), and optional viewing requests. Record in-app notifications; email/SMS/push are future adapters and are not launch dependencies.

### Administration

Provide listing moderation with confirmation for bulk-safe actions, user/role/agency/category/amenity/location management, abuse reports, audit logs for security-sensitive and administrative changes, and dashboard metrics for active listings, pending moderation, enquiries, users, and city/category distribution.

## Logical data model

Core entities are `User`, `Role`, `Permission`, `UserRole`, `Profile`, `Agency`, `AgencyMember`, `Location`, `PropertyCategory`, `Amenity`, `Listing`, `ListingAmenity`, `ListingAttribute`, `ListingMedia`, `SavedListing`, `SavedSearch`, `Enquiry`, `ViewingRequest`, `ModerationReview`, `Report`, `AuditLog`, and `Notification`.

Listings include title, slug, description, transaction type, category, status, owner/agent, location, price, currency, negotiability, bedrooms, bathrooms, areas/units, furnishing, parking, contact visibility, publication timestamps, and verification metadata.

## Initial API surface

- Auth: `POST /auth/register`, `/auth/login`, `/auth/refresh`, `/auth/logout`, and `GET /me`.
- Listings: list/create/detail/update, submit, and pause endpoints.
- Saves: save/unsave listings, saved-listing retrieval, and saved-search CRUD.
- Engagement: listing enquiries, enquiry updates, and viewing requests.
- Taxonomy: locations, categories, and amenities.
- Dashboards: owner/agent listings and enquiries.
- Operations: staff/admin moderation, users, taxonomy, locations, reports, and audits.

Pagination, filtering, sorting, validation, authentication, and authorization errors must have consistent documented behavior.

## Seed and acceptance requirements

The repeatable seed should include development-only accounts for all roles; at least 100 realistic listings across Yangon and Mandalay, categories, transactions, prices, areas, amenities, statuses, owners, agents, agencies, media placeholders, saves, enquiries, and moderation records; and enough township/neighborhood variation to exercise filters.

The web MVP is complete when visitors can find and inspect buy/rent listings in Yangon and Mandalay; role-limited actions and records are enforced; owners/agents can submit through moderation; buyers/renters can save and enquire; staff can moderate with reasons; admins can manage locations/categories; seed data exercises the workflows; automated checks pass; and primary journeys work on desktop and narrow viewports.

## Delivery phases

- Phase 0: foundation, contract, schema, auth, seed strategy, design tokens, and CI.
- Phase 1: web MVP and core moderation/admin workflows.
- Phase 1.5: accessibility, performance, audits, abuse reporting, storage adapter, OpenAPI, backups, observability, and deployment readiness.
- Phase 2: Expo mobile app for high-value discovery, saves, enquiries, notifications, and listing management.
- Phase 3: more cities, maps/search, verified agencies, analytics, communications, multilingual content, and eventual production database migration.
