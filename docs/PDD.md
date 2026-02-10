Project Name: Flux (The Context-Aware Fitness App)

Product Vision: To create a fitness application that dynamically adapts workout plans based on a user's real-time physiological data (wearables) and logistical constraints (calendar), prioritizing energy management and longevity over rigid adherence.

Epic 1: Onboarding & "Context" Integration
Focus: Establishing the user's baseline, connecting external data sources, and setting the "Battery" parameters.

User Stories:

US-1.1: User Profile Creation

As a new user,

I want to input my basic biometrics (age, weight, gender) and fitness experience level,

So that the app calculates my baseline caloric needs and safe workout intensities.

US-1.2: Wearable API Connection

As a user with a wearable (Whoop, Apple Health, Oura),

I want to authorize Flux to read my Sleep, HRV, and Resting Heart Rate data,

So that the app can automatically determine my daily readiness score.

US-1.3: Calendar Sync

As a busy professional,

I want to sync my Google/Outlook calendar with Flux,

So that the app knows exactly when I am free to exercise without me manually inputting it.

US-1.4: "Battery" Baseline Setup

As a user,

I want to complete a short "Life Load" questionnaire (job stress, commute time, injury history),

So that the "Battery" algorithm has context for my mental drain beyond just physical metrics.

US-1.5: Goal Definition

As a user,

I want to select a primary goal (Strength, Stress Management, or Endurance),

So that the workout recommendations prioritize the right type of movement.

Epic 2: The "Readiness Engine" (The Core Logic)
Focus: The algorithm that decides "What workout do I do today?" based on inputs.

User Stories:

US-2.1: Daily "Flux" Score Generation

As a user,

I want to see a single "Readiness Score" (0-100) on my dashboard every morning,

So that I immediately understand how hard I should push myself today.

US-2.2: Automated Workout Selection

As a user,

I want to be presented with a "Recommended Workout" that matches my Readiness Score (e.g., High Score = HIIT; Low Score = Yoga),

So that I don't have to think or choose what to do.

US-2.3: Calendar Conflict Resolution

As a user with a packed day,

I want the app to analyze my free blocks and suggest a workout duration that fits (e.g., "You have 30 mins at 7 AM"),

So that I don't schedule a 60-minute workout I can't finish.

US-2.4: Real-Time Adjustment (The "Bad Day" Switch)

As a user who feels worse than my data suggests,

I want to manually toggle a "Low Energy" button,

So that the planned high-intensity workout is instantly swapped for a recovery session.

Epic 3: Workout Execution & Micro-Dosing
Focus: The interface and mechanics of actually performing the workout.

User Stories:

US-3.1: The "Time Crunch" Feature

As a user running late,

I want to input my exact available time (e.g., "18 minutes"),

So that the current workout is stripped down to the most essential sets to fit that window.

US-3.2: Haptic Feedback Cues (Silent Mode)

As a gym-goer,

I want my watch/phone to vibrate when a rest timer ends,

So that I don't have to stare at my screen between every set.

US-3.3: Video Guidance Overlay

As a beginner,

I want to see a looping GIF/Video of the exercise I am performing,

So that I can ensure my form is correct without pausing the timer.

US-3.4: RPE (Rate of Perceived Exertion) Logging

As a user finishing a set,

I want to quickly rate how hard the set was (1-10),

So that the AI learns if the weights are too heavy or too light for next time.

Epic 4: The "Battery" Dashboard & Recovery
Focus: Visualization of energy balance and gamifying recovery.

User Stories:

US-4.1: Battery Visualization

As a user,

I want to see a "Body Battery" icon that drains based on my activity/stress and recharges based on recovery actions,

So that I have a visual goal to not hit "0%" by the end of the day.

US-4.2: Recovery Activity Logging

As a user,

I want to log "Recharge" activities (e.g., Nap, Meditation, Healthy Meal),

So that I can see my battery percentage go up in real-time.

US-4.3: End-of-Day Report

As a user,

I want to receive a notification at 8 PM summarizing my energy balance,

So that I know if I need to prioritize sleep or if I have energy for evening activities.

Epic 5: Technical Infrastructure & Admin
Focus: Backend stability, data security, and content management.

User Stories:

US-5.1: Exercise Library CMS

As an admin/content manager,

I want to easily upload new exercises, videos, and tags (e.g., "Legs," "Low Impact"),

So that the AI has a diverse database to pull workouts from.

US-5.2: Data Privacy Compliance

As a user,

I want my health data to be encrypted and GDPR/CCPA compliant,

So that I feel safe sharing sensitive biometric data.

US-5.3: Offline Mode

As a user in a basement gym with no signal,

I want to be able to start and complete a workout that was already downloaded,

So that connectivity issues don't stop my training.
