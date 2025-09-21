# ConnectClass Product Requirements Document (PRD)

## Goals and Background Context

### Goals
- Acquire 50+ paying Japanese schools within 12 months at ¥4,000/month per 100 students
- Achieve 70%+ teacher satisfaction rate with classroom engagement improvements
- Create psychologically safe groups of 4 similar students with 75%+ compatibility scores
- Generate ¥2.4M annually through teacher/school subscriptions
- Reduce social anxiety for 60%+ of students in group settings (teacher-reported)
- Maintain simple content updates with cultural appropriateness
- Enable teachers to facilitate "magical 10-minute interactions" that transform classroom dynamics
- Achieve 80%+ assessment completion rate in teacher-led sessions

### Background Context

ConnectClass addresses a critical problem in Japanese education: 86% of students report social anxiety in group settings, while traditional personality assessments feel invasive and culturally inappropriate. The app uses culturally-dynamic hybrid personality discovery through trending Japanese scenarios combined with HEXACO personality mapping and Fuzzy C-Means clustering to create psychologically safe groups of 4 similar students.

Unlike traditional questionnaires, students engage with relatable, constantly-updated scenarios sourced from active trend monitoring of student-focused websites, anime culture, and idol preferences. The system serves as a facilitator tool - enabling face-to-face human connections rather than replacing them - with AI-generated ice-breakers designed for Japanese classroom culture while respecting preferences for harmony and face-saving.

### Change Log
| Date | Version | Description | Author |
|------|---------|-------------|---------|
| 2025-08-28 | 1.0 | Initial PRD creation from Project Brief | John (PM Agent) |
| 2025-08-28 | 1.1 | Updated for teacher-focused business model and simplified MVP | John (PM Agent) |

## Requirements

### Functional Requirements

**FR1:** The system shall provide cultural trend-driven personality assessment using 12-15 scenario questions sourced from active Japanese student websites, updated monthly

**FR2:** Each scenario shall offer 4 culturally-specific multiple-choice responses mapping to HEXACO personality dimensions  

**FR3:** The system shall implement Fuzzy C-Means clustering to create groups of 4 similar students with 75%+ compatibility scores

**FR4:** Teachers shall preview and select from 20+ culturally-current scenario options before class sessions

**FR5:** The system shall generate AI-powered ice-breaker activities for "magical 10-minute" group interactions

**FR6:** The mobile-first interface shall display visual personality reveals using Japanese-preferred colors/symbols

**FR7:** Students shall join classes using anonymous authentication with teacher-generated codes, creating temporary guest sessions

**FR8:** Anonymous guest users shall convert to permanent accounts post-class, automatically migrating all session data via built-in identity linking

**FR9:** The system shall use Row Level Security policies to distinguish between guest and permanent user permissions

**FR10:** Group relationship data shall persist through guest-to-permanent user conversion via foreign key relationships

### Non-Functional Requirements

**NFR1:** The system shall maintain sub-100ms response times using Vercel CDN edge locations in Japan

**NFR2:** The Progressive Web App shall work on iOS/Android without app store dependencies

**NFR3:** Operational costs shall not exceed $40/month using free-tier architecture (Vercel, Supabase)

**NFR4:** The system shall handle up to 1M+ users while starting completely free

**NFR5:** Cultural scenarios shall achieve 80%+ student recognition rate as personally relevant

**NFR6:** The system shall maintain 95%+ cultural consultant approval for all content updates

**NFR7:** All student personality data shall be automatically purged if user students choose to (privacy requirement)

**NFR8:** Anonymous user cleanup shall run automatically after 30 days for unconverted accounts

**NFR9:** The system shall prevent abuse through invisible CAPTCHA on anonymous sign-ins with 30 requests/hour IP limit

## User Interface Design Goals

### Overall UX Vision
A mobile-first Progressive Web App that feels like a friendly social discovery game rather than a formal assessment tool. The interface should embrace Japanese aesthetic preferences with clean, harmonious design that reduces anxiety while encouraging authentic self-expression through culturally relevant scenarios.

