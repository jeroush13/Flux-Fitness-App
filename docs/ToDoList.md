Sprint 0: Project Initialization & Cleanup
Goal: Get the "Hytel" monorepo running and stripped of backend dependencies.

[ ] Repo Setup

[ ] Clone the Hytel boilerplate.

[ ] Run pnpm install.

[ ] Verify pnpm dev runs locally.

[ ] Architecture Cleanup

[ ] Delete apps/functions directory (We are going Serverless/Thick Client).

[ ] Remove trpc related code from apps/web (trpc provider, hooks).

[ ] Update turbo.json to remove the backend build pipeline.

[ ] Firebase Configuration

[ ] Create a new project in Firebase Console.

[ ] Enable Authentication (Google & Email/Password providers).

[ ] Enable Firestore Database (Start in Test Mode).

[ ] Copy Firebase Config keys (apiKey, authDomain, etc.) to .env.local in apps/web.

[ ] Shared Packages Setup

[ ] Install firebase SDK in apps/web.

[ ] Install zod in packages/shared.

Sprint 1: Auth & User Onboarding
Goal: Users can sign in and define their baseline profile.

Backend / Schema (packages/shared)

[ ] Define UserOnboardingSchema (Zod): Validate age, fitness level, equipment access.

[ ] Define UserSettingsSchema (Zod): Validate battery sensitivity settings.

Frontend (apps/web)

[ ] Create lib/firebase.ts: Initialize Firebase App.

[ ] Create AuthProvider context: Handle Login/Logout state.

[ ] Build Login Page:

[ ] "Sign in with Google" button.

[ ] Error handling toast.

[ ] Build Onboarding Wizard (Multi-step form):

[ ] Step 1: Basic Info (Name, Age).

[ ] Step 2: "Life Load" (Job stress, Kids, Commute).

[ ] Step 3: Equipment Check (Dumbbells, Gym, Bodyweight).

[ ] Implement useCreateUser hook:

[ ] Checks if users/{uid} exists.

[ ] If not, writes initial profile data to Firestore.

Sprint 2: The "Readiness Engine" (Core Feature)
Goal: Calculate the "Flux Score" based on inputs.

Schema (packages/shared)

[ ] Define DailyLogSchema: Validate sleep hours, stress level, and calculated score.

Frontend Logic (apps/web)

[ ] Build Manual Input Component:

[ ] Slider for Sleep (0-12 hours).

[ ] Slider for Stress (1-10).

[ ] Toggle for "Soreness/Injury".

[ ] Implement Google Calendar Integration:

[ ] Setup Google Identity Services SDK.

[ ] Create useCalendarEvents hook to fetch "busy" minutes for the current day.

[ ] Create useReadinessEngine hook:

[ ] Input: Sleep data, Stress data, Calendar busy time.

[ ] Logic: Reduce "Battery" (100%) based on penalties defined in TDD.

[ ] Output: fluxScore (0-100).

UI Implementation

[ ] Build <BatteryGauge /> component (using SVG or Shadcn Progress).

[ ] Green (High Energy), Yellow (Moderate), Red (Low).

[ ] Build Dashboard Layout:

[ ] Display Date.

[ ] Show Battery Gauge.

[ ] Show "Recommended Intensity" text based on score.

Sprint 3: Workouts & Execution
Goal: Display and track workouts based on the Readiness Score.

Data Prep

[ ] Create exercises.json seed file (List of ~20 basic exercises).

[ ] Write a script (or manual Firestore entry) to populate the exercises collection.

Frontend (apps/web)

[ ] Build <WorkoutCard /> component.

[ ] Implement Workout Recommendation Logic:

[ ] If Score > 70: Suggest HIIT/Heavy Lifting.

[ ] If Score < 40: Suggest Yoga/Mobility.

[ ] Build "Time Crunch" Feature:

[ ] Input: "I have X minutes."

[ ] Logic: Filter selected workout to fit time (drop isolation exercises, keep compounds).

[ ] Build Workout Player:

[ ] List of exercises with checkboxes.

[ ] Integrated Rest Timer.

[ ] "Finish Workout" button.

[ ] Implement saveWorkout function:

[ ] Write to users/{uid}/workouts sub-collection.

[ ] Update users/{uid}/daily_logs to mark workoutCompleted: true.

Sprint 4: Security, Persistence & Polish
Goal: Make it production-ready.

Security (Firestore Console)

[ ] Implement Firestore Security Rules:

[ ] Copy rules from TDD (User can only read/write own data).

[ ] Lock down exercises collection to read-only.

Resilience

[ ] Enable Offline Persistence in lib/firebase.ts.

[ ] Add "Offline Mode" indicator in UI.

Testing

[ ] Write Unit Tests (Vitest) for the useReadinessEngine logic (ensure math is correct).

[ ] Test Zod schemas with invalid data.

Deployment

[ ] Configure Vercel/Netlify build settings.

[ ] Set Environment Variables in deployment.

[ ] Deploy Production Build.

Future / Icebox (Post-MVP)
[ ] Integrate Vital/Terra API for automatic wearable syncing.

[ ] Add "Haptic Feedback" support for mobile browsers.

[ ] Add "History" view to see past workout logs.
