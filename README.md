# Budget Tracker

An offline-first, event-sourced budget tracking application designed for multi-device synchronization. Built with Flutter for mobile (using Drift for local storage), Next.js for the web dashboard, and Firebase (Firestore + Cloud Functions) for real-time sync across devices. All date aggregations (dayKey/monthKey) are computed using the user's home timezone (IANA string) to ensure consistent reporting regardless of device location.

## Repository Structure

- **mobile/** - Flutter mobile app with offline-first event sourcing and Drift database
- **dashboard/** - Next.js web dashboard for desktop budget management and visualization
- **functions/** - Firebase Cloud Functions for server-side event processing and sync
- **shared/** - Shared TypeScript types and interfaces used across projects

## Local Development

Start Firebase emulators:
```bash
firebase emulators:start
```

Run Flutter mobile app:
```bash
cd mobile
flutter run
```

Run Next.js dashboard:
```bash
cd dashboard
npm run dev
```
