# Platform Health Metric Definitions

## Insurance Metrics
- **Attachment rate:** % of bookings with insurance added, segmented by:
  - Product type (car, hotel, cruise, tour, air)
  - Funnel (agent console, consumer e-site, specific club)
  - Price tier (<$500, $500-$2000, $2000+)
- **Coverage gap:** Product combinations where insurance is not offered
- **Attachment trend:** WoW and YoY comparison of attachment rates

## Manual Booking Metrics
- **Volume:** Total manual bookings per period
- **Volume trend:** WoW and YoY comparison
- **Product mix:** Breakdown by product type

## Issue Metrics
- **Inflow rate:** New Jira tickets per day/week for platform queues
- **Creator concentration:** Tickets per known creator (Mindy, Dawn, etc.)
- **Cluster density:** Groups of similar tickets (via bug-consolidator)
- **Self-service candidates:** Tickets matching recurring patterns

## Anomaly Thresholds
- **Significant change:** >10% WoW deviation from trailing 4-week average
- **Spike:** >25% single-day deviation from 7-day average
- **Trend reversal:** 3+ consecutive weeks of directional change
