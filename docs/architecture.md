# ConnectClass Fullstack Architecture Document

## Introduction

This document outlines the complete fullstack architecture for ConnectClass, including backend systems, frontend implementation, and their integration. It serves as the single source of truth for AI-driven development, ensuring consistency across the entire technology stack.

This unified approach combines what would traditionally be separate backend and frontend architecture documents, streamlining the development process for modern fullstack applications where these concerns are increasingly intertwined.

### Starter Template or Existing Project

**Analysis of ConnectClass Project Context:**

After reviewing the PRD and existing documents, this is a **greenfield project** with specific technical assumptions already defined:

1. **Predefined Technical Stack:** The PRD specifies Next.js 15 with App Router, Supabase for backend services, and Vercel for deployment
2. **Cultural Market Focus:** Japanese education market requiring specific performance (<100ms response times in Japan) and cultural considerations
3. **Monorepo Structure:** Single repository approach specified for rapid iteration and unified deployment pipeline
4. **Cost Constraints:** Must operate within free-tier limitations ($40/month maximum operational costs)

**Recommended Starter Template Approach:**
Given the specific requirements and constraints, I recommend starting with the **Next.js 15 + Supabase Starter Template** or creating a custom setup that includes:

- Next.js 15 with App Router and Turbopack
- Supabase with anonymous authentication and PostgreSQL
- Prisma ORM for type-safe database interactions
- PWA configuration for mobile-first deployment
- Vercel deployment optimization for Japanese CDN performance

**Decision:** Custom greenfield project setup based on PRD specifications rather than existing template, allowing full control over Japanese market optimizations and anonymous user workflow requirements.

### Change Log

| Date | Version | Description | Author |
|------|---------|-------------|---------|
| 2025-09-21 | 1.0 | Initial fullstack architecture document creation | Winston (Architect Agent) |

---

## High Level Architecture

### Technical Summary

ConnectClass employs a **serverless-first fullstack architecture** deployed on Vercel's Edge Network with Supabase backend services, optimized for the Japanese education market. The **Next.js 15 monorepo** supports both web and mobile deployment through Capacitor.js, enabling rapid iteration of cultural scenario content while maintaining sub-100ms response times in Japan.

**Anonymous-to-permanent user flow** leverages Supabase's built-in anonymous authentication with automatic session migration, allowing students to participate immediately without registration friction then optionally convert to persistent accounts post-class. **Real-time group formation** combines HEXACO personality scoring with Fuzzy C-Means clustering algorithms, generating psychologically compatible groups of 4 students with AI-powered ice-breakers tailored to Japanese classroom culture.

The architecture achieves PRD goals through **cost-efficient scalability** (free-tier foundation supporting 100+ initial users), **cultural adaptability** (monthly scenario content rotation), and **teacher workflow optimization** (simple dashboard triggering AI grouping with real-time progress monitoring).

### Platform and Infrastructure Choice

**Recommended Platform: Vercel + Supabase Ecosystem**

**Analysis of Options:**
1. **Vercel + Supabase** ⭐ **RECOMMENDED**
   - ✅ **Pros:** Next.js optimization, Japanese CDN edge locations, anonymous auth built-in, generous free tiers, rapid deployment
   - ✅ **Perfect for:** Cost-conscious educational apps needing real-time features and cultural content updates
   - ⚠️ **Considerations:** Vendor lock-in, limited backend customization

2. **AWS Full Stack**
   - ✅ **Pros:** Enterprise scale, full control, comprehensive services
   - ❌ **Cons:** Complex setup, higher costs, overkill for 100-user MVP, steeper learning curve

3. **Google Cloud + Firebase**
   - ✅ **Pros:** Firebase real-time database, good Asian performance
   - ❌ **Cons:** More expensive than Supabase, complex pricing model, unnecessary complexity

**Final Recommendation:** **Vercel + Supabase** perfectly aligns with ConnectClass requirements: Japanese market performance, anonymous user workflows, real-time group updates, and cost constraints.

**Platform:** Vercel + Supabase
**Key Services:** Vercel Edge Functions, Supabase PostgreSQL + Auth + Realtime, OpenAI API
**Deployment Host and Regions:** Vercel Edge Network (Tokyo/Osaka nodes), Supabase Asia-Pacific (ap-northeast-1)

### Repository Structure

**Structure:** Monorepo with Next.js 15 App Router
**Monorepo Tool:** Built-in npm workspaces (simple, no additional tooling needed)
**Package Organization:** Feature-based modules with shared type definitions

**Rationale:** ConnectClass benefits from monorepo simplicity - shared TypeScript interfaces between frontend/backend, unified deployment pipeline, and rapid iteration for cultural content updates. npm workspaces sufficient for 100-user MVP without Turborepo/Nx complexity.

### High Level Architecture Diagram

