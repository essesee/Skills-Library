# Travel Industry Domain Profile

## Industry Overview

The travel industry connects travelers with suppliers (airlines, hotels, car rental companies, cruise lines, tour operators) through a layered distribution network. Key intermediaries include Global Distribution Systems (GDSs), Online Travel Agencies (OTAs), Travel Management Companies (TMCs), and traditional travel agencies. Revenue flows through commissions, booking fees, markup, and negotiated rates.

The industry runs on decades-old infrastructure (GDS systems date to the 1960s) layered with modern APIs. Products are perishable (unsold seats/rooms expire), pricing is dynamic and opaque, and inventory is shared across channels — meaning availability and price can change between search and booking. AAA clubs operate as a hybrid: leisure travel agencies with membership-driven value propositions and negotiated supplier relationships.

## Core Domain Concepts

- **PNR (Passenger Name Record)** — The core booking record in GDS systems. Contains itinerary, traveler info, ticketing, and remarks. The single source of truth for a reservation.
- **GDS (Global Distribution System)** — Centralized platforms (Sabre, Amadeus, Travelport) that aggregate supplier inventory and enable search/book/manage across airlines, hotels, cars.
- **OTA (Online Travel Agency)** — Consumer-facing booking platforms (Expedia, Booking.com) that compete with traditional agency channels.
- **Segment** — One leg of a journey (e.g., SFO-LAX). A trip can have multiple segments across multiple suppliers.
- **Fare Rules** — Complex, supplier-specific conditions governing price, changes, cancellations, refunds, and penalties. Never assume fare rules are simple or uniform.
- **Rate Code / Negotiated Rate** — Special pricing tiers from suppliers, often tied to corporate accounts, membership programs (like AAA), or volume agreements.
- **Booking Flow** — Search > Quote > Book > Ticket/Confirm > Manage > Cancel/Refund. Each step can fail independently.
- **Ticketing** — Separate from booking. A reservation can exist without a ticket. Ticketing deadlines (TTLs) are enforced and missing them cancels the booking.
- **Void vs. Refund** — Voids happen same-day (free). Refunds happen after ticketing and may incur penalties. Conflating these is a common and costly mistake.
- **BSP (Billing and Settlement Plan)** — IATA's financial settlement system between airlines and travel agents. Governs how agencies report and pay for tickets.
- **ARC (Airlines Reporting Corporation)** — US equivalent of BSP. Manages accreditation, reporting, and settlement for US travel agencies.
- **NDC (New Distribution Capability)** — IATA's XML/API standard for airline retailing. Meant to replace GDS-centric distribution with direct airline connections. Adoption is uneven.
- **Itinerary** — The complete trip plan: all segments, all product types (air, hotel, car, etc.), all travelers. Must handle multi-supplier, multi-segment, multi-traveler complexity.
- **Traveler Profile** — Stored traveler data (loyalty numbers, preferences, passport info, TSA Known Traveler). Sensitive PII that requires careful handling.
- **Commission** — Revenue earned by the agency on a booking. Varies wildly by supplier, product type, and negotiated agreement. Can be percentage or flat.

## Established Patterns

### Data & Architecture
- **Supplier abstraction layer**: Products come from multiple suppliers with wildly different data formats. Successful systems normalize supplier data into a canonical model while preserving supplier-specific details in extension fields.
- **Async confirmation**: Bookings are not always instantly confirmed. Hotel, car, and cruise bookings may be "on request" with confirmation arriving hours or days later. Systems must handle pending states.
- **Price volatility handling**: Prices change between search and booking. Quote-and-hold patterns, fare locks, and price change notifications are standard. Never cache prices as if they're stable.
- **Multi-product itineraries**: A single trip often spans air + hotel + car + insurance + activities. These must be managed together (for the traveler) while being booked independently (per supplier).
- **Booking lifecycle state machine**: Search > Quote > Reserve > Confirm > Ticketed > Active > Completed / Cancelled / Refunded. Each state has valid transitions and invalid ones. Enforce the state machine.

### Business Processes
- **Mid-trip changes**: Travelers change plans. Rebooking, adding segments, upgrading — all mid-trip. Systems that only handle happy-path booking are incomplete.
- **Group bookings**: Different flow from individual. Blocks of inventory, name-later reservations, rooming lists, deposit schedules. Cannot be shoehorned into individual booking flows.
- **Queue-based workflows**: GDS systems use queues (numbered message boards) for itinerary changes, schedule alerts, ticketing reminders. Agent workflows revolve around queue processing.
- **Back-office reconciliation**: Matching bookings to supplier invoices, commission tracking, BSP/ARC reporting. Financial accuracy is non-negotiable.

### Standards & Protocols
- **IATA codes**: 3-letter airport codes (LAX), 2-letter airline codes (AA), city codes. Use IATA codes, not invented identifiers.
- **ISO 8601**: Date/time formats. Always include timezone. Travel spans timezones constantly.
- **ISO 4217**: Currency codes. Travel deals in multiple currencies per transaction.
- **OTA XML / OpenTravel**: Legacy but still prevalent schema standards for travel data exchange.
- **HTNG (Hotel Technology Next Generation)**: Hotel-specific integration standards.
- **IATA NDC**: Modern airline distribution API standard. JSON/XML. Replaces EDIFACT messaging.

## Anti-Patterns & Pitfalls

