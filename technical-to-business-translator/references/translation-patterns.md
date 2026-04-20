# Translation Patterns

## Common Tech → Business Mappings

| Technical Term | Business Translation |
|---------------|---------------------|
| Latency | How long users wait |
| Throughput | How many requests/bookings the system handles at once |
| Database migration | Moving data to a new structure (may require maintenance window) |
| API deprecation | An integration point we're retiring — partners need to update |
| Microservice | An independent piece of the platform that handles one specific job |
| Technical debt | Shortcuts taken earlier that now slow us down |
| Refactoring | Reorganizing the code to be healthier without changing what it does |
| Cache invalidation | Making sure users see fresh data instead of stale data |
| Race condition | Two things happening at once that shouldn't, causing unpredictable results |
| Memory leak | System gradually using more resources until it slows down or stops |
| CI/CD pipeline | The automated process that tests and deploys our code changes |
| Feature flag | A switch that lets us turn a feature on/off without deploying code |
| Rollback | Reverting to the previous version because something went wrong |
| Load balancing | Distributing traffic across multiple servers so no single one gets overwhelmed |
| SSL/TLS | Encryption that keeps data secure in transit |
| Rate limiting | Controlling how many requests a user/system can make to prevent overload |

## Framing Templates by Scenario

### Performance Improvement
- **Tech:** "Reduced P95 latency from 2.1s to 450ms on the booking endpoint"
- **Business:** "Booking confirmations now load 4x faster, reducing the chance users abandon mid-booking"

### Security Patch
- **Tech:** "Patched CVE-2026-XXXX in the auth middleware"
- **Business:** "Fixed a security vulnerability in our login system. No user data was exposed. The fix is already deployed."

### Infrastructure Change
- **Tech:** "Migrating from RDS to Aurora with read replicas"
- **Business:** "Upgrading our database to handle 3x more traffic and improve reliability. Requires a 2-hour maintenance window."

### Feature Flag Rollout
- **Tech:** "Rolling out behind a feature flag, 10% canary then full rollout"
- **Business:** "We're releasing this gradually — starting with 10% of users to catch any issues, then expanding to everyone."

### Breaking API Change
- **Tech:** "v2 endpoint changes the response schema; v1 deprecated with 90-day sunset"
- **Business:** "We're updating how our system communicates with partners. Partners have 90 days to update. Support available throughout."

## Business Dimension Checklist

When translating any technical topic, check each dimension:

- [ ] **User Impact** — Does this change what users see or experience?
- [ ] **Cost** — Does this cost money, time, or require trade-offs?
- [ ] **Risk** — What could go wrong? How likely? How bad?
- [ ] **Timeline** — When does this happen? How long?
- [ ] **Opportunity Cost** — What are we NOT doing while we do this?
- [ ] **Strategic Alignment** — How does this connect to business goals?
- [ ] **Revenue/Growth** — Could this affect the bottom line?

Not every dimension applies to every translation. Skip irrelevant ones.

## Anti-Patterns

**Don't hide risk.** "Minor infrastructure update" when it's a database migration is dishonest.

**Don't create false precision.** "This will reduce errors by 23.7%" when you're estimating is worse than "roughly a 20-25% improvement."

**Don't oversimplify to the point of being wrong.** "It's like moving to a new house" for a database migration loses important nuance (downtime, data risk).

**Don't blame technology.** "The system can't do that" should be "That would require X effort because of Y constraint."

**Don't use jargon as authority.** If you can't explain it simply, you may not understand it well enough.
