# WebyPost Android App --- Complete Technical Recommendation

## 1. Executive Recommendation

Build WebyPost as a **real Android application**, not as a WebView
wrapper.

The recommended architecture is:

``` text
                    WEBYPOST
                       │
              ┌────────┴────────┐
              │                 │
        Web Browser        Android App
              │                 │
       Existing Web UI     React Native
              │                 │
              └────────┬────────┘
                       │
                  HTTPS REST API
                       │
                  PHP API Layer
                       │
                    MySQL
```

The existing WebyPost MySQL database remains the **source of truth**.

The existing PHP backend/business logic should be reused wherever
practical. The Android app should consume a clean API layer rather than
connecting directly to MySQL.

The Android UI should reproduce the existing WebyPost mobile-responsive
design while adding:

-   Native-feeling navigation
-   Smooth page transitions
-   Native gestures
-   Fast feed scrolling
-   API caching
-   Skeleton/loading states
-   Optimistic interactions
-   Image/video handling
-   Android back navigation
-   Deep links
-   Push notifications
-   Production-grade error handling

The objective is:

> **Keep WebyPost visually recognizable and consistent, but make the
> Android experience feel like a purpose-built social network rather
> than a website inside an app.**

------------------------------------------------------------------------

# 2. Recommended Development Target

## MVP

**20--30 working days**

Suitable for:

-   Authentication
-   Feed
-   Profiles
-   Posts
-   Likes/comments
-   Basic search
-   Basic notifications
-   Basic navigation
-   Android build

## Complete WebyPost Android Application

**40--55 working days**

Suitable for:

-   Complete social feed
-   All major post types
-   Profiles
-   Social interactions
-   Search
-   Notifications
-   Messaging
-   Media
-   Listings
-   Articles
-   Caching
-   Native transitions
-   Error handling
-   Performance optimization
-   Android release candidate

## Polished Production Application

**55--75 working days**

Includes additional:

-   Extensive device testing
-   Performance optimization
-   Edge-case handling
-   Upload reliability
-   Offline/cache behavior
-   Deep linking
-   Push notification refinement
-   Crash/error monitoring
-   Release hardening
-   Play Store preparation

### Recommended planning target

**6--8 weeks for a serious first production release.**

Do not plan the project around a 10--15 day AI-generated build. Claude
Code can dramatically accelerate implementation, but integration,
debugging, API issues, device testing and UX refinement still require
substantial time.

------------------------------------------------------------------------

# 3. Claude Code Usage Estimate

Claude Code usage is difficult to express as a literal percentage
because it depends on:

-   Number of existing PHP files
-   Complexity of the MySQL schema
-   API quality
-   Existing authentication
-   Amount of legacy code
-   Number of screens
-   Number of bugs
-   How much context Claude needs
-   Number of iterations
-   Amount of testing

A useful planning budget is:

  Phase                       Approximate Project Usage
  ------------------------- ---------------------------
  Architecture and audit                          5--8%
  API layer                                     10--15%
  React Native foundation                        8--10%
  Core screens                                  15--20%
  Social features                               15--20%
  Messaging/notifications                        8--12%
  Media/upload                                    5--8%
  Testing/debugging                             15--20%
  Optimization/release                           8--12%

The important point is:

> **Do not try to complete the project in one giant Claude Code
> conversation.**

Use separate development phases with persistent documentation.

------------------------------------------------------------------------

# 4. Recommended Technology Stack

## Mobile Frontend

``` text
React Native
TypeScript
React Navigation
React Native Reanimated
React Native Gesture Handler
TanStack Query
Zustand
Axios
FlashList
```

### Why React Native?

It provides:

-   Native Android UI capabilities
-   Good animation support
-   Shared TypeScript code
-   Large ecosystem
-   Strong navigation options
-   Good performance when implemented correctly
-   Future possibility of an iOS version

------------------------------------------------------------------------

# 5. Why Not Simply Use WebView?

A WebView wrapper is tempting because WebyPost already exists.

However, it will not give you the experience you are asking for.

A WebView approach generally looks like:

``` text
Android
   ↓
WebView
   ↓
webypost.com
```

Your desired architecture should instead be:

``` text
Android
   ↓
React Native UI
   ↓
TypeScript API client
   ↓
PHP API
   ↓
MySQL
```

The second architecture provides much more control over:

-   Transitions
-   Gestures
-   Loading states
-   Feed performance
-   Native navigation
-   Media
-   Notifications
-   Android back behavior
-   Local cache
-   Future native features