- **Treating booking as a single atomic operation** — Booking is a multi-step process where each step can fail. Partial bookings (air confirmed, hotel failed) are real and must be handled gracefully.
- **Flat pricing models** — Travel pricing is not "item has a price." It's base fare + taxes + fees + surcharges + markups, varying by traveler type, date, availability class, and channel. Flattening this loses critical information.
- **Ignoring fare rule complexity** — Every fare has rules governing changes, cancellations, minimum stay, advance purchase, blackout dates, and combinability. Systems that treat fares as interchangeable will produce invalid bookings.
- **Assuming real-time availability** — GDS availability is a cache, not a live feed. "Available" at search time may be gone at booking time. Always handle sell failures.
- **Single-currency assumption** — A single booking can involve USD for air, EUR for hotel, and GBP for car. Display currency, supplier currency, and settlement currency can all differ.
- **Conflating void and refund** — Voids are same-day, free, and simple. Refunds are post-ticketing, may take weeks, involve penalties, and require BSP/ARC processing. Mixing these up has financial consequences.
- **Name matching naivety** — Airline tickets require exact legal name matching. Suffixes, middle names, special characters, and transliteration all cause issues. "Close enough" doesn't work.
- **Ignoring supplier-specific quirks** — Each airline, hotel chain, and car company has idiosyncratic rules, error codes, and behaviors. A "generic supplier" abstraction that doesn't accommodate these will break.
- **Forgetting about time zones** — An itinerary from New York to Tokyo crosses multiple time zones. Departure and arrival times are local. Duration calculations must account for this. Displaying "3pm" without a timezone is a bug.
- **No offline/degraded mode** — GDS systems have outages. Suppliers go offline. A travel system with no degraded mode is fragile. Agents need to continue working during outages.

## Regulatory & Compliance

- **PCI DSS** — Payment Card Industry Data Security Standard. Travel agencies handle card data for bookings. PCI compliance is mandatory. Card data must not be logged, stored in plaintext, or transmitted insecurely.
- **DOT (Department of Transportation)** — US regulations on airline pricing transparency, baggage fee disclosure, tarmac delay rules, oversale compensation. Affects how prices and policies are displayed to consumers.
- **GDPR / Privacy** — Traveler profiles contain extensive PII (passport numbers, loyalty accounts, travel patterns). Data retention, consent, and right-to-deletion apply. Cross-border data transfer rules matter for international travel.
- **IATA Accreditation** — Agencies must maintain IATA/ARC accreditation to issue tickets. Accreditation requires financial standards, reporting compliance, and security standards.
- **TSA Secure Flight** — US requirement to collect and transmit traveler data (full name, DOB, gender, redress number) for flights touching the US. Non-compliance means travelers can't fly.
- **APIS (Advance Passenger Information System)** — International travel requires passport/visa data submitted to destination countries before departure. Missing APIS data causes boarding denial.
- **EU Package Travel Directive** — When selling combined travel products (flight + hotel), the seller takes on package organizer liability including insolvency protection. Affects how multi-product offerings are structured.

## Industry Systems & Integrations

- **Sabre** — Major GDS. SOAP/REST APIs. Sabre Red Workspace is the agent desktop. SynXis for hotel CRS. Primary GDS for many US agencies including AAA clubs.
- **Amadeus** — Largest GDS globally. Altea for airline PSS. REST APIs via Amadeus for Developers. Strong in European market.
- **Travelport** — GDS encompassing Apollo, Galileo, Worldspan legacy systems. Travelport+ is the modern platform.
- **NDC Aggregators** — Platforms that aggregate NDC connections across airlines (Travelfusion, Kyte, etc.). Simplify airline-direct integration.
- **Hotel Aggregators** — Platforms aggregating hotel inventory beyond GDS (Expedia Partner Solutions, Hotelbeds, Derbysoft). Different rate types and content.
- **Payment Processors** — Travel-specific payment handling: split payments across suppliers, virtual card generation, BSP/ARC settlement. Not generic e-commerce payments.
- **Insurance Providers** — Travel protection products integrated at point of sale. API-driven quoting and binding. Regulations vary by state/country.
- **Duty of Care / Traveler Tracking** — Systems like iJet, WorldAware, International SOS. Required for corporate travel to locate and communicate with travelers during emergencies.

## Evaluation Heuristics

### For PRDs
- Does it account for the full booking lifecycle, not just the happy path?
- Are cancellation, refund, and change flows explicitly addressed?
- Does it handle multi-segment, multi-supplier, multi-traveler scenarios?
- Are GDS dependencies and limitations acknowledged?
- Is pricing modeled with sufficient granularity (base + taxes + fees)?
- Are supplier-specific variations called out?
- Does it address what happens when a supplier is unavailable?

### For API Specs
- Does the data model use industry-standard identifiers (IATA codes, ISO currencies)?
- Are date/times timezone-aware?
- Does the booking API handle partial failures (some segments booked, others failed)?
- Is the API designed for the async nature of travel confirmations?
- Are error responses specific enough to distinguish supplier errors from system errors?
- Does it support the void/refund distinction?
- Can it handle multi-currency transactions?

### For Data Models
- Is the fare/rate structure granular enough (base, taxes, fees, markups as separate fields)?
- Does the itinerary model support mixed product types?
- Are traveler profiles separated from booking data (for reuse and PII management)?
- Is the booking state machine explicitly modeled with valid transitions?
- Can the model accommodate supplier-specific extension data?
- Are PNR locators and supplier confirmation numbers tracked separately?
- Is audit history maintained for compliance and dispute resolution?

### For UIs
- Can agents process queues efficiently (keyboard shortcuts, bulk actions)?
- Is pricing broken down transparently (base, taxes, fees visible)?
- Are fare rules accessible at decision points, not buried?
- Does the UI support comparing options across suppliers?
- Are booking status and next-required-actions always visible?
- Does it handle the agent workflow of serving a customer while managing GDS interactions?
- Are PII fields appropriately masked and access-controlled?
