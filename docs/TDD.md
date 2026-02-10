# Technical Design Document: Flux (Serverless Edition)

**Version:** 2.0  
**Architecture Pattern:** Thick Client / BaaS (Backend-as-a-Service)  
**Stack:** React (Vite) + Firebase (Firestore/Auth)  
**Date:** October 26, 2023

## 1. Executive Summary

Flux is a context-aware fitness web application (PWA). This iteration prioritizes rapid development and low operational overhead by leveraging **Google Firebase**. The complex business logic (Readiness Engine, Scheduling) resides in the **React Frontend**, while data integrity and security are enforced via **Firestore Security Rules**.

---

## 2. Modified Hytel Monorepo Structure

| Directory         | Status         | Role in Flux (Serverless)                                                                                                                                            |
| :---------------- | :------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `apps/web`        | **Primary**    | Contains **ALL** application logic: The Readiness Algorithm, Calendar API calls, and Firestore hooks.                                                                |
| `apps/functions`  | **Deprecated** | We will **remove** the tRPC backend. No custom Node.js server will be deployed.                                                                                      |
| `packages/shared` | **Active**     | **Zod Schemas.** These remain critical. We use them to validate data _before_ writing to Firestore to ensure our NoSQL database doesn't become a "schema-less mess." |
| `packages/ui`     | **Active**     | Shared UI components (Shadcn UI).                                                                                                                                    |

---

## 3. Architecture Overview

### 3.1 The "Thick Client" Model

In this architecture, the user's browser performs the heavy lifting.

1.  **Authentication:** User logs in via **Firebase Auth** (Google/Email).
2.  **Logic:** The "Readiness Engine" runs locally in a React `useEffect` or Custom Hook. It fetches raw data (yesterday's sleep, today's calendar), calculates the "Flux Score," and updates the UI state.
3.  **Persistence:** The calculated score and workout logs are saved directly to **Firestore**.
4.  **Security:** Firestore Security Rules intercept every read/write to ensure users can only access their own data.

---

## 4. Data Model (Firestore NoSQL)

We optimize for **Read Performance**. We will use a root-level `users` collection and sub-collections for high-volume data (logs).

### 4.1 Collection Structure

#### **`users` (Collection)**

- **Doc ID:** `uid` (from Firebase Auth)
- **Fields:**
  - `email`: string
  - `onboarding`: map
    - `fitnessLevel`: string ("beginner", "intermediate")
    - `equipment`: array ["dumbbells", "bands"]
  - `settings`: map
    - `batterySensitivity`: number (0.5 - 1.5)
    - `integrations`: map (tokens/status for Calendar/Wearables)

#### **`users/{uid}/daily_logs` (Sub-collection)**

- _Why a sub-collection?_ To prevent the User document from hitting the 1MB limit over time.
- **Doc ID:** `YYYY-MM-DD` (e.g., `2023-10-27`)
- **Fields:**
  - `date`: timestamp
  - `inputs`: map
    - `sleepHours`: number
    - `stressLevel`: number (1-10)
    - `calendarEvents`: number (count)
  - `outputs`: map
    - `fluxScore`: number (0-100)
    - `recommendedIntensity`: string
  - `workoutCompleted`: boolean

#### **`users/{uid}/workouts` (Sub-collection)**