------------------------------------------------------------------------

# 6. Reuse the Existing WebyPost Backend

Do not rebuild the entire WebyPost backend.

If the existing system already has:

-   Users
-   Profiles
-   Posts
-   Comments
-   Likes
-   Followers
-   Listings
-   Articles
-   Messages
-   Notifications
-   Media

then expose those capabilities through APIs.

The database remains:

``` text
MySQL
```

The Android application communicates through:

``` text
HTTPS REST API
```

The PHP application remains responsible for:

-   Authentication
-   Authorization
-   Validation
-   Business rules
-   Database access
-   File/media handling
-   Security
-   Data transformation

------------------------------------------------------------------------

# 7. Recommended API Architecture

Create a versioned API:

``` text
/api/v1/
```

Example:

``` text
/api/v1/auth/login
/api/v1/auth/register
/api/v1/auth/logout

/api/v1/feed
/api/v1/posts
/api/v1/posts/{id}

/api/v1/posts/{id}/like
/api/v1/posts/{id}/comment
/api/v1/posts/{id}/repost

/api/v1/users/{id}
/api/v1/users/{id}/followers
/api/v1/users/{id}/following

/api/v1/profile
/api/v1/profile/update

/api/v1/messages
/api/v1/messages/{id}

/api/v1/notifications

/api/v1/search

/api/v1/listings
/api/v1/articles
```

Avoid exposing raw database queries to the mobile application.

------------------------------------------------------------------------

# 8. Authentication

Do not make the Android application depend on a fragile browser-session
implementation.

Recommended:

``` text
Login
  ↓
API authentication
  ↓
Access token
  ↓
Secure device storage
  ↓
Authenticated API requests
```

The exact token implementation should be chosen after auditing the
current WebyPost authentication system.

The mobile application should support:

-   Login
-   Registration
-   Logout
-   Session restoration
-   Token expiration
-   Token refresh where applicable
-   Unauthorized response handling
-   Secure credential storage
-   Forgot password

------------------------------------------------------------------------

# 9. WebyPost Design System

The existing WebyPost mobile web UI should become the **canonical visual
reference**.

Do not ask Claude Code to invent a generic social media design.

Instead:

> Reproduce the existing WebyPost visual identity and mobile-responsive
> design, then adapt it into native React Native components.

Existing WebyPost branding should remain recognizable.

Suggested design tokens:

``` text
Primary:
Emerald Green

Secondary:
Light Yellow

Background:
White / light neutral

Text:
Dark charcoal

Cards:
Moderately rounded

Typography:
Modern sans-serif

Spacing:
8 / 12 / 16 / 24 / 32 px system
```

The exact values should be extracted from the existing WebyPost site
rather than guessed.

------------------------------------------------------------------------

# 10. Core Mobile Navigation

Recommended structure:

``` text
┌─────────────────────────┐
│ WebyPost          🔔 💬 │
├─────────────────────────┤
│                         │
│       SCREEN            │
│                         │
│                         │
│                         │
├─────────────────────────┤
│ 🏠    🔎    ➕    💬  👤 │
└─────────────────────────┘
```

Possible primary destinations:

``` text
Home
Search
Create
Messages
Profile
```

Notifications can remain accessible from the top navigation.

The exact navigation should follow the current WebyPost product
structure.

------------------------------------------------------------------------

# 11. Suggested Screen Map

``` text
Splash
  ↓
Authentication
  ├── Login
  ├── Register
  └── Forgot Password

Main App
  ├── Home
  │    ├── Feed
  │    ├── Post Details
  │    ├── Comments
  │    ├── Media Viewer
  │    └── User Profile
  │
  ├── Search
  │    ├── Users
  │    ├── Posts
  │    ├── Articles
  │    └── Listings
  │
  ├── Create
  │    ├── Text Post
  │    ├── Image Post
  │    ├── Video Post
  │    ├── Link Post
  │    ├── Poll
  │    ├── Listing
  │    └── Article
  │
  ├── Messages
  │    ├── Conversations
  │    └── Conversation
  │
  ├── Notifications
  │
  └── Profile
       ├── Profile
       ├── Edit Profile
       ├── Posts
       ├── Listings
       └── Settings
```

------------------------------------------------------------------------

# 12. WebyPost Feed

The feed is one of the most important parts of the application.

It should not simply fetch every post and render everything at once.

Recommended:

