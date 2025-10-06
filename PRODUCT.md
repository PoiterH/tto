# Product Specification

## Overview

The Tournament Tipping Orchestrator (TTO) is a web application designed to streamline and automate the process of running group tipping competitions for major tournaments (e.g., World Cup, major eSports tournaments, bracket-style championships).



Motivation (Meri's "why")

This project serves two primary purposes:

    A Tool for Self: To create a robust, reliable, and automated solution for running personal tipping competitions, eliminating the need for manual spreadsheet management.
    Side Project Potential: To build a scalable platform that could potentially be offered as a service or business in the future.

Core Agentic Functionality

The application will be driven by two primary, reasoning-enabled tools (agents) with sub agents that handle different aspects of the competition lifecycle:

1. Tool: Competition Orchestrator (CompOrchestrator)

2. Tool: Competition Manager (CompManager)




## Problem Statement
The core focus of this project is to develop intelligent, agentic loops that handle the administrative burden—from ingesting tournament data to managing user picks, processing results, and updating the leaderboard—leaving the admin with minimal manual intervention. The ultimate prize for users is social bragging rights amongst friends.

## Target Users
Three levels of users.
1. Power/SiteAdmin: users who oversee the entire application
2. CohortAdmin: these users are per group admins who will be responsible for managing which tournaments the users within their cohort can access. 
3. End users who login and add their tips etc for the cohorts they have access to.

## Core Features

### MVP (Minimum Viable Product)
- [ ] Feature 1: CompOrchestrator: Ingesting and managing the raw tournament structure and results data.
    Initial Setup: Automatically fetch and ingest the structure (rounds, group stage, straight knockout) and initial list of qualified teams/participants at the start of a new major competition.
- [ ] Feature 2: CompOrchestrator: Data Updates: Monitor and update the tournament data in real-time as knockouts or results occur, automatically configuring the match-ups for the next tipping round.
- [ ] Feature 3: CompManager: Interacting with users, applying custom tipping rules, and managing scoring.
    Tipping Logic: Manage and process tips submitted by users for various stages of the competition.
    Advanced Rules (Wildcards & Weightings): Process custom rules such as wildcards and weightings. For example, a user might use a wildcard to double or triple the points earned from a specific high-confidence prediction.

### Future Enhancements
- [ ] Feature 4: CompManager: User Engagement: Ensure all involved users have submitted their tips for the current stage. If a deadline is missed, the agent will automatically apply a pre-defined fallback option (e.g., picking the lower seed) to keep the competition moving.
- [ ] Feature 5

## Technical Stack
- **Frontend:** Next.js, TypeScript, React
- **Styling:** [TBD]
- **Database:** [TBD]
- **Authentication:** [TBD]
- **Deployment:** [TBD]

## User Flow
1. Power/SiteAdmin: users who oversee the entire application. These users will be able to view all cohorts and leaderboards, review agent processes to ensure the data is ciorrect.  Still a human in the loop for some things TBA.
2. CohortAdmin: these users are per group admins who will be responsible for managing which tournaments the users within their cohort can access. They will 
- login and be presented with their known linked cohorts or start a new cohort
- for new cohorts, they should select an existing tournament that has already been added internally or suggest a new tournament that is coming.
- set the rules for tournament for this cohort
- invite users to each cohort.
- manage the cohort.
3. End users who login and add their tips etc for the cohorts they have access to. They will
- login and be presented with their known linked cohorts (as more tournaments can be happenning at once and they are linked to more than one)
- be presented with their required inputs for the round/stage to enter. At this stage they should be able to see a leaderboard of their cohort only.
- get a single email reminder to submit their tips n days before each stage/round closes for tipping.

## Success Metrics
1. Can the agent pull the data for a new tournament and create the structure required that aligns with the CohortAdmin's rules.
2. Can the agent update the data as the tournament progresses.

## Data Model / Entities

### Core Entities
- **Tournament**: Structure, stages, rounds, teams/participants, match schedule
- **Cohort**: Group of users competing together, linked to a tournament
- **User**: Power/SiteAdmin, CohortAdmin, or EndUser with role-based permissions
- **Tip**: User's prediction for a match/round
- **Match**: Individual game/event within a tournament
- **Round/Stage**: Grouping of matches (e.g., Group Stage, Quarter Finals)
- **Rule**: Custom scoring rules per cohort (wildcards, weightings, deadlines)
- **Leaderboard**: Calculated scores per cohort

## Agent Architecture

### CompOrchestrator
**Responsibilities:**
- Fetch and ingest tournament data from external sources
- Monitor and update tournament structure as results come in
- Configure next round match-ups based on results
- Validate data consistency

**Trigger Points:**
- New tournament creation
- Scheduled updates (polling for results)
- Webhook notifications (if available from data source)

### CompManager
**Responsibilities:**
- Process user tips and apply cohort-specific rules
- Calculate scores with wildcards/weightings
- Update leaderboards
- Manage user engagement and deadlines
- Apply fallback picks for missed deadlines

**Trigger Points:**
- User tip submission
- Round/stage closure
- Result confirmation from CompOrchestrator

### Agent Interaction
- CompOrchestrator updates tournament state → triggers CompManager to recalculate scores
- CompManager requests tournament data → queries CompOrchestrator's stored state

## Integration Points

### Tournament Data Sources
- **Option 1:** Public APIs (e.g., sports data APIs, esports APIs)
- **Option 2:** Manual admin input (for unsupported tournaments)
- **Option 3:** Web scraping (fallback, less reliable)

**Decision:** [TBD - depends on tournament types and data availability]

### External Services
- **Email:** [TBD - SendGrid, Resend, AWS SES, etc.]
- **Authentication:** [TBD - Auth0, Clerk, NextAuth, Supabase Auth]
- **Hosting:** [TBD - Vercel, AWS, Railway]

## Rules Engine

### Rule Types
- **Scoring Rules:** Points per correct prediction (varies by stage)
- **Wildcards:** Multipliers (2x, 3x) for high-confidence picks
- **Weightings:** Different point values for different rounds
- **Deadlines:** When tips must be submitted per round
- **Fallback Strategy:** Auto-pick lower seed, favorite, or random

### Storage
- Rules defined at cohort creation
- Stored as JSON or structured config
- Versioned per tournament stage

## Notification Strategy

### Notification Types
1. **Tip Reminder:** N days before round closes
2. **Round Closed:** Confirmation that tips are locked in
3. **Results Posted:** New leaderboard available
4. **Cohort Invitation:** New user added to cohort

### Timing
- Configurable per cohort
- Default: 2 days before deadline

### Delivery
- Email (primary)
- Future: In-app notifications, SMS, Discord/Slack webhooks

## Admin Controls

### Power/SiteAdmin Can:
- View all cohorts and tournaments
- Override agent decisions
- Manually correct tournament data
- Review agent logs/processes
- Approve new tournament requests from CohortAdmins

### Human-in-Loop Required For:
- New tournament approval (initially)
- Ambiguous results (e.g., tie-breakers, postponed matches)
- User disputes
- Agent failure/error resolution

### CohortAdmin Can:
- Create/manage their cohorts
- Set cohort-specific rules
- Invite/remove users
- Request new tournaments
- View cohort leaderboards only

## Risks & Assumptions

### Risks
1. **Data Source Reliability:** APIs may be unavailable, rate-limited, or inaccurate
2. **Timezone Complexity:** Users in different timezones, tournament times, deadline confusion
3. **Result Ambiguity:** Postponed matches, forfeits, disputed outcomes
4. **Agent Errors:** Incorrect scoring, missed updates, infinite loops
5. **Scale:** Agent processing time for large cohorts

### Assumptions
1. Tournament data is available in a structured format
2. Users have email access for notifications
3. CohortAdmins will set reasonable rules
4. Most tournaments follow standard bracket structures
5. 3-week timeline focuses on core MVP only

## Out of Scope (for MVP)

### Explicitly NOT in 3-Week MVP
- [ ] Mobile native apps
- [ ] Real-time live updates (use polling instead)
- [ ] Social features (comments, trash talk)
- [ ] Payment/monetization
- [ ] Multiple sports/tournament types (focus on ONE type initially)
- [ ] Advanced analytics/insights
- [ ] Tournament simulation/prediction tools
- [ ] Public leaderboards (cohorts are private)
- [ ] User-created tournaments (admin-only)

## Timeline
Max time is three weeks to develop...let's go!