```mermaid
graph TB
    subgraph "User Access"
        Student[📱 Students<br/>Mobile PWA]
        Teacher[💻 Teachers<br/>Dashboard]
    end

    subgraph "Vercel Edge Network"
        CDN[🌐 Vercel CDN<br/>Tokyo/Osaka Edge]
        NextApp[⚡ Next.js 15 App<br/>SSR + Edge Functions]
    end

    subgraph "Supabase Backend"
        Auth[🔐 Anonymous Auth<br/>+ User Migration]
        DB[(🗄️ PostgreSQL<br/>RLS Policies)]
        Realtime[📡 Realtime<br/>Group Updates]
    end

    subgraph "AI Services"
        OpenAI[🧠 OpenAI API<br/>Ice-breaker Generation]
        Clustering[📊 Fuzzy C-Means<br/>Group Formation]
    end

    subgraph "Content Management"
        Scenarios[📚 Cultural Scenarios<br/>Monthly Updates]
        Admin[👨‍💼 Cultural Consultant<br/>Content Approval]
    end

    Student --> CDN
    Teacher --> CDN
    CDN --> NextApp
    NextApp --> Auth
    NextApp --> DB
    NextApp --> Realtime
    NextApp --> OpenAI
    NextApp --> Clustering
    Admin --> Scenarios
    Scenarios --> DB

    classDef japanese fill:#FF6B6B,stroke:#D63031,color:#FFFFFF
    classDef serverless fill:#74B9FF,stroke:#0984E3,color:#FFFFFF
    classDef database fill:#55A3FF,stroke:#2D7DD2,color:#FFFFFF
    classDef ai fill:#A29BFE,stroke:#6C5CE7,color:#FFFFFF

    class Student,Teacher japanese
    class CDN,NextApp,Auth,Realtime serverless
    class DB database
    class OpenAI,Clustering ai
```

### Architectural Patterns

- **Jamstack Architecture:** Static generation with serverless API routes - _Rationale:_ Optimal performance for cultural scenario content with Japanese CDN edge caching, reduces server costs
- **Anonymous-First Authentication:** Guest sessions with optional conversion - _Rationale:_ Removes registration friction while enabling data persistence for engaged users, culturally appropriate for Japanese privacy preferences
- **Event-Driven Real-time:** Supabase subscriptions for live group formation - _Rationale:_ Creates engaging "magical 10-minute" experience as students see groups form in real-time
- **Repository Pattern:** Abstract database operations behind service layer - _Rationale:_ Enables testing of group formation algorithms and potential migration from Supabase if needed
- **API Gateway Pattern:** Next.js API routes with unified error handling - _Rationale:_ Centralized authentication, rate limiting, and cultural content validation
- **Content Strategy Pattern:** Pluggable cultural scenario sources - _Rationale:_ Supports monthly content rotation and multiple cultural consultant workflows
- **Cluster-First Design:** Group formation as primary architectural concern - _Rationale:_ HEXACO + Fuzzy C-Means clustering drives core user experience and database schema design

---

## Tech Stack

This is the **DEFINITIVE technology selection** for the entire ConnectClass project. This table serves as the single source of truth - all development must use these exact versions.

### Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
|----------|------------|---------|---------|-----------|
| **Frontend Language** | TypeScript | ^5.3.0 | Type-safe development across entire stack | Prevents runtime errors, enables better AI code assistance, essential for shared interfaces between features |
| **Frontend Framework** | Next.js | ^15.0.0 | React framework with App Router and Edge Functions | Latest App Router, Turbopack for 10x faster dev, built-in PWA support, optimal Japanese CDN performance |
| **UI Component Library** | shadcn/ui | ^0.8.0 | Accessible, customizable component system | Copy-paste components, full customization, works with Tailwind, reduces development time |
| **CSS Framework** | Tailwind CSS | ^4.0.0 | Utility-first styling with Japanese design tokens | Rapid prototyping, consistent spacing, easy Japanese cultural design adaptation |
| **State Management** | Zustand | ^4.4.0 | Lightweight state for group formation results | Simple API, TypeScript support, perfect for temporary session state and group assignments |
| **Backend Language** | TypeScript | ^5.3.0 | Unified language across frontend/backend | Code sharing, consistent patterns, single skillset for fullstack development |
| **Backend Framework** | Next.js API Routes | ^15.0.0 | Serverless API endpoints | Integrated with frontend, Vercel Edge Functions, optimal Japanese performance |
| **API Style** | REST + tRPC | tRPC v11 | Type-safe APIs with REST fallbacks | End-to-end type safety, excellent for group formation APIs, REST for external integrations |
| **Database** | PostgreSQL (Supabase) | v15 | Relational database with real-time features | ACID compliance for group assignments, built-in auth, real-time subscriptions for live updates |
| **Cache** | Vercel Edge Cache | Built-in | CDN caching for cultural scenarios | Automatic static asset caching, reduced server load for scenario content |
| **File Storage** | Supabase Storage | Built-in | Scenario images and cultural assets | Integrated with auth, CDN delivery, cost-effective for cultural content images |
| **Authentication** | Supabase Auth | Built-in | Anonymous + permanent user workflows | Native anonymous auth, seamless conversion, Japanese privacy compliance |
| **Frontend Testing** | Vitest + Testing Library | Vitest ^1.0 | Fast unit and component testing | Vite integration, Jest compatibility, excellent TypeScript support |
| **Backend Testing** | Vitest + Supertest | Vitest ^1.0 | API endpoint testing | Same test runner as frontend, consistent tooling, good async testing support |
| **E2E Testing** | Playwright | ^1.40.0 | Critical user flows and group formation | Excellent Japanese character support, reliable for complex workflows |
| **Build Tool** | Turbopack | Built-in Next.js | Ultra-fast development builds | 10x faster than Webpack, native Next.js 15 integration |
| **Bundler** | Next.js built-in | ^15.0.0 | Production optimization | Automatic code splitting, tree shaking, edge-optimized bundles |
| **Package Manager** | pnpm | ^8.10.0 | Fast, efficient dependency management | Disk space efficiency, faster installs, good monorepo support |
| **Monorepo Tool** | npm workspaces | Built-in | Simple feature-based organization | No additional complexity, sufficient for ConnectClass scope |
| **IaC Tool** | Vercel CLI | Latest | Infrastructure as code for deployments | Git-based deployments, environment management, preview deployments |
| **CI/CD** | GitHub Actions | Latest | Automated testing and deployment | Free for public repos, excellent Vercel integration, Japanese-friendly timing |
| **Monitoring** | Vercel Analytics | Built-in | Core Web Vitals and performance | Real user metrics, Japanese performance insights, cost-effective |
| **Logging** | Vercel Functions Logs | Built-in | Error tracking and debugging | Centralized logging, real-time monitoring, integrated with deployment |
| **Linting** | ESLint + Prettier | ESLint ^8.50 | Code quality and formatting | Consistent code style, TypeScript integration, team collaboration |
| **Type Checking** | TypeScript Compiler | ^5.3.0 | Compile-time error detection | Catch errors before runtime, better refactoring, AI-friendly codebase |