``` text
Open Feed
   ↓
Load cached/previous data
   ↓
Render immediately
   ↓
Fetch latest API data
   ↓
Update feed
```

Use pagination:

``` text
Initial:
20 posts

Scroll:
next 20

Continue:
next 20
```

Avoid loading hundreds of posts into memory.

------------------------------------------------------------------------

# 13. Feed Performance

Use a high-performance list implementation such as FlashList or the
current recommended React Native list technology after validating the
project dependencies.

Important principles:

-   Virtualized rendering
-   Image resizing
-   Thumbnail usage
-   Lazy media loading
-   Pagination
-   Stable component keys
-   Avoid unnecessary re-renders
-   Memoized post components
-   Separate expensive media components
-   Avoid large JSON payloads

Target a visually smooth scrolling experience.

------------------------------------------------------------------------

# 14. Post Architecture

WebyPost should have a unified post model.

Conceptually:

``` text
Post
├── Text
├── Image
├── Video
├── Link
├── Poll
├── Listing
└── Article
```

Rather than creating completely separate feed systems, create a shared
post container.

Example:

``` text
PostCard
   │
   ├── Header
   ├── Content
   ├── Media
   ├── InteractionBar
   └── Footer
```

Then:

``` text
PostRenderer
   ├── TextContent
   ├── ImageContent
   ├── VideoContent
   ├── LinkContent
   ├── PollContent
   ├── ListingContent
   └── ArticleContent
```

This will make the app easier to maintain.

------------------------------------------------------------------------

# 15. Social Interactions

Implement:

``` text
Like
Comment
Reply
Share
Repost
Follow
Unfollow
Save
View profile
```

Where appropriate, use optimistic UI.

Example:

``` text
User taps Like
       ↓
UI changes immediately
       ↓
API request
       ↓
Success → keep change
Failure → revert + show error
```

This makes the app feel significantly faster.

------------------------------------------------------------------------

# 16. Navigation and Transitions

The objective is not to add animations everywhere.

The objective is continuity.

Recommended transition categories:

### Normal navigation

``` text
Feed
  →
Profile
```

Native horizontal/stack transition.

### Modal

``` text
Create Post
    ↑
Bottom sheet
```

### Comments

``` text
Comments
   ↑
Bottom sheet
```

### Media viewer

``` text
Thumbnail
    ↓
Full-screen media
```

### Post details

``` text
Feed
  ↓
Post Details
```

Animation durations should generally remain subtle, often around:

``` text
150–300 ms
```

Avoid excessive animation.

------------------------------------------------------------------------

# 17. Recommended State Architecture

Use different tools for different state types.

## Server state

Use:

``` text
TanStack Query
```

For:

-   Feed
-   Profiles
-   Posts
-   Comments
-   Notifications
-   Search
-   Messages

## Local application state

Use:

``` text
Zustand
```

For:

-   Current user
-   UI state
-   Draft post
-   App preferences
-   Navigation-related state where needed

Avoid putting everything into one giant global store.

------------------------------------------------------------------------

# 18. API Client

Create one centralized API layer.

Example conceptual structure:

``` text
src/api/
   client.ts
   auth.ts
   feed.ts
   posts.ts
   profiles.ts
   messages.ts
   notifications.ts
   search.ts
```

Instead of having random API calls inside UI components.

Bad:

``` text
Screen → fetch(...)
```

Better:

``` text
Screen
 ↓
Hook
 ↓
API service
 ↓
HTTP client
 ↓
PHP API
```

------------------------------------------------------------------------

# 19. TypeScript Types

Create centralized models:

``` text
src/types/
   User.ts
   Profile.ts
   Post.ts
   Comment.ts
   Message.ts
   Notification.ts
   Listing.ts
   Article.ts
```

Example:

``` typescript
interface Post {
    id: number;
    userId: number;
    type: PostType;
    content?: string;
    createdAt: string;
    likes: number;
    comments: number;
    shares: number;
}
```

The exact schema should mirror the actual API contract.

------------------------------------------------------------------------

# 20. API Response Standard

Standardize responses.

Example:

``` json
{
  "success": true,
  "data": {},
  "message": null,
  "meta": {}
}
```

Errors:

``` json
{
  "success": false,
  "data": null,
  "message": "Unable to load post.",
  "error_code": "POST_NOT_FOUND"
}
```

This makes Claude Code much more effective because the frontend has
predictable contracts.

------------------------------------------------------------------------

