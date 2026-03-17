# Audience Framing Guides

## Clubs (Travel Club Stakeholders)

### What They Care About
- **HIGH:** Agent workflow efficiency, member experience, booking reliability, self-service capabilities
- **MEDIUM:** New features they can offer, integration stability, support responsiveness
- **LOW:** Internal architecture, team structure, engineering process

### How to Frame
- Lead with impact on their agents and members
- Use workflow language: "This means your agents will..." / "Members will see..."
- Be specific about timeline and what they need to do (if anything)
- Include a "What this means for you" section

### Vocabulary
- Use: agents, members, bookings, itineraries, portal, workflow, experience
- Avoid: endpoints, APIs, microservices, deployments, sprints, refactoring, tech debt

### Structure
- Short paragraphs, bullet points for action items
- Always end with: "Questions? Reach out to [contact]."

### Never Say
- "Technical debt" — they don't need to know about internal quality
- "We're refactoring" — they hear "we're not building features"
- "It's a backend change" — meaningless to them; tell them what changes in their experience
- "Sprint" or "velocity" — internal process language

---

## Leadership (Executives)

### What They Care About
- **HIGH:** Revenue impact, strategic progress, risk to the business, resource allocation
- **MEDIUM:** Timeline, competitive positioning, customer satisfaction
- **LOW:** Implementation details, specific technologies, individual tickets

### How to Frame
- Lead with business impact: revenue, risk, or strategic alignment
- Frame decisions as trade-offs with clear options
- Include ROI indicators where possible
- State clearly: is this an FYI or do they need to decide something?

### Vocabulary
- Use: ROI, strategic initiative, risk mitigation, customer impact, competitive advantage, SLA
- Avoid: function names, error codes, specific technologies, sprint numbers

### Structure
- Executive summary first (2-3 sentences)
- Supporting detail below for those who want it
- Bullet points for options/recommendations

### Never Say
- Technical jargon without translation
- "We need to refactor" without the business reason
- "It's complicated" — explain the complexity in business terms
- Vague timelines — give ranges, not "soon"

---

## Engineering (Other Technical Teams)

### What They Care About
- **HIGH:** API contract changes, integration impact, breaking changes, migration steps
- **MEDIUM:** Architecture decisions and rationale, performance characteristics
- **LOW:** Business rationale (they trust it's there), stakeholder communication

### How to Frame
- Lead with what changed and why from a technical perspective
- Be specific about API changes, versioning, deprecation timelines
- Include code examples or migration guides when relevant
- Don't over-explain things engineers already understand

### Vocabulary
- Use technical terms freely — this audience speaks the language
- Be precise about versions, dates, and breaking changes

### Structure
- Technical summary, then details
- Code blocks for API changes
- Migration checklist if action required

### Never Say
- Dumbed-down explanations of concepts they know
- "Simple change" when it has integration implications
- Business framing without the technical detail they need to act on

---

## Partners (External Technical Partners)

### What They Care About
- **HIGH:** API stability, contract changes, migration support, versioning
- **MEDIUM:** New capabilities they can leverage, performance improvements
- **LOW:** Internal architecture, business strategy, team changes

### How to Frame
- Lead with what's changing in the interface they use
- Be extremely specific about: what version, what date, what breaks
- Provide migration guide or link to documentation
- Include support contact for questions

### Vocabulary
- Use: API version, endpoint, request/response, backward compatibility, migration, sunset date
- Avoid: Internal team names, business strategy, internal tooling

### Structure
- Summary of change
- Impact assessment (breaking/non-breaking)
- Timeline with key dates
- Migration steps (if applicable)
- Support resources

### Never Say
- Internal business rationale they don't need
- "We're improving our architecture" without telling them what changes for them
- Anything that implies they need to rush without a clear deadline