---

## Data Models

Based on the PRD requirements and user workflows, I'll define the core data models that will be shared between frontend and backend for ConnectClass.

### User

**Purpose:** Represents both anonymous students and permanent users, with seamless conversion workflow

**Key Attributes:**
- `id`: string (UUID) - Supabase user identifier
- `type`: 'anonymous' | 'permanent' - User account status
- `email`: string | null - Only for permanent users
- `created_at`: timestamp - Account creation time
- `anonymous_expires_at`: timestamp | null - 24-hour cleanup for anonymous users
- `display_name`: string | null - Optional friendly name
- `preferences`: object - UI language, cultural content preferences

#### TypeScript Interface
```typescript
interface User {
  id: string;
  type: 'anonymous' | 'permanent';
  email?: string | null;
  created_at: string;
  anonymous_expires_at?: string | null;
  display_name?: string | null;
  preferences: {
    language: 'ja' | 'en';
    cultural_content_preference?: string[];
  };
}
```

#### Relationships
- One-to-many with PersonalityAssessment
- Many-to-many with ClassSession (through participation)
- One-to-many with GroupMembership

### ClassSession

**Purpose:** Temporary classroom environment where students join for personality assessment and group formation

**Key Attributes:**
- `id`: string (UUID) - Session identifier
- `teacher_id`: string - Reference to teacher user
- `name`: string - Descriptive session name
- `join_code`: string - 6-digit alphanumeric code
- `status`: enum - Session lifecycle state
- `created_at`: timestamp - Session creation time
- `expires_at`: timestamp - 4-hour expiration
- `max_participants`: number - Class size limit
- `settings`: object - Session configuration

#### TypeScript Interface
```typescript
interface ClassSession {
  id: string;
  teacher_id: string;
  name: string;
  join_code: string;
  status: 'active' | 'assessment_phase' | 'grouping_phase' | 'completed' | 'expired';
  created_at: string;
  expires_at: string;
  max_participants: number;
  settings: {
    allow_retakes: boolean;
    auto_group_threshold: number;
    cultural_scenario_set: string;
  };
}
```

#### Relationships
- Belongs-to User (teacher)
- One-to-many with PersonalityAssessment
- One-to-many with Group

### CulturalScenario

**Purpose:** Japanese cultural scenarios used for personality assessment with HEXACO mapping

**Key Attributes:**
- `id`: string (UUID) - Scenario identifier
- `title`: string - Scenario title
- `description`: string - Scenario context description
- `category`: enum - Cultural theme categorization
- `status`: enum - Content approval workflow
- `created_at`: timestamp - Content creation time
- `approved_by`: string - Cultural consultant approval
- `recognition_rate`: number - Student relevance feedback
- `responses`: array - Four multiple choice options with HEXACO mapping

#### TypeScript Interface
```typescript
interface CulturalScenario {
  id: string;
  title: string;
  description: string;
  category: 'anime' | 'idol_culture' | 'school_life' | 'social_situations' | 'trending';
  status: 'draft' | 'review' | 'approved' | 'archived';
  created_at: string;
  approved_by?: string;
  recognition_rate: number;
  responses: {
    id: string;
    text: string;
    hexaco_mapping: {
      honesty_humility: number;
      emotionality: number;
      extraversion: number;
      agreeableness: number;
      conscientiousness: number;
      openness: number;
    };
  }[];
}
```

#### Relationships
- One-to-many with AssessmentResponse
- Many-to-one with User (cultural consultant)

### PersonalityAssessment

**Purpose:** Student's completed personality assessment with HEXACO scores and metadata

**Key Attributes:**
- `id`: string (UUID) - Assessment identifier
- `user_id`: string - Student who completed assessment
- `class_session_id`: string - Session context
- `hexaco_scores`: object - Six personality dimension scores
- `completed_at`: timestamp - Assessment completion time
- `time_taken_seconds`: number - Assessment duration
- `response_pattern_flags`: array - Quality validation flags
- `cultural_recognition_score`: number - How relevant scenarios felt to student

#### TypeScript Interface
```typescript
interface PersonalityAssessment {
  id: string;
  user_id: string;
  class_session_id: string;
  hexaco_scores: {
    honesty_humility: number;
    emotionality: number;
    extraversion: number;
    agreeableness: number;
    conscientiousness: number;
    openness: number;
  };
  completed_at: string;
  time_taken_seconds: number;
  response_pattern_flags: ('rushed' | 'random' | 'extreme' | 'consistent')[];
  cultural_recognition_score: number;
}
```

#### Relationships
- Belongs-to User (student)
- Belongs-to ClassSession
- One-to-many with AssessmentResponse
- One-to-one with GroupMembership

### Group

**Purpose:** Result of Fuzzy C-Means clustering with 4 compatible students and generated ice-breakers