# 21. Media Architecture

Images and videos are likely to be one of the larger technical areas.

Recommended pipeline:

``` text
Select media
     ↓
Validate
     ↓
Compress/resize where appropriate
     ↓
Upload
     ↓
PHP API
     ↓
Storage
     ↓
Return media URL
     ↓
Create/update post
```

Do not send unnecessarily large original images to the feed.

Use:

``` text
thumbnail
medium
original
```

where practical.

------------------------------------------------------------------------

# 22. Loading States

Avoid blank screens.

Every major screen should support:

``` text
Loading
Loaded
Empty
Error
Retry
```

Example:

``` text
Feed
├── Loading
├── Posts
├── No Posts
└── Error + Retry
```

Use skeleton components where appropriate.

------------------------------------------------------------------------

# 23. Offline and Cache Strategy

The app should remain responsive even when the network is slow.

At minimum:

``` text
Cached feed
Cached profile
Cached user session
Cached basic app configuration
```

When the user opens the app:

``` text
Cache
 ↓
Immediate UI
 ↓
Network
 ↓
Refresh
```

This is much better than:

``` text
Open app
 ↓
Blank screen
 ↓
Wait
 ↓
API
 ↓
Show screen
```

------------------------------------------------------------------------

# 24. Notifications

Plan for:

``` text
Push notification
       ↓
Android
       ↓
Tap notification
       ↓
Deep link
       ↓
Relevant WebyPost screen
```

Examples:

``` text
Someone liked your post
      ↓
Post Details

Someone commented
      ↓
Post + Comment

New message
      ↓
Conversation

New follower
      ↓
Profile
```

------------------------------------------------------------------------

# 25. Deep Linking

Implement deep links so links can open the correct Android screen.

Conceptually:

``` text
https://webypost.com/post/123
```

could eventually open:

``` text
WebyPost Android
      ↓
Post #123
```

Similarly:

``` text
https://webypost.com/profile/mahesh
```

could open the profile.

This is particularly valuable when WebyPost is shared through social
media.

------------------------------------------------------------------------

# 26. Android Back Button

The Android back button must be explicitly designed.

Examples:

``` text
Conversation
   ↓ back
Messages

Post Details
   ↓ back
Feed

Profile
   ↓ back
Previous screen

Modal
   ↓ back
Close modal
```

Do not rely on accidental default behavior.

------------------------------------------------------------------------

# 27. Security

Important rules:

### Never:

``` text
Android → MySQL
```

### Always:

``` text
Android
 ↓ HTTPS
PHP API
 ↓
MySQL
```

The backend must validate:

-   User permissions
-   Post ownership
-   Profile editing
-   Delete operations
-   Comments
-   Messages
-   Uploads
-   Listing changes

Never trust values coming from the Android client.

------------------------------------------------------------------------

# 28. API Security

Consider:

``` text
HTTPS only
Authentication tokens
Token expiration
Authorization checks
Rate limiting
Input validation
SQL parameterization
Upload validation
File type validation
Maximum file sizes
Server-side permission checks
```

The mobile app should not contain:

-   MySQL credentials
-   Admin credentials
-   Database passwords
-   Private server secrets

------------------------------------------------------------------------

# 29. Project Folder Structure

Recommended:

``` text
webypost-mobile/
│
├── android/
│
├── src/
│   │
│   ├── api/
│   │   ├── client.ts
│   │   ├── auth.ts
│   │   ├── feed.ts
│   │   ├── posts.ts
│   │   ├── profiles.ts
│   │   ├── messages.ts
│   │   ├── notifications.ts
│   │   └── search.ts
│   │
│   ├── components/
│   │   ├── PostCard/
│   │   ├── ProfileHeader/
│   │   ├── MediaViewer/
│   │   ├── CommentItem/
│   │   ├── Loading/
│   │   └── common/
│   │
│   ├── screens/
│   │   ├── auth/
│   │   ├── home/
│   │   ├── search/
│   │   ├── create/
│   │   ├── messages/
│   │   ├── notifications/
│   │   ├── profile/
│   │   └── settings/
│   │
│   ├── navigation/
│   │
│   ├── hooks/
│   │
│   ├── store/
│   │
│   ├── services/
│   │
│   ├── types/
│   │
│   ├── theme/
│   │
│   ├── utils/
│   │
│   └── assets/
│
├── package.json
├── tsconfig.json
├── app.json
├── README.md
├── ARCHITECTURE.md
├── API_MAP.md
└── MOBILE_SCREEN_MAP.md
```