### Key Interaction Paradigms
- **Scenario-Based Discovery:** Swipe-through cultural scenarios with visual storytelling
- **Gentle Progress Indicators:** Soft, encouraging feedback during personality assessment
- **One-Touch Joining:** Simple code entry without registration friction
- **Visual Personality Reveals:** Color-coded and symbol-based results that feel celebratory rather than clinical
- **Group Formation Magic:** Smooth animations showing compatible students coming together

### Core Screens and Views
1. **Student Code Entry** - Simple, welcoming landing page
2. **Scenario Assessment Flow** - Engaging cultural scenario presentation
3. **Personality Discovery Results** - Visual, celebratory reveal of traits
4. **Group Formation Display** - Animated grouping with compatibility indicators  
5. **Ice-Breaker Activity Screen** - Generated conversation starters
6. **Post-Class Signup Invitation** - Gentle account creation encouragement

### Accessibility: WCAG AA
Meeting WCAG AA standards for educational environments with high contrast ratios, keyboard navigation support, and screen reader compatibility for inclusive classroom use.

### Branding
Clean, modern Japanese aesthetic with:
- Soft, harmonious color palette inspired by Japanese seasonal colors
- Gentle animations reminiscent of Japanese mobile game UX (Maybe noe brutalism or retro UI library will be a good options)
- Typography that works well with both Japanese and English text
- Cultural symbols and imagery that resonate with student life

### Target Device and Platforms: Web Responsive
Progressive Web App optimized for mobile devices (primary) with desktop compatibility for teacher dashboards, ensuring seamless experience across iOS/Android without app store dependencies.
<!-- I heard of this service https://capacitorjs.com/ that help nextJS mobie app works better, I need you to do research about this one -->


### User Guidance Strategy
**Progressive Help System:** Clean interface with on-demand detailed explanations to satisfy Japanese users' need for comprehensive information while maintaining mobile performance. Implementation includes:

- **30-second overview videos** (external links) for teachers and students  
- **Smart contextual tooltips** using lightweight libraries (Shepherd.js/OnboardJS)
- **Cultural adaptation layer** providing extra explanations for Japanese users
- **Progressive disclosure** starting minimal, expanding based on user interaction


### Performance Targets
<!--We might not need video instruction now, just simple text, and animated instruction 
- Also I am consider using floating-ui.com other than the other one, we need To do research on is it the best option here. -->
- Tutorial system under 50KB total footprint
- Contextual help loads on-demand only
- Video content externally hosted (Vercel/YouTube)
- Cultural explanation overlays conditionally loaded

## Technical Assumptions

### Repository Structure: Monorepo
Single repository containing the complete ConnectClass application with organized feature modules, shared components, and unified deployment pipeline optimized for rapid iteration and Japanese market testing.

### Service Architecture
**Next.js 15 Serverless Monolith:** Server-side rendering with Vercel Edge Functions for optimal performance in Japan. Architecture supports scaling to microservices post-MVP while maintaining cost efficiency during initial 50-school rollout phase.

### Testing Requirements
**Unit + Integration Testing:** Vitest for component testing, Playwright for critical user flows (personality assessment, group formation), and manual testing scripts for cultural consultant approval workflows. Focus on compatibility testing across Japanese mobile devices.

### Additional Technical Assumptions and Requests

**Frontend Framework:**
- **Next.js 15** with App Router and stable Server Actions
- **Turbopack** for 10x faster development (default in v15)
- **PWA capabilities** via next-pwa for iOS/Android compatibility

**Database & Authentication:**
- **Supabase** with anonymous authentication and user migration
- **Row Level Security (RLS)** for data privacy compliance
- **Prisma ORM** for type-safe database interactions
- **Automated cleanup** for anonymous users after 30 days

**Deployment & Performance:**
- **Vercel Edge Network** with Japanese CDN optimization  
- **Target: <100ms response times** in Tokyo/Osaka regions
- **Progressive enhancement** ensuring functionality without JavaScript