**Key Attributes:**
- `id`: string (UUID) - Group identifier
- `class_session_id`: string - Session context
- `name`: string - Group identifier for teachers
- `compatibility_score`: number - Clustering quality metric
- `formation_algorithm`: string - Algorithm version used
- `created_at`: timestamp - Group creation time
- `ice_breakers`: array - AI-generated conversation starters
- `clustering_metadata`: object - Algorithm details for debugging

#### TypeScript Interface
```typescript
interface Group {
  id: string;
  class_session_id: string;
  name: string;
  compatibility_score: number;
  formation_algorithm: string;
  created_at: string;
  ice_breakers: {
    id: string;
    prompt: string;
    context: string;
    estimated_duration_minutes: number;
  }[];
  clustering_metadata: {
    algorithm_version: string;
    iterations: number;
    convergence_threshold: number;
    silhouette_score: number;
  };
}
```

#### Relationships
- Belongs-to ClassSession
- One-to-many with GroupMembership

### GroupMembership

**Purpose:** Junction table linking students to their assigned groups with role information

**Key Attributes:**
- `id`: string (UUID) - Membership identifier
- `group_id`: string - Group assignment
- `user_id`: string - Student member
- `personality_assessment_id`: string - Assessment that contributed to grouping
- `assigned_at`: timestamp - Group assignment time
- `member_role`: string - Optional role within group

#### TypeScript Interface
```typescript
interface GroupMembership {
  id: string;
  group_id: string;
  user_id: string;
  personality_assessment_id: string;
  assigned_at: string;
  member_role?: string;
}
```

#### Relationships
- Belongs-to Group
- Belongs-to User
- Belongs-to PersonalityAssessment

---

## API Specification

Based on our chosen tech stack (tRPC + REST fallbacks), I'll define the complete API specification for ConnectClass using tRPC routers with TypeScript interfaces.

### tRPC Router Definitions

Our API is organized into feature-based routers that mirror our folder structure, providing end-to-end type safety from frontend to backend.

```typescript
// Main API Router - Combines all feature routers
export const appRouter = router({
  auth: authRouter,
  classes: classRouter,
  scenarios: scenarioRouter,
  assessment: assessmentRouter,
  groups: groupRouter,
  icebreakers: icebreakerRouter,
  analytics: analyticsRouter,
});

export type AppRouter = typeof appRouter;
```

### Authentication Router

**Purpose:** Handles anonymous user creation, session management, and permanent account conversion

```typescript
export const authRouter = router({
  // Create anonymous user for class joining
  createAnonymousUser: publicProcedure
    .input(z.object({
      classJoinCode: z.string().length(6),
      userAgent: z.string().optional(),
    }))
    .output(z.object({
      user: UserSchema,
      accessToken: z.string(),
      sessionExpires: z.string(),
    }))
    .mutation(async ({ input }) => {
      // Creates anonymous Supabase user linked to class session
      // Returns JWT token for session management
    }),

  // Convert anonymous to permanent account
  convertToPermanent: protectedProcedure
    .input(z.object({
      email: z.string().email(),
      password: z.string().min(8),
      keepPersonalData: z.boolean(),
      allowTeacherArchive: z.boolean(),
      archiveDurationDays: z.enum(['30', '90', '365']).optional(),
    }))
    .output(z.object({
      user: UserSchema,
      migrationSuccess: z.boolean(),
      dataRetained: z.array(z.string()),
    }))
    .mutation(async ({ input, ctx }) => {
      // Converts anonymous user using Supabase updateUser()
      // Migrates personality data based on consent
      // Creates teacher archive if permitted
    }),
});
```

### Class Session Router

**Purpose:** Teacher class creation and management, student joining workflows

```typescript
export const classRouter = router({
  // Teacher creates new class session
  create: teacherProcedure
    .input(z.object({
      name: z.string().min(1).max(100),
      maxParticipants: z.number().min(4).max(50).default(32),
      settings: z.object({
        allowRetakes: z.boolean().default(false),
        autoGroupThreshold: z.number().min(8).max(50).default(16),
        culturalScenarioSet: z.string().default('mixed'),
      }),
    }))
    .output(z.object({
      session: ClassSessionSchema,
      joinCode: z.string(),
      expiresAt: z.string(),
    }))
    .mutation(async ({ input, ctx }) => {
      // Generates unique 6-digit join code
      // Creates session with 4-hour expiration
      // Sets up real-time subscription channels
    }),

  // Student joins class with code
  join: publicProcedure
    .input(z.object({
      joinCode: z.string().length(6),
      displayName: z.string().max(50).optional(),
    }))
    .output(z.object({
      session: ClassSessionSchema,
      participantCount: z.number(),
      userRole: z.enum(['student', 'teacher']),
      nextStep: z.enum(['wait', 'assess', 'groups', 'complete']),
    }))
    .mutation(async ({ input }) => {
      // Validates join code and session status
      // Creates anonymous user if needed
      // Adds participant to real-time channel
    }),
});
```

---

## Unified Project Structure

This feature-based monorepo structure enables AI-driven development with clear feature boundaries and predictable organization patterns.