------------------------------------------------------------------------

# 30. Claude Code Development Strategy

Do not use one giant prompt.

Use controlled phases.

## Phase 1 --- Audit

Tell Claude:

``` text
Audit the existing WebyPost PHP/MySQL project.

Do not modify anything.

Map:
- database tables
- existing PHP pages
- authentication
- sessions
- posts
- profiles
- comments
- likes
- followers
- messages
- notifications
- listings
- articles
- uploads
- existing APIs

Produce:
ARCHITECTURE.md
DATABASE_MAP.md
API_MAP.md
MOBILE_SCREEN_MAP.md

Identify risks and legacy dependencies.
```

------------------------------------------------------------------------

# 31. Phase 2 --- API Foundation

Ask Claude to build the API layer.

Requirements:

``` text
/api/v1/

Standard response format
Authentication
Authorization
Validation
Pagination
Error handling
Logging
Security
```

Do not immediately build the mobile app.

First make the API reliable.

------------------------------------------------------------------------

# 32. Phase 3 --- React Native Foundation

Prompt Claude to create:

``` text
React Native
TypeScript
Navigation
Theme
API client
State management
Query management
Reusable components
Error boundary
Loading components
```

Do not implement all screens yet.

------------------------------------------------------------------------

# 33. Phase 4 --- Authentication

Build:

``` text
Login
Register
Forgot password
Session restoration
Logout
Unauthorized handling
```

Test Android authentication before continuing.

------------------------------------------------------------------------

# 34. Phase 5 --- Feed

Build:

``` text
Home
Feed
PostCard
Post details
Comments
Like
Share
Repost
Pagination
Pull-to-refresh
Skeleton loading
Error retry
```

This becomes the first major milestone.

------------------------------------------------------------------------

# 35. Phase 6 --- Profiles

Build:

``` text
Profile
Profile header
Profile posts
Followers
Following
Edit profile
Profile media
```

Use the same API layer.

------------------------------------------------------------------------

# 36. Phase 7 --- Create Post

Implement WebyPost's actual post system.

Potential types:

``` text
Text
Image
Video
Link
Poll
Listing
Article
```

Do not simplify the existing WebyPost concept into a generic "Create
Post" if the backend already supports richer types.

------------------------------------------------------------------------

# 37. Phase 8 --- Search

Implement:

``` text
Search
Users
Posts
Articles
Listings
```

Use debounced search.

Do not send an API request for every single keystroke.

------------------------------------------------------------------------

# 38. Phase 9 --- Messaging

Build:

``` text
Conversation list
Conversation
Messages
Message composer
Unread count
Read state
Attachments where required
```

If WebyPost's current messaging backend is not real-time, first make the
API stable.

Real-time messaging can be introduced as a later improvement using an
appropriate WebSocket/realtime architecture.

------------------------------------------------------------------------

# 39. Phase 10 --- Notifications

Implement:

``` text
Notification list
Unread count
Push notification
Deep linking
Read/unread
```

------------------------------------------------------------------------

# 40. Phase 11 --- Animation and UX

Only after core functionality works should Claude optimize transitions.

Implement:

``` text
Stack transitions
Bottom sheets
Modal transitions
Media transitions
Gesture interactions
Pull-to-refresh
Loading transitions
```

Do not spend the first two weeks making animations while the API is
unstable.

------------------------------------------------------------------------

# 41. Phase 12 --- Performance

Measure:

``` text
Feed FPS
Screen transition time
API response time
Image load time
Memory usage
App startup time
Crash rate
```

Then optimize the actual bottlenecks.

------------------------------------------------------------------------

# 42. Testing Strategy

Test on at least:

``` text
Low-end Android
Mid-range Android
High-end Android
Small screen
Large screen
Slow network
Fast network
No network
Portrait
Rotation where supported
Dark/light settings if implemented
```

Test:

``` text
Login
Logout
Feed
Post
Comment
Like
Share
Profile
Search
Messaging
Notifications
Media
Back navigation
Deep links
```

------------------------------------------------------------------------

# 43. Build Milestones

Use these milestones.

## Milestone 1

``` text
App opens
Authentication works
Navigation works
```

## Milestone 2

``` text
Feed works
Posts display
Profiles work
```

## Milestone 3

``` text
Create post
Like
Comment
Share
Repost
```

## Milestone 4

