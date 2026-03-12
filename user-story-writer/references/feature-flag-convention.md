# PostHog Feature Flag Convention

## Naming Pattern

```
{product}-{feature-description}
```

- **Format**: kebab-case
- **Product prefix**: required (see table below)
- **Feature description**: 2-4 words describing the capability
- **One-sentence description**: required per flag (used in PostHog flag metadata)

## Product Prefixes

Derived from existing Flipt production flags. Use the prefix matching the product area.

| Prefix | Product Area |
|--------|-------------|
| `activity` | Activities and excursions |
| `admin` | Admin panel and back-office |
| `agent` | Agent-facing tools and workflows |
| `car` | Car rentals |
| `cart` | Shopping cart and multi-item |
| `checkout` | Checkout and payment flows |
| `cruise` | Cruise bookings |
| `flight` | Flight bookings |
| `hotel` | Hotel bookings |
| `insurance` | Travel insurance and protection |
| `manual-booking` | Manual booking entry |
| `parks` | Theme parks and attractions |
| `prepack` | Pre-packaged vacations |
| `tour` | Tours and guided experiences |
| `vacation-rentals` | Vacation rental properties |
| `platform` | Cross-cutting / shared infrastructure |

## Migration from Flipt

Flipt used PascalCase with an "Enabled" suffix. PostHog flags drop the suffix (flags are inherently toggleable) and use kebab-case.

| Flipt (old) | PostHog (new) |
|-------------|---------------|
| `ActivityNewDetailsPageEnabled` | `activity-new-details-page` |
| `CheckoutMultiItemEnabled` | `checkout-multi-item` |
| `AgentReturnDetailsFormEnabled` | `agent-return-details-form` |

## Lifecycle

All feature flags are temporary rollout mechanisms. Every flag should be removed after the feature is stable in production. The story that introduces a flag must include a cleanup AC.