```
connectclass/
├── .github/                          # CI/CD workflows
│   └── workflows/
│       ├── ci.yaml                   # Test and lint
│       ├── deploy-staging.yaml       # Preview deployments
│       └── deploy-production.yaml    # Production releases
│
├── apps/
│   └── web/                          # Next.js 15 application
│       ├── src/
│       │   ├── app/                  # Next.js App Router
│       │   │   ├── (student)/        # Student route group
│       │   │   │   ├── join/
│       │   │   │   │   └── page.tsx  # Class joining flow
│       │   │   │   ├── assess/
│       │   │   │   │   └── page.tsx  # Personality assessment
│       │   │   │   ├── groups/
│       │   │   │   │   └── page.tsx  # Group assignment
│       │   │   │   └── layout.tsx    # Student layout
│       │   │   │
│       │   │   ├── (teacher)/        # Teacher route group
│       │   │   │   ├── dashboard/
│       │   │   │   │   └── page.tsx  # Teacher dashboard
│       │   │   │   ├── session/
│       │   │   │   │   └── [id]/
│       │   │   │   │       └── page.tsx # Session monitoring
│       │   │   │   ├── archive/
│       │   │   │   │   └── page.tsx  # Historical sessions
│       │   │   │   └── layout.tsx    # Teacher layout
│       │   │   │
│       │   │   ├── api/              # API routes
│       │   │   │   ├── trpc/
│       │   │   │   │   └── [trpc]/
│       │   │   │   │       └── route.ts # Main tRPC handler
│       │   │   │   ├── auth/
│       │   │   │   │   └── callback/
│       │   │   │   │       └── route.ts # Supabase callback
│       │   │   │   └── groups/
│       │   │   │       └── formation/
│       │   │   │           └── route.ts # Background clustering
│       │   │   │
│       │   │   ├── globals.css       # Global styles
│       │   │   ├── layout.tsx        # Root layout
│       │   │   └── page.tsx          # Landing page
│       │   │
│       │   ├── features/             # 🎯 CORE FEATURE MODULES
│       │   │   ├── student-joining/
│       │   │   │   ├── components/
│       │   │   │   │   ├── JoinCodeInput.tsx
│       │   │   │   │   ├── WaitingRoom.tsx
│       │   │   │   │   └── ConnectionStatus.tsx
│       │   │   │   ├── hooks/
│       │   │   │   │   ├── useClassJoining.ts
│       │   │   │   │   └── useRealTimeParticipants.ts
│       │   │   │   ├── api/
│       │   │   │   │   └── joinRoutes.ts
│       │   │   │   ├── types/
│       │   │   │   │   └── joining.types.ts
│       │   │   │   └── README.md     # "How class joining works"
│       │   │   │
│       │   │   ├── personality-assessment/
│       │   │   │   ├── components/
│       │   │   │   │   ├── ScenarioCard.tsx
│       │   │   │   │   ├── ProgressIndicator.tsx
│       │   │   │   │   ├── ResponseOptions.tsx
│       │   │   │   │   └── ResultsDisplay.tsx
│       │   │   │   ├── cultural-scenarios/
│       │   │   │   │   ├── ScenarioManager.ts
│       │   │   │   │   ├── CulturalValidator.ts
│       │   │   │   │   └── ContentRotation.ts
│       │   │   │   ├── api/
│       │   │   │   │   └── assessmentRoutes.ts
│       │   │   │   ├── algorithms/
│       │   │   │   │   ├── HexacoScoring.ts
│       │   │   │   │   └── QualityValidation.ts
│       │   │   │   ├── stores/
│       │   │   │   │   └── assessmentStore.ts
│       │   │   │   ├── types/
│       │   │   │   │   └── assessment.types.ts
│       │   │   │   └── README.md     # "How personality assessment works"
│       │   │   │
│       │   │   ├── group-formation/
│       │   │   │   ├── algorithms/
│       │   │   │   │   ├── FuzzyCMeans.ts
│       │   │   │   │   ├── ClusteringValidator.ts
│       │   │   │   │   └── GroupOptimizer.ts
│       │   │   │   ├── components/
│       │   │   │   │   ├── GroupRevealAnimation.tsx
│       │   │   │   │   ├── CompatibilityDisplay.tsx
│       │   │   │   │   └── GroupMemberCard.tsx
│       │   │   │   ├── api/
│       │   │   │   │   └── groupingRoutes.ts
│       │   │   │   ├── database/
│       │   │   │   │   └── groupRepository.ts
│       │   │   │   ├── stores/
│       │   │   │   │   └── groupFormationStore.ts
│       │   │   │   ├── types/
│       │   │   │   │   └── grouping.types.ts
│       │   │   │   └── README.md     # "How group formation works"
│       │   │   │
│       │   │   ├── ice-breaker-generation/
│       │   │   │   ├── api/
│       │   │   │   │   └── icebreakerRoutes.ts
│       │   │   │   ├── components/
│       │   │   │   │   ├── IceBreakerCard.tsx
│       │   │   │   │   ├── ActivityTimer.tsx
│       │   │   │   │   └── RegenerateButton.tsx
│       │   │   │   ├── prompts/
│       │   │   │   │   ├── JapaneseCulturalPrompts.ts
│       │   │   │   │   └── PersonalityBasedPrompts.ts
│       │   │   │   ├── fallbacks/
│       │   │   │   │   └── PrewrittenIceBreakers.ts
│       │   │   │   ├── services/
│       │   │   │   │   └── OpenAIService.ts
│       │   │   │   ├── types/
│       │   │   │   │   └── icebreaker.types.ts
│       │   │   │   └── README.md     # "How to update ice-breaker prompts"
│       │   │   │
│       │   │   ├── teacher-dashboard/
│       │   │   │   ├── components/
│       │   │   │   │   ├── ClassCreation.tsx
│       │   │   │   │   ├── ParticipantMonitor.tsx
│       │   │   │   │   ├── GroupFormationTrigger.tsx
│       │   │   │   │   └── SessionResults.tsx
│       │   │   │   ├── api/
│       │   │   │   │   └── teacherRoutes.ts
│       │   │   │   ├── stores/
│       │   │   │   │   └── teacherDashboardStore.ts
│       │   │   │   ├── types/
│       │   │   │   │   └── teacher.types.ts
│       │   │   │   └── README.md     # "Teacher workflow and controls"
│       │   │   │
│       │   │   └── content-management/
│       │   │       ├── admin-ui/
│       │   │       │   ├── ScenarioEditor.tsx
│       │   │       │   └── ApprovalWorkflow.tsx
│       │   │       ├── approval-workflow/
│       │   │       │   ├── ContentValidator.ts
│       │   │       │   └── CulturalReview.ts
│       │   │       ├── japanese-scenarios/
│       │   │       │   ├── AnimeScenarios.json
│       │   │       │   ├── SchoolLifeScenarios.json
│       │   │       │   └── SocialScenarios.json
│       │   │       ├── api/
│       │   │       │   └── contentRoutes.ts
│       │   │       ├── types/
│       │   │       │   └── content.types.ts
│       │   │       └── README.md     # "How to add/update cultural content"
│       │   │
│       │   ├── shared/               # Only truly shared code
│       │   │   ├── ui/               # shadcn/ui components
│       │   │   │   ├── button.tsx
│       │   │   │   ├── card.tsx
│       │   │   │   ├── modal.tsx
│       │   │   │   ├── loading-spinner.tsx
│       │   │   │   └── index.ts
│       │   │   ├── auth/
│       │   │   │   ├── supabaseClient.ts
│       │   │   │   ├── authStore.ts
│       │   │   │   └── middleware.ts
│       │   │   ├── api/
│       │   │   │   ├── trpc.ts
│       │   │   │   └── queryClient.ts
│       │   │   ├── database/
│       │   │   │   ├── schema.ts
│       │   │   │   └── migrations/
│       │   │   ├── utils/
│       │   │   │   ├── cultural.ts
│       │   │   │   ├── validation.ts
│       │   │   │   └── performance.ts
│       │   │   ├── hooks/
│       │   │   │   ├── useRealTime.ts
│       │   │   │   └── useLocalStorage.ts
│       │   │   ├── types/
│       │   │   │   ├── global.types.ts
│       │   │   │   └── api.types.ts
│       │   │   └── constants/
│       │   │       ├── cultural.ts
│       │   │       └── config.ts
│       │   │
│       │   └── server/               # Backend services
│       │       ├── routers/
│       │       │   ├── auth.ts
│       │       │   ├── classes.ts
│       │       │   ├── assessment.ts
│       │       │   ├── groups.ts
│       │       │   ├── icebreakers.ts
│       │       │   ├── analytics.ts
│       │       │   └── _app.ts       # Main router
│       │       ├── services/
│       │       │   ├── authService.ts
│       │       │   ├── personalityService.ts
│       │       │   ├── clusteringService.ts
│       │       │   ├── aiService.ts
│       │       │   └── realtimeService.ts
│       │       ├── middleware/
│       │       │   ├── auth.ts
│       │       │   ├── rateLimit.ts
│       │       │   ├── cors.ts
│       │       │   └── logging.ts
│       │       ├── utils/
│       │       │   ├── validation.ts
│       │       │   ├── cultural.ts
│       │       │   ├── fuzzyMeans.ts
│       │       │   └── hexaco.ts
│       │       └── types/
│       │           ├── api.ts
│       │           ├── database.ts
│       │           └── external.ts
│       │
│       ├── public/                   # Static assets
│       │   ├── images/
│       │   │   ├── cultural/         # Japanese cultural imagery
│       │   │   └── icons/
│       │   ├── fonts/                # Japanese typography
│       │   └── manifests/
│       │       └── pwa-manifest.json
│       │
│       ├── tests/                    # Test organization
│       │   ├── unit/
│       │   │   ├── features/         # Feature-specific unit tests
│       │   │   │   ├── personality-assessment/
│       │   │   │   │   ├── HexacoScoring.test.ts
│       │   │   │   │   └── ScenarioCard.test.tsx
│       │   │   │   ├── group-formation/
│       │   │   │   │   ├── FuzzyCMeans.test.ts
│       │   │   │   │   └── GroupReveal.test.tsx
│       │   │   │   └── ice-breaker-generation/
│       │   │   │       └── OpenAIService.test.ts
│       │   │   └── shared/
│       │   │       └── utils/
│       │   │           └── cultural.test.ts
│       │   ├── integration/
│       │   │   ├── api/
│       │   │   │   ├── assessment.test.ts
│       │   │   │   └── groups.test.ts
│       │   │   └── database/
│       │   │       └── groupRepository.test.ts
│       │   ├── e2e/
│       │   │   ├── student-journey.spec.ts
│       │   │   ├── teacher-workflow.spec.ts
│       │   │   └── group-formation.spec.ts
│       │   └── setup/
│       │       ├── test-db.ts
│       │       └── playwright.config.ts
│       │
│       ├── package.json
│       ├── next.config.js
│       ├── tailwind.config.js
│       ├── tsconfig.json
│       └── .env.example
│
├── packages/                         # Shared packages (future)
│   ├── config/                       # Shared configuration
│   │   ├── eslint/
│   │   ├── typescript/
│   │   └── tailwind/
│   └── ui/                          # Shared UI library (future)
│       └── package.json
│
├── infrastructure/                   # Infrastructure as Code
│   ├── vercel/
│   │   ├── vercel.json
│   │   └── project.json
│   ├── supabase/
│   │   ├── config.toml
│   │   ├── migrations/
│   │   └── seed.sql
│   └── monitoring/
│       └── vercel-analytics.json
│
├── scripts/                          # Development and deployment scripts
│   ├── setup.sh                     # Initial project setup
│   ├── dev.sh                       # Development environment
│   ├── test.sh                      # Run all tests
│   ├── build.sh                     # Production build
│   ├── deploy-staging.sh            # Staging deployment
│   └── cultural-content-update.sh   # Monthly scenario rotation
│
├── docs/                            # Project documentation
│   ├── architecture/
│   │   ├── fullstack-architecture.md # This document
│   │   ├── feature-organization.md
│   │   └── ai-development-guide.md
│   ├── features/
│   │   ├── personality-assessment.md
│   │   ├── group-formation.md
│   │   └── cultural-scenarios.md
│   ├── deployment/
│   │   ├── vercel-setup.md
│   │   └── supabase-setup.md
│   ├── prd.md                       # Product Requirements Document
│   └── api-documentation.md
│
├── .ai/                             # AI development guides
│   ├── fuzzy-cmeans-grouping-guide.md
│   ├── hexaco-implementation-guide.md
│   ├── cultural-content-guide.md
│   └── feature-development-guide.md
│
├── .github/
├── .gitignore
├── package.json                     # Root workspace configuration
├── pnpm-workspace.yaml             # Workspace definition
├── README.md
└── CLAUDE.md                        # AI development context
```

