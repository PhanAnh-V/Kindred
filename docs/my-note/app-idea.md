# VibeCheck Product Requirements Document

## 1. Product Overview

**Product Name**: VibeCheck  
**Purpose**: Facilitate real-life connections between students and strangers through AI-powered group matching based on personality and interests.

## 2. User Roles

### 2.1 Session Creator (Authenticated User)
- Has a registered account with OAuth login
- Can create and manage multiple sessions
- Has access to group management and analytics

### 2.2 Guest (Unauthenticated User)
- No account required
- Joins sessions using Session ID + Password
- Limited access to only their assigned session
- Can upgrade to full account after session

## 3. Core Features

### 3.1 Authentication System
- **OAuth Integration**: Support for Google, GitHub, or other providers, Like Line
- **Guest Access**: Session-based authentication using Session ID + Password
- **Account Conversion**: Guests can create accounts post-session to retain data

### 3.2 Session Management

#### 3.2.1 Session Creation (Creator Only)
- Generate unique Session ID
- Set session password
- Configure session settings (duration, max participants, etc.)
- Share session credentials with intended guests

#### 3.2.2 Session Access (Guests)
- Landing page with two options:
  1. Login/Signup for registered users
  2. Join Session form (Session ID + Password)
- Guest authentication creates temporary session token
- Guests are restricted to their specific session

### 3.3 Activity Flow

#### 3.3.1 Questionnaire Activity
- **Questions**: 6 pre-designed questions about interests, personality, goals
- **Submission**: Guests answer and submit responses
- **Processing**: Responses stored and queued for AI analysis
- Also have plan in the future for more activity then just asking questions (Still dont know what could it be, And if so the session creation function is gonna be very sotipicated)

#### 3.3.2 AI Profile Generation
- **Input**: Guest's answers to 6 questions
- **Output**: 
  - Personality summary
  - Interest analysis
  - Communication style insights
  - Compatibility factors

#### 3.3.3 AI Group Formation
- **Trigger**: Creator clicks "Create Groups" button
- **Algorithm**: 
  - Analyze all guest profiles
  - Identify commonalities and complementary traits
  - Form groups of 2-4 people maximum
- **Output**: Optimized groups based on compatibility

#### 3.3.4 Ice Breaker Generation
- **Trigger**: Creator clicks "Ice Breaker" button for a group
- **Process**: 
  - AI analyzes group members' profiles
  - Generates tiered questions (surface → deep)
  - Questions specifically tailored to group dynamics
- **Output**: 4-5 progressive conversation starters

### 3.4 Post-Session Features
- **Account Creation Prompt**: Guests invited to create accounts
- **Data Retention**: Save profile analysis and group history
- **Session History**: View past sessions and connections

## 4. Technical Requirements

### 4.1 Frontend
- **Framework**: Next.js 14+ with App Router
- **Styling**: Tailwind CSS
- **State Management**: React Context or Zustand
- **UI Components**: shadcn/ui or similar

### 4.2 Backend 
- **API**: Next.js API Routes (serverless functions)
- **Database**: 
  - PostgreSQL (via Supabase or Neon) for relational data
  - OR MongoDB for flexible schema
- **Authentication**: NextAuth.js for OAuth and session management
- **AI Integration**: OpenAI API or Anthropic Claude API
(I need advice on this one, I am just going to use security auth from database, vercel for deployment, and integate stripe for payment, but those are just what i know that there is. Depend on the complexity of the app I will follow the advice from AI to how to manage the app bacnkend)
### 4.3 Data Models

#### User Model
```javascript
{
  id: string,
  email: string,
  name: string,
  oauthProvider: string,
  createdSessions: [Session],
  participatedSessions: [Session]
}
```

#### Session Model
```javascript
{
  id: string,
  password: string,
  creatorId: string,
  title: string,
  status: 'active' | 'completed',
  guests: [Guest],
  groups: [Group],
  createdAt: Date,
  expiresAt: Date
}
```

#### Guest Model
```javascript
{
  id: string,
  sessionId: string,
  tempName: string,
  answers: [Answer],
  profile: Profile,
  groupId: string | null,
  convertedToUser: boolean
}
```

#### Profile Model
```javascript
{
  guestId: string,
  summary: string,
  interests: string[],
  personality: string,
  communicationStyle: string,
  analysisDate: Date
}
```

#### Group Model
```javascript
{
  id: string,
  sessionId: string,
  members: [Guest],
  iceBreakers: [Question],
  createdAt: Date
}
```

## 5. Security Considerations

### 5.1 Access Control
- Guests can ONLY access their assigned session
- Session passwords should be hashed
- Temporary tokens expire after session ends
- Rate limiting on session join attempts

### 5.2 Data Privacy
- Guest data is temporary unless converted to account
- AI prompts should not expose other users' raw answers
- GDPR compliance for data deletion requests

## 6. MVP Scope

### Phase 1 (MVP)
1. Basic OAuth login for creators
2. Session creation with ID/password
3. Guest access system
4. 6-question form
5. Simple AI profile generation
6. Manual group creation (no AI)

### Phase 2
1. AI-powered group formation
2. Ice breaker generation
3. Guest-to-user conversion
4. Basic analytics for creators

### Phase 3
1. Multiple activity types
2. Real-time collaboration features
3. Session templates
4. Advanced matching algorithms

## 7. Success Metrics
- Session completion rate
- Guest-to-user conversion rate
- Average group interaction time
- User satisfaction scores
- Number of successful connections made