- **Doc ID:** `uuid`
- **Fields:**
  - `name`: string
  - `exercises`: array of maps (Denormalized - we copy the exercise data here so history doesn't change if we edit the global library later)
    - `name`: "Bench Press"
    - `sets`: 3
    - `reps`: 10
    - `weight`: 135

#### **`exercises` (Root Collection - Read Only)**

- **Doc ID:** `uuid`
- **Fields:**
  - `name`: string
  - `category`: string
  - `videoUrl`: string
  - `difficulty`: string

---

## 5. Security & Validation

Since we have no backend code to validate inputs, we rely on **Firestore Rules** and **Client-Side Zod Validation**.

### 5.1 Firestore Security Rules (The "Firewall")

These rules replace the backend authorization logic.

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Helper function to check auth
    function isAuthenticated() {
      return request.auth != null;
    }

    // Helper to check ownership
    function isOwner(userId) {
      return request.auth.uid == userId;
    }

    // USER DATA: Users can only read/write their own document
    match /users/{userId} {
      allow read, write: if isAuthenticated() && isOwner(userId);

      // SUB-COLLECTIONS: Same rules inherit down
      match /daily_logs/{logId} {
        allow read, write: if isAuthenticated() && isOwner(userId);
      }
      match /workouts/{workoutId} {
        allow read, write: if isAuthenticated() && isOwner(userId);
      }
    }

    // EXERCISES: Public Read, Admin Write (managed via Firebase Console)
    match /exercises/{exerciseId} {
      allow read: if isAuthenticated();
      allow write: if false; // strictly manual updates by admin
    }
  }
}

5.2 Client-Side Validation (Zod)
We will strictly define data shapes in packages/shared. The frontend must pass validation before attempting a Firestore write.

// packages/shared/src/schemas/dailyLog.ts
import { z } from 'zod';

export const DailyLogSchema = z.object({
  date: z.date(),
  inputs: z.object({
    sleepHours: z.number().min(0).max(24),
    stressLevel: z.number().min(1).max(10),
  }),
  outputs: z.object({
    fluxScore: z.number().min(0).max(100),
  })
});

6. Logic Implementation (The "Frontend Brain")
6.1 The Readiness Engine Hook
Instead of a backend service, we create a reusable React Hook.

// apps/web/src/hooks/useReadinessEngine.ts
import { useFirestoreDocument } from './useFirestore'; // custom wrapper

export function useReadinessEngine(date: Date) {
  // 1. Fetch Inputs
  const { data: userProfile } = useAuth();
  const { data: calendar } = useCalendarApi(); // Client-side Google API
  const { data: health } = useHealthKit(); // Native bridge or manual input

  // 2. The Algorithm (Pure JS)
  const calculateScore = () => {
    if (!userProfile || !health) return 50; // default

    let score = 100;
    // Sleep Penalty
    if (health.sleep < 7) score -= (7 - health.sleep) * 10;
    // Stress Penalty (Calendar)
    if (calendar.busyMinutes > 240) score -= 20;

    return Math.max(0, score); // Clamp to 0
  };

  return {
    fluxScore: calculateScore(),
    isLoading: !userProfile
  };
}

7. Integrations (Client-Side)
7.1 Google Calendar
We will use the Google Identity Services SDK for JavaScript (Client-side OAuth 2.0).

Flow: User clicks "Connect Calendar" -> Popup asks for permission -> We get an Access Token -> We query the Calendar API directly from the browser -> We discard the token (or store short-term in memory). We do not store long-term refresh tokens in Firestore as that is a security risk without a backend to proxy them.

7.2 Wearables (The Trade-off)
Since we have no backend to hold "Client Secrets" for APIs like Whoop or Oura:

Option A (Recommended): Manual Input. A simple slider for "How did you sleep?" (Low tech, high reliability).

Option B (Mobile Only): If the app is wrapped (Capacitor/Ionic), we can read Apple Health / Google Fit directly from the device.

Option C (Proxy): If strict API integration is needed later, we can add a single Firebase Extension (e.g., "Trigger Email" or a custom proxy) without writing full backend code. For now, we assume Option A/B.

8. Development Roadmap (Agile)
Sprint 1: Foundation
Initialize Firebase Project.

Deploy apps/web to Vercel/Netlify.

Setup Firebase Auth (Google Sign-in).

Implement User creation in Firestore upon first login.

Sprint 2: The Core Loop
Build the "Manual Input" form (Sleep/Stress sliders).

Write the useReadinessEngine hook.

Create the "Battery" UI Component using Shadcn.

Sprint 3: The Workout
Populate exercises collection manually.

Build the Workout Player UI.

Implement "Save Workout" (Write to users/{uid}/workouts).

Sprint 4: Polish
Add Offline Persistence (Firestore enables this by default with one line of config).

Refine Firestore Security Rules.
```