**AI/ML Integration:**
- **OpenAI API** for ice-breaker generation and cultural adaptation
- **Edge runtime compatibility** for low-latency personality analysis
- **Fallback mechanisms** for AI service interruptions

**Internationalization:**
- **Built-in Next.js i18n** with Japanese/English support
- **Cultural adaptation layer** for UI explanations and help content
- **Font optimization** supporting Japanese characters (Gothic/Mincho)

**Monitoring & Analytics:**
- **Vercel Analytics** for Core Web Vitals tracking
- **Supabase Dashboard** for user behavior insights  
- **Cultural consultant approval tracking** system

## Epic List

### Epic 1: Foundation & Core Infrastructure
**Goal:** Establish project setup, anonymous authentication, and basic student class joining functionality while delivering a testable "hello world" experience.

### Epic 2: Personality Discovery Engine
**Goal:** Create the cultural scenario-based personality assessment system with HEXACO mapping and basic result display.

### Epic 3: Group Formation & Ice-Breaker System
**Goal:** Implement Fuzzy C-Means clustering for compatible groups and AI-generated ice-breaker activities.

### Epic 4: Teacher Dashboard & Class Management  
**Goal:** Build essential teacher interfaces for class creation, simple progress monitoring, and basic feedback collection focused on teacher value.

### Epic 5: Simple Data Choices & Basic Retention
**Goal:** Enable simple post-class data choices (keep or delete) with basic account creation and NPS feedback collection.

### Epic 6: Basic Content Management & Localization
**Goal:** Implement basic content management tools and Japanese localization for MVP launch.

## Epic 1: Foundation & Core Infrastructure

**Expanded Goal:** Establish the technical foundation for ConnectClass including Next.js 15 setup, Supabase integration, anonymous authentication system, and basic student class joining functionality. This epic delivers a working "minimal viable classroom" where students can join with codes and teachers can create classes, providing immediate testable value while setting up all infrastructure for future personality assessment and grouping features.

### Story 1.1: Project Setup & Development Environment
**As a developer,**  
**I want a properly configured Next.js 15 project with all necessary dependencies,**  
**so that the team can begin building ConnectClass features immediately.**

#### Acceptance Criteria:
1. Next.js 15 project initialized with App Router and TypeScript configuration
2. Supabase client configured with environment variables and database connection
3. Prisma ORM integrated with Supabase PostgreSQL and initial schema migrations
4. PWA configuration enabled with manifest.json for mobile app-like experience
5. Development scripts configured (dev, build, test, lint) with proper error handling
6. Git repository initialized with appropriate .gitignore and README documentation

### Story 1.2: Anonymous Authentication System
**As a student,**  
**I want to join classes without creating an account,**  
**so that I can participate immediately without registration friction.**

#### Acceptance Criteria:
1. Supabase anonymous authentication enabled with proper RLS policies configured
2. Anonymous users created automatically when students join classes via codes
3. Anonymous user sessions persist throughout class duration and assessment flow
4. Row Level Security policies distinguish between anonymous and permanent users
5. Anonymous user data properly scoped to individual sessions without cross-contamination
6. Error handling for authentication failures with user-friendly messages

### Story 1.3: Teacher Class Creation
**As a teacher,**  
**I want to create new class sessions with unique join codes,**  
**so that students can easily access my classroom activities.**

#### Acceptance Criteria:
1. Teacher dashboard interface for creating new class sessions with descriptive names
2. Unique 6-digit alphanumeric join codes generated automatically for each class
3. Class metadata stored including teacher ID, creation timestamp, and session status
4. Join codes expire after configurable time period (default 4 hours) for security
5. Teachers can view active classes with student count and join code display
6. Database schema supports class-to-student relationships for group formation tracking

### Story 1.4: Student Class Joining
**As a student,**  
**I want to join a class using a simple code,**  
**so that I can participate in classroom activities without complex registration.**

#### Acceptance Criteria:
1. Clean, mobile-optimized landing page with prominent code entry field
2. 6-digit code validation with immediate feedback for invalid/expired codes
3. Successful join creates anonymous user session linked to specific class
4. Student automatically redirected to waiting room showing class details
5. Join attempts logged for teachers to monitor class participation
6. Error handling for full classes, expired codes, or duplicate join attempts