``` text
Search
Notifications
Messaging
```

## Milestone 5

``` text
Media
Caching
Transitions
Deep links
```

## Milestone 6

``` text
Performance
Security
Testing
Release build
```

------------------------------------------------------------------------

# 44. Suggested 8-Week Roadmap

## Week 1

``` text
Existing system audit
API architecture
Database mapping
Mobile screen mapping
React Native setup
Design system
```

## Week 2

``` text
Authentication
Navigation
API client
User state
Home shell
Feed foundation
```

## Week 3

``` text
Feed
Post cards
Post details
Comments
Likes
Reposts
Shares
```

## Week 4

``` text
Profiles
Profile editing
Search
Create post
Image/video
Poll
```

## Week 5

``` text
Listings
Articles
Notifications
Messaging
```

## Week 6

``` text
Caching
Optimistic UI
Transitions
Gestures
Deep links
Push notifications
```

## Week 7

``` text
Performance
Android device testing
API edge cases
Upload testing
Security testing
```

## Week 8

``` text
Bug fixing
UI polish
Release build
Play Store assets
Production configuration
Final QA
```

------------------------------------------------------------------------

# 45. What Claude Code Should NOT Do

Do not allow Claude to:

``` text
Rewrite the entire backend without analysis.

Change the MySQL schema unnecessarily.

Create duplicate user tables.

Create a second authentication system without understanding the existing one.

Hard-code API URLs throughout components.

Put API requests directly into UI components.

Store database credentials in the Android project.

Create a giant global state store.

Replace WebyPost branding with generic social-media UI.

Implement every feature simultaneously.

Delete working legacy PHP code simply because it looks old.
```

------------------------------------------------------------------------

# 46. Most Important Principle

The project should have three clearly separated layers:

``` text
┌─────────────────────────────┐
│        MOBILE UI            │
│     React Native + TS       │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│       API / SERVICES        │
│        PHP + REST           │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│        DATA LAYER            │
│           MySQL              │
└─────────────────────────────┘
```

This separation will make future development significantly easier.

------------------------------------------------------------------------

# 47. Future Architecture

Once Android is stable, WebyPost can evolve toward:

``` text
                    WEBYPOST
                       │
               Central API Layer
                       │
       ┌───────────────┼───────────────┐
       │               │               │
      Web           Android           iOS
       │               │               │
       └───────────────┼───────────────┘
                       │
                    MySQL
```

You could then add:

``` text
AI features
Push notifications
Realtime messaging
Recommendation engine
Advanced search
Content moderation
Analytics
Premium features
Payments
```

without redesigning the entire frontend architecture.

------------------------------------------------------------------------

# 48. Final Recommendation

For WebyPost, I would choose:

``` text
Frontend:
React Native + TypeScript

Navigation:
React Navigation

Animation:
React Native Reanimated

Gestures:
React Native Gesture Handler

Server state:
TanStack Query

Local state:
Zustand

Networking:
Axios

Large feeds:
FlashList

Backend:
Existing PHP

API:
Versioned REST API

Database:
Existing MySQL

Authentication:
Secure API-based authentication

Media:
Existing backend/storage, optimized for mobile

Development:
Claude Code

Target:
Android first
```

### Target timeline

**6--8 weeks**

### MVP

**3--4 weeks**

### Production-quality app

**6--8 weeks**

### Heavy polish / complex realtime features

**8--12+ weeks**

------------------------------------------------------------------------

# 49. The Most Important Development Rule

Build the application in this order:

``` text
EXISTING WEBYPOST
       ↓
AUDIT
       ↓
API CONTRACT
       ↓
AUTHENTICATION
       ↓
REACT NATIVE FOUNDATION
       ↓
FEED
       ↓
POSTS
       ↓
PROFILES
       ↓
SOCIAL INTERACTIONS
       ↓
CREATE POST
       ↓
SEARCH
       ↓
MESSAGES
       ↓
NOTIFICATIONS
       ↓
MEDIA
       ↓
CACHE
       ↓
TRANSITIONS
       ↓
PERFORMANCE
       ↓
SECURITY
       ↓
QA
       ↓
ANDROID RELEASE
```

The key strategy is:

> **Reuse WebyPost's existing backend and database, preserve the
> existing WebyPost visual identity, and rebuild only the mobile
> presentation layer as a native-feeling React Native application.**

That gives you the best balance between development speed, reuse of your
existing investment, maintainability and the smooth social-network
experience you want.