---

## Development Workflow

### Local Development Setup

#### Prerequisites
```bash
# Install Node.js, pnpm, and development tools
node --version    # v20.x or higher
pnpm --version    # v8.x or higher
git --version     # Latest stable
```

#### Initial Setup
```bash
# Clone and setup project
git clone <repository-url> connectclass
cd connectclass

# Install dependencies across workspace
pnpm install

# Setup environment variables
cp apps/web/.env.example apps/web/.env.local
# Edit .env.local with Supabase and OpenAI credentials

# Setup Supabase local development
pnpm supabase start
pnpm db:reset

# Generate database types
pnpm db:types

# Start development server
pnpm dev
```

#### Development Commands
```bash
# Start all services (Next.js + Supabase)
pnpm dev

# Start only frontend (requires external Supabase)
pnpm dev:web

# Start only database
pnpm dev:db

# Run tests
pnpm test          # All tests
pnpm test:unit     # Unit tests only
pnpm test:e2e      # End-to-end tests
pnpm test:watch    # Watch mode

# Build and validate
pnpm build         # Production build
pnpm lint          # Code linting
pnpm type-check    # TypeScript validation

# Database operations
pnpm db:migrate    # Apply migrations
pnpm db:reset      # Reset to clean state
pnpm db:seed       # Add test data
pnpm db:types      # Generate TypeScript types
```