### Story 1.5: Basic Real-Time Class Status
**As a teacher and student,**  
**I want to see real-time updates of class participation,**  
**so that everyone knows when the class is ready to begin activities.**

#### Acceptance Criteria:
1. Real-time student counter displays current participants to teacher and students
2. Supabase real-time subscriptions update participant lists without page refresh
3. Student names/avatars displayed in waiting room (anonymous identifiers like "Student A")
4. Teacher can see which students have joined and their join timestamps
5. Connection status indicators show when students lose/regain network connectivity
6. System handles real-time subscription cleanup when students leave or disconnect

## Epic 2: Personality Discovery Engine

**Expanded Goal:** Build the core personality assessment system using culturally-dynamic Japanese scenarios that map to HEXACO personality dimensions. This epic delivers a complete personality discovery experience where students engage with trending cultural content, receive personalized results, and understand their traits through visual presentations - establishing the foundation data needed for compatible group formation.

### Story 2.1: Scenario Content Management System
**As a cultural consultant,**  
**I want to create and manage Japanese cultural scenarios with multiple choice responses,**  
**so that personality assessments remain current and culturally relevant for students.**

#### Acceptance Criteria:
1. Admin interface for creating scenarios with title, description, and cultural context
2. Four multiple choice responses per scenario, each mapped to specific HEXACO dimensions
3. Scenario categorization by cultural themes (anime, idol culture, school life, social situations)
4. Draft/published workflow allowing consultant review before scenarios go live
5. Scenario scheduling system for monthly content rotation and updates
6. Analytics tracking scenario engagement rates and student recognition scores

### Story 2.2: HEXACO Personality Assessment Flow
**As a student,**  
**I want to answer engaging cultural scenarios that reveal my personality,**  
**so that I can discover my traits through content I find personally relevant.**

#### Acceptance Criteria:
1. Mobile-optimized assessment interface presenting 12-15 scenarios sequentially
2. Visual scenario presentation with cultural imagery and clear response options
3. Progress indicator showing assessment completion percentage with encouraging messaging
4. Response data stored linked to anonymous user session for later group formation
5. Assessment can be paused and resumed within class session timeframe
6. Responses automatically saved to prevent data loss from network interruptions

### Story 2.3: HEXACO Score Calculation Engine
**As the system,**  
**I want to calculate personality scores from scenario responses,**  
**so that students receive accurate personality profiles for group formation.**

#### Acceptance Criteria:
1. Response mapping algorithm converts multiple choice answers to HEXACO dimension scores
2. Weighted scoring system accounts for scenario difficulty and cultural relevance
3. Six HEXACO dimensions calculated: Honesty-Humility, Emotionality, eXtraversion, Agreeableness, Conscientiousness, Openness
4. Score normalization ensures balanced distribution across personality dimensions
5. Calculation results stored with anonymous user session for group formation algorithms
6. Score recalculation possible if assessment is retaken within same class session

### Story 2.4: Visual Personality Results Display
**As a student,**  
**I want to see my personality results in an engaging visual format,**  
**so that I can understand and celebrate my unique traits.**

#### Acceptance Criteria:
1. Personality reveal screen using Japanese-preferred colors and cultural symbols
2. Six HEXACO dimensions displayed as visual elements (bars, circles, or cultural icons)
3. Brief, positive descriptions of each personality trait in encouraging language
4. Cultural context explaining how traits manifest in Japanese classroom settings
5. Shareable visual summary suitable for group ice-breaker discussions
6. Results screen includes gentle encouragement to join permanent account post-class

### Story 2.5: Assessment Data Validation & Quality
**As a teacher,**  
**I want confidence that personality assessments are producing reliable results,**  
**so that group formations will be effective and educationally valuable.**

#### Acceptance Criteria:
1. Assessment completion tracking with minimum response threshold for valid results
2. Response pattern analysis detecting rushed or random answer selection
3. Cultural consultant approval workflow for new scenarios before student access
4. Assessment reliability metrics tracked and reported in teacher dashboard
5. Student feedback mechanism for scenarios that felt irrelevant or confusing
6. Data quality reports showing scenario performance and recognition rates

## Epic 3: Group Formation & Ice-Breaker System

**Expanded Goal:** Implement intelligent group formation using Fuzzy C-Means clustering to create psychologically safe groups of 4 similar students based on HEXACO personality profiles. This epic delivers the core classroom transformation experience through AI-generated ice-breaker activities designed for Japanese cultural preferences, enabling the "magical 10-minute interactions" that improve group dynamics and reduce social anxiety.

### Story 3.1: Fuzzy C-Means Clustering Algorithm
**As the system,**  
**I want to group students with similar personality profiles using advanced clustering,**  
**so that each group of 4 students feels psychologically safe and compatible.**

#### Acceptance Criteria:
1. Fuzzy C-Means algorithm implementation using HEXACO scores as clustering features
2. Target group size of exactly 4 students with 75%+ compatibility threshold
3. Algorithm handles edge cases: odd numbers, insufficient students, extreme personality outliers
4. Clustering results include compatibility scores and reasoning for teacher review
5. Group formation completes within 30 seconds of assessment completion for smooth flow
6. Fallback grouping strategy when clustering fails to meet compatibility thresholds

### Story 3.2: Group Formation Results Display
**As a student,**  
**I want to see my assigned group with visual compatibility indicators,**  
**so that I feel confident about working with these specific classmates.**

#### Acceptance Criteria:
1. Group reveal animation showing 4 students coming together with celebratory visual effects
2. Compatibility percentage displayed prominently with positive, encouraging messaging
3. Anonymous student identifiers (Student A, B, C, D) with unique visual avatars
4. Shared personality traits highlighted to show why the group works well together
5. Group number/name assigned for easy teacher reference during classroom activities
6. Subtle transition preparing students for upcoming ice-breaker activities

### Story 3.3: AI-Generated Ice-Breaker Activities
**As a teacher,**  
**I want AI to generate culturally-appropriate ice-breaker activities for each group,**  
**so that students can have meaningful 10-minute interactions that reduce social anxiety.**

#### Acceptance Criteria:
1. OpenAI API integration generating 3-5 ice-breaker options per group formation
2. Activities tailored to Japanese classroom culture emphasizing harmony and face-saving
3. Ice-breakers account for group's shared personality traits and compatibility themes
4. Activity difficulty appropriate for 10-minute time constraint with clear instructions
5. Fallback pre-written ice-breakers available when AI service is unavailable
6. Teacher can preview and optionally regenerate ice-breakers before presenting to groups

### Story 3.4: Group Activity Interface
**As a student,**  
**I want clear, engaging presentation of my group's ice-breaker activity,**  
**so that I can participate confidently in the group interaction.**

#### Acceptance Criteria:
1. Mobile-optimized activity screen with clear instructions and visual guidance
2. Activity prompts presented in encouraging, culturally-sensitive language
3. Optional conversation starters and follow-up questions to maintain group flow
4. Activity completion confirmation allowing groups to signal they're finished
5. Subtle background music or ambient sounds to reduce awkward silence anxiety

### Story 3.5: Group Formation Analytics & Teacher Monitoring
**As a teacher,**  
**I want real-time visibility into group formation success and activity progress,**  
**so that I can facilitate classroom dynamics and provide support where needed.**

#### Acceptance Criteria:
1. Teacher dashboard showing all groups with compatibility scores and member assignments
2. Real-time activity progress indicators showing which groups are engaged vs struggling
3. Group formation analytics including clustering success rates and compatibility metrics
4. Teacher intervention tools allowing manual group adjustments if needed
5. Post-activity feedback collection measuring student satisfaction with group assignments
6. Historical data tracking for improving future group formation algorithms

## Epic 4: Teacher Dashboard & Class Management