### Environment Configuration

#### Required Environment Variables

```bash
# Frontend (.env.local)
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_VERCEL_URL=your-vercel-app.vercel.app

# Backend (.env)
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
SUPABASE_JWT_SECRET=your-jwt-secret
OPENAI_API_KEY=your-openai-key
DATABASE_URL=postgresql://postgres:password@localhost:54322/postgres

# Shared
NODE_ENV=development
VERCEL_ENV=development
```

---

## Deployment Architecture

### Deployment Strategy

**Frontend Deployment:**
- **Platform:** Vercel with Edge Functions
- **Build Command:** `pnpm build`
- **Output Directory:** `apps/web/.next`
- **CDN/Edge:** Automatic Vercel Edge Network with Tokyo/Osaka nodes

**Backend Deployment:**
- **Platform:** Vercel Edge Functions (integrated with frontend)
- **Build Command:** Automatic with frontend build
- **Deployment Method:** Git-based with preview deployments

### Environments

| Environment | Frontend URL | Backend URL | Purpose |
|-------------|-------------|-------------|---------|
| Development | http://localhost:3000 | http://localhost:3000/api | Local development |
| Staging | https://staging-connectclass.vercel.app | https://staging-connectclass.vercel.app/api | Pre-production testing |
| Production | https://connectclass.app | https://connectclass.app/api | Live environment |

---

## Security and Performance

### Security Requirements

**Frontend Security:**
- CSP Headers: `default-src 'self'; script-src 'self' 'unsafe-inline' vercel.live; style-src 'self' 'unsafe-inline'`
- XSS Prevention: Input sanitization with DOMPurify, React's built-in protections
- Secure Storage: Session tokens in httpOnly cookies, sensitive data in encrypted localStorage

**Backend Security:**
- Input Validation: Zod schemas for all API inputs with cultural content validation
- Rate Limiting: 60 requests/minute per IP for anonymous users, 300/minute for teachers
- CORS Policy: Restricted to ConnectClass domains and localhost for development

**Authentication Security:**
- Token Storage: JWT in httpOnly cookies with 4-hour expiration
- Session Management: Supabase Row Level Security with anonymous user auto-cleanup
- Password Policy: Minimum 8 characters, optional complexity for Japanese users

### Performance Optimization

**Frontend Performance:**
- Bundle Size Target: <500KB initial bundle, <200KB per route chunk
- Loading Strategy: Progressive enhancement with Next.js App Router, lazy loading for cultural scenarios
- Caching Strategy: SWR for API data, 30-second stale-while-revalidate, Service Worker for offline scenarios

**Backend Performance:**
- Response Time Target: <100ms for API calls in Japan, <200ms for AI ice-breaker generation
- Database Optimization: Indexed queries on session_id, group formation materialized views
- Caching Strategy: Vercel Edge Cache for cultural scenarios, Redis for session state if needed

---

## Testing Strategy

### Testing Pyramid