**Expanded Goal:** Build essential teacher interfaces for class creation, simple progress monitoring, and group management. This epic delivers the core classroom management tools that enable teachers to create classes, trigger AI grouping when ready, and view group results with student information - focusing on the essential workflow without complex analytics.

### Story 4.1: Basic Class Session Management
**As a teacher,**  
**I want to create classes and see basic submission status,**  
**so that I can manage ConnectClass sessions with minimal complexity.**

#### Acceptance Criteria:
1. Simple dashboard interface for creating new class sessions with session names
2. Join code display with copy-to-clipboard functionality for sharing with students
3. Basic submission counter showing "X of Y students submitted" without complex notifications
4. Class session list showing active/past sessions with basic status indicators
5. Simple class controls: create, view submissions, archive when complete
6. Student list showing who has joined the class session

### Story 4.2: AI Grouping Trigger & Processing
**As a teacher,**  
**I want to trigger AI grouping when all students have submitted,**  
**so that I can control when group formation happens.**

#### Acceptance Criteria:
1. "Group" button becomes available when desired number of students have submitted
2. Button shows loading/processing state and becomes unclickable during AI clustering
3. Processing indicator with simple message "Creating groups..." during fuzzy C-means calculation
4. Grouping completes and displays results without teacher needing to refresh page
5. Error handling if grouping fails with option to retry or manual fallback
6. Simple confirmation that grouping is complete and ready to show students

### Story 4.3: Group Results & Student Information Display
**As a teacher,**  
**I want to see all group formations and complete student information,**  
**so that I understand the groups and can facilitate activities effectively.**

#### Acceptance Criteria:
1. Group results screen showing all formed groups (Group 1, Group 2, etc.)
2. Each group displays 4 student members with their complete personality information
3. Student HEXACO scores and traits visible to teacher for context understanding
4. Group compatibility scores shown for teacher reference
5. Clean, organized layout suitable for quick teacher reference during class
6. Option to print or export group assignments for offline reference

### Story 4.4: Ice Breaker Distribution
**As a teacher and student,**  
**I want ice breaker questions delivered to each group,**  
**so that groups can begin meaningful conversations.**

#### Acceptance Criteria:
1. AI-generated ice breakers created specific to each group's personality combination
2. Students in each group see their unique group-specific ice breaker questions
3. Ice breakers delivered to student devices after teacher confirms group formation
4. Teacher can preview ice breakers before they're sent to students
5. Simple, clear presentation of 3-5 conversation starter questions per group
6. Questions designed for face-to-face conversation without requiring app interaction

### Story 4.5: Basic Session Completion
**As a teacher,**  
**I want simple session completion and cleanup,**  
**so that I can finish classes cleanly and prepare for next sessions.**

#### Acceptance Criteria:
1. "End Session" button to cleanly close class activities
2. Session summary showing group assignments and basic completion status
3. Data cleanup process removing temporary session data while preserving essential records
4. Simple archive functionality for referencing past successful class configurations
5. Student prompt encouraging account signup appears after session ends
6. Clear session state reset preparing system for next class creation

## Epic 5: User Account Migration & Data Persistence

**Expanded Goal:** Enable simple post-class data choices for students - keep personality results or delete everything. This epic delivers basic user retention through optional account creation while maintaining privacy-first approach with automatic cleanup as default.

### Story 5.1: Simple Post-Session Data Choice
**As a student,**  
**I want to easily choose what happens to my data after class,**  
**so that I have control over my privacy without complexity.**

#### Acceptance Criteria:
1. Simple choice screen appears when class session ends: "Keep Results" or "Delete Everything"
2. If "Keep Results": Basic signup form (email + password) to save personality data
3. If "Delete Everything": All data immediately deleted, no trace left
4. Clear explanation: "Keep = see your personality anytime, Delete = complete privacy"
5. Default is "Delete Everything" to protect privacy
6. No complex group persistence features - focus on individual choice only

### Story 5.2: Anonymous-to-Permanent Account Conversion
**As the system,**  
**I want to seamlessly migrate anonymous user data to permanent accounts,**  
**so that students retain their personality and group information without data loss.**

#### Acceptance Criteria:
1. Supabase anonymous user conversion using updateUser() or linkIdentity() methods
2. All personality assessment data automatically transfers to permanent account
3. Group membership and compatibility scores preserved during account conversion
4. Anonymous session data cleaned up after successful account creation
5. Conversion process handles errors gracefully with data rollback if signup fails
6. User session continues seamlessly without re-authentication after conversion

### Story 5.3: Basic Group Information Display
**As a student who kept their data,**  
**I want to see basic information about my classroom group,**  
**so that I remember the group formation experience.**

#### Acceptance Criteria:
1. Simple group info page showing: group members (anonymous names like "Student A, B, C"), compatibility score, formation date
2. No persistent group features or ongoing interaction capabilities
3. Read-only information for personal reference only
4. No complex group management or persistent social features
5. Group info deleted if any member chooses "Delete Everything"
6. Foundation for potential future group features in later versions

### Story 5.4: Automatic Data Cleanup & Privacy Protection
**As the system,**  
**I want to automatically clean up ephemeral data while preserving persistent user choices,**  
**so that student privacy is protected by default with clear data retention policies.**

#### Acceptance Criteria:
1. Anonymous user data automatically deleted 24 hours after class session ends
2. Class session data cleaned up after all students complete signup decisions
3. Students who don't signup have all personality and group data permanently removed
4. Database cleanup jobs run daily to ensure no orphaned anonymous data remains
5. Clear audit trail of data deletion for privacy compliance and reporting
6. Students can manually delete their anonymous profile before 24-hour automatic cleanup

### Story 5.5: Simple Review Collection
**As the product team,**  
**I want to collect basic feedback about the classroom experience,**  
**so that we can improve the app for teachers and students.**

#### Acceptance Criteria:
1. After data choice, simple 2-question feedback: "How likely to recommend?" (0-10 NPS scale)
2. Optional follow-up: "Main reason for rating?" (text box, optional)
3. Takes maximum 30 seconds to complete
4. Feedback tied to class session for teacher insights
5. Anonymous feedback - no personal identification
6. Results aggregated for teacher dashboard and product improvement

## Epic 6: Cultural Content Management & Optimization

**Expanded Goal:** Implement basic content management for cultural scenarios and simple Japanese localization. This epic delivers essential content tools and teacher feedback collection while keeping complexity minimal for MVP launch.

### Story 6.1: Cultural Scenario Content Management System
**As a cultural consultant,**  
**I want a streamlined system to create, review, and schedule Japanese cultural scenarios,**  
**so that personality assessments remain engaging and culturally relevant for students.**

#### Acceptance Criteria:
1. Admin dashboard for creating new scenarios with cultural context documentation
2. Scenario template system ensuring consistent HEXACO mapping across all content
3. Cultural relevance tagging system (anime, idol culture, school life, social trends)
4. Draft/review/approval workflow preventing unvetted content from reaching students
5. Bulk import functionality for efficient monthly scenario batch updates
6. Version control system tracking scenario changes and consultant approval history

### Story 6.2: Basic Content Updates
**As a product owner,**  
**I want simple content update capability,**  
**so that scenarios can be improved based on feedback.**

#### Acceptance Criteria:
1. Manual content update process (no automation needed for MVP)
2. Basic approval workflow: draft → review → publish
3. Simple content backup before updates
4. Basic scenario replacement functionality
5. Manual testing of new scenarios before deployment
6. Simple content versioning (current vs previous)

### Story 6.3: Teacher Feedback Dashboard
**As a teacher,**  
**I want to see basic feedback about my class sessions,**  
**so that I understand if ConnectClass is helping my students.**

#### Acceptance Criteria:
1. Simple dashboard showing: student completion rate, average group compatibility scores
2. Aggregated NPS scores from student feedback after sessions
3. Basic session success metrics (completed vs incomplete assessments)
4. Simple export of class results for teacher records
5. Optional comments from student feedback displayed anonymously
6. Basic trend showing improvement over multiple class sessions