```
        E2E Tests (Playwright)
       /                    \
    Integration Tests
   /                        \
Frontend Unit Tests    Backend Unit Tests
(Vitest + Testing Lib)    (Vitest + Supertest)
```

### Test Organization

#### Frontend Tests
```
tests/unit/features/
├── personality-assessment/
│   ├── components/
│   │   ├── ScenarioCard.test.tsx     # Component behavior
│   │   └── ResultsDisplay.test.tsx   # Cultural result formatting
│   ├── algorithms/
│   │   └── HexacoScoring.test.ts     # Personality calculation accuracy
│   └── stores/
│       └── assessmentStore.test.ts   # State management logic
├── group-formation/
│   ├── algorithms/
│   │   └── FuzzyCMeans.test.ts       # Clustering algorithm validation
│   └── components/
│       └── GroupReveal.test.tsx      # Animation and display logic
└── shared/
    ├── utils/
    │   └── cultural.test.ts          # Japanese localization helpers
    └── hooks/
        └── useRealTime.test.ts       # Supabase subscription handling
```

#### Backend Tests
```
tests/integration/
├── api/
│   ├── assessment.test.ts            # Personality assessment API
│   ├── groups.test.ts               # Group formation endpoints
│   └── auth.test.ts                 # Anonymous user workflows
├── services/
│   ├── personalityService.test.ts   # HEXACO scoring business logic
│   ├── clusteringService.test.ts    # Fuzzy C-Means implementation
│   └── aiService.test.ts            # OpenAI integration
└── database/
    ├── groupRepository.test.ts      # Data access layer
    └── migrations.test.ts           # Schema validation
```

#### E2E Tests
```
tests/e2e/
├── student-journey.spec.ts          # Complete student flow: join → assess → groups
├── teacher-workflow.spec.ts         # Teacher flow: create → monitor → results
├── group-formation.spec.ts          # Full clustering and ice-breaker generation
├── cultural-scenarios.spec.ts       # Japanese content display and interaction
└── real-time-updates.spec.ts        # Live session synchronization
```

---

## Monitoring and Observability

### Monitoring Stack

- **Frontend Monitoring:** Vercel Analytics with Core Web Vitals tracking optimized for Japanese users
- **Backend Monitoring:** Vercel Functions logs with custom metrics for clustering performance
- **Error Tracking:** Vercel Error Tracking with cultural context preservation
- **Performance Monitoring:** Real User Monitoring (RUM) for Japanese network conditions

### Key Metrics

**Frontend Metrics:**
- Core Web Vitals (LCP, FID, CLS) with Japanese mobile network benchmarks
- JavaScript errors with cultural scenario context
- API response times for personality assessment and group formation
- User interactions: assessment completion rates, cultural recognition scores

**Backend Metrics:**
- Request rate and response times by feature (assessment, grouping, ice-breakers)
- Error rate with categorization (validation, clustering, AI generation)
- Database query performance for group formation operations
- OpenAI API response times and success rates

**Business Metrics:**
- Session completion rates (join → assess → groups)
- Teacher satisfaction with group formation quality
- Student engagement with cultural scenarios
- Account conversion rates (anonymous → permanent)

---

## Coding Standards

### Critical Fullstack Rules

- **Type Sharing:** Always define types in `shared/types/` and import across features - prevents frontend/backend type mismatches
- **API Calls:** Never make direct HTTP calls - use tRPC client for type safety and error handling
- **Environment Variables:** Access only through `shared/config/` objects, never `process.env` directly in components
- **Error Handling:** All API routes must use standardized `ConnectClassError` wrapper for consistent client handling
- **State Updates:** Never mutate Zustand state directly - use actions for proper subscription notifications
- **Cultural Content:** All Japanese text must use `shared/utils/cultural.ts` helpers for proper formatting and validation
- **Real-time Updates:** Use `shared/hooks/useRealTime.ts` wrapper, never direct Supabase subscriptions in components
- **Feature Isolation:** Components can only import from their own feature or `shared/` - no cross-feature dependencies

### Naming Conventions

| Element | Frontend | Backend | Example |
|---------|----------|---------|---------|
| Components | PascalCase | - | `PersonalityAssessment.tsx` |
| Hooks | camelCase with 'use' | - | `useGroupFormation.ts` |
| API Routes | - | kebab-case | `/api/group-formation` |
| Database Tables | - | snake_case | `personality_assessments` |
| Feature Folders | kebab-case | kebab-case | `personality-assessment/` |
| tRPC Procedures | camelCase | camelCase | `formGroups`, `getAssignment` |

---

## Error Handling Strategy

### Error Response Format

```typescript
interface ApiError {
  error: {
    code: 'VALIDATION_ERROR' | 'SESSION_EXPIRED' | 'CLUSTERING_FAILED' | 'AI_SERVICE_ERROR';
    message: string;
    details?: Record<string, any>;
    timestamp: string;
    requestId: string;
    culturalContext?: {
      japaneseMessage: string;
      suggestedAction: string;
    };
  };
}
```

---

**🎯 This comprehensive ConnectClass Fullstack Architecture provides:**

✅ **AI-Driven Development Ready:** Feature-based structure with clear boundaries
✅ **Japanese Market Optimized:** Cultural scenarios, edge performance, privacy compliance
✅ **Scalable Foundation:** Clean architecture supporting growth from 100 to 100,000+ users
✅ **Type-Safe Fullstack:** End-to-end TypeScript with tRPC for bulletproof development
✅ **Real-Time Classroom Experience:** Live group formation and session management
✅ **Production-Ready:** Comprehensive testing, monitoring, and deployment strategies

**The architecture is now complete and ready for implementation! 🚀**