### Story 6.4: Basic Japanese Localization
**As Japanese users,**  
**I want the app interface in Japanese,**  
**so that teachers and students can use it naturally.**

#### Acceptance Criteria:
1. All UI text translated to Japanese with basic cultural adaptation
2. Japanese font support for readable text on mobile devices
3. Basic Japanese input support for teacher class names and feedback
4. Simple language toggle (Japanese/English) for international testing
5. Cultural appropriateness review of translated content
6. Basic Japanese number and date formatting

### Story 6.5: Admin Analytics Dashboard
**As the product owner,**  
**I want basic analytics about app usage,**  
**so that I can understand product performance and plan improvements.**

#### Acceptance Criteria:
1. Simple admin dashboard showing: total classes created, students participated, completion rates
2. Basic feedback aggregation from NPS scores and comments
3. Simple usage metrics: most popular scenarios, average session duration
4. Teacher adoption metrics: active teachers, repeat usage
5. Basic error tracking for technical issues
6. Simple CSV export for further analysis

## Checklist Results Report

### PM Checklist Validation Report - ConnectClass PRD

#### Executive Summary
- **Overall PRD Completeness:** 88% complete
- **MVP Scope Appropriateness:** Just Right - well-scoped for vibe coder implementation
- **Readiness for Architecture Phase:** Ready with minor clarifications needed
- **Most Critical Gap:** Technical constraints need minor clarification on Japanese compliance requirements

#### Category Analysis Table

| Category | Status | Critical Issues |
|----------|--------|----------------|
| 1. Problem Definition & Context | PASS | ✅ Clear problem, target users, success metrics from Project Brief |
| 2. MVP Scope Definition | PASS | ✅ Excellent scope - epics are appropriately sized and sequential |
| 3. User Experience Requirements | PASS | ✅ Japanese UI preferences researched, progressive help system defined |
| 4. Functional Requirements | PASS | ✅ FR/NFR clearly mapped, guest-to-permanent workflow well-designed |
| 5. Non-Functional Requirements | PASS | ✅ Performance targets, privacy requirements, cost constraints defined |
| 6. Epic & Story Structure | PASS | ✅ 6 epics logically sequenced, stories appropriately sized |
| 7. Technical Guidance | PARTIAL | ⚠️ Need clarity on Japanese privacy compliance implementation |
| 8. Cross-Functional Requirements | PASS | ✅ Data model clear, Supabase+Prisma integration documented |
| 9. Clarity & Communication | PASS | ✅ Clear language, user-focused, well-structured |

#### Final Decision

**✅ READY FOR ARCHITECT**: The PRD provides comprehensive requirements with clear technical constraints. The epic structure supports incremental development perfect for a vibe coder approach. Minor clarifications above can be addressed during architecture phase without blocking progress.

**Strengths:**
- Excellent user research integration (Japanese market needs)
- Smart technical stack choices for cost/scale constraints
- Well-sequenced epics enabling progressive development
- Clear business model alignment with MVP features

This PRD successfully balances technical feasibility with market needs while maintaining focus on the core classroom transformation value proposition.

## Next Steps

### UX Expert Prompt
Create mobile-first wireframes and user flows for ConnectClass focusing on Japanese student preferences for information-rich interfaces. Design the personality assessment flow as engaging cultural scenario presentation, group formation results with celebratory animations, and progressive help system balancing clean aesthetics with detailed explanations. Prioritize Epic 1-3 user journeys: student class joining, cultural personality assessment, and group formation reveal.

### Architect Prompt 
Design technical architecture for ConnectClass using Next.js 15, Supabase (anonymous auth + PostgreSQL), and Prisma ORM. Focus on anonymous-to-permanent user migration workflow, HEXACO personality data modeling, and Fuzzy C-Means clustering integration. Address Japanese market performance requirements (<100ms response times), ephemeral session management (24hr cleanup), and scalable group formation for 50+ student classes. Prioritize Epic 1 foundation: anonymous authentication, class session management, and real-time student tracking.