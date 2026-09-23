EduNexa — Phase 1: Foundation
Smart School Management, Simplified.
What was built
Everything in a single self-contained index.html — HTML, CSS, and JS (Firebase init + app logic) in one file, per the "simple one-file architecture" preference.
Email/password auth: login, logout, forgot/reset password, session persistence.
First-run bootstrap flow: when wisdomebubedaniel2009@gmail.com logs in with no Firestore profile yet, it's shown a one-time "Initialize your school" screen. Submitting it creates schools/{schoolId} and users/{uid} (role schoolAdmin) in one go.
Role-based routing and sidebar for all four roles (Super Admin, School Admin, Teacher, Student) — each gets a dashboard shell with real (empty-state) stat cards. Every other nav item shows an honest "not built yet" placeholder rather than a fake working screen.
Toasts, a confirm dialog, loading skeleton, and empty/error states.
Responsive layout (mobile sidebar drawer, fluid grid).
Firestore Security Rules enforcing school isolation and role checks, with a narrow, self-closing bootstrap exception for the one initial admin email.
No Firebase Storage. Photos and payment receipts (Phase 2/5) will use a different approach — most likely direct image URLs or a non-Firebase upload target — decided when we get to those phases.
Files
index.html        (everything — HTML, CSS, JS)
firestore.rules
README.md
Where your Firebase config is used
Inside index.html's <script type="module"> block — the firebaseConfig object you supplied is passed straight to initializeApp(). Nothing else touches it directly.
Manual Firebase Console steps (do these before testing)
MANUAL STEP REQUIRED
Authentication → Sign-in method → enable Email/Password.
Authentication → Users → confirm wisdomebubedaniel2009@gmail.com is listed. If it has no password set, click the account → reset/set a password there. (EduNexa never sees or sets your password.)
Firestore Database → click Create database → start in production mode → pick a region.
Firestore Database → Rules → paste the contents of firestore.rules → Publish.
No composite indexes are needed yet — Phase 1 only does single-field reads/writes on schools and users.
Storage is not enabled and not needed for this build.
How to run it
A single static file — no build step. Options:
Open index.html directly in a browser, or
Serve the folder with any static server (npx serve .), or
Deploy to Firebase Hosting later (firebase deploy --only hosting) once you're ready.
Testing the existing Admin login
Open the app → you'll land on the login screen.
Log in with wisdomebubedaniel2009@gmail.com and its password.
Because no users/{uid} document exists yet, you'll see "Initialize your school" instead of the dashboard.
Enter your school name and your full name → Create school & continue.
You're now in the School Admin dashboard. Log out and back in — the bootstrap screen is gone for good; you go straight to the dashboard from now on.
How the Admin is connected to the first school
The bootstrap form writes two documents in one client action:
schools/{schoolId} — the school record (name, contact fields blank for now, active: true).
users/{yourUid} — your profile, with role: "schoolAdmin" and schoolId pointing at the school doc just created.
schoolId is derived from your Auth UID (school_<first12charsofuid>), so the pairing is deterministic and idempotent — no server-side ID generation needed for a one-time bootstrap.
Collections created automatically vs. manual
Collection
Created by
Manual step needed?
schools/{schoolId}
App, during bootstrap
No
users/{uid}
App, during bootstrap (you) and later by School Admin (teachers/students)
No
Nothing in Phase 1 requires you to hand-create a Firestore document. Everything else in the full data model (students, teachers, classes, etc.) is deliberately locked by the rules (allow read, write: if false) until its phase implements it.
Testing the Security Rules
In the Firebase Console → Firestore → Rules → Rules playground:
Simulate create on users/<your-uid> as an unauthenticated user → should be denied.
Simulate create on users/some-other-uid authenticated as a different email → should be denied (bootstrap only works for the exact initial admin email, on their own uid).
Once your profile exists, simulate update on your own users/{uid} doc changing role to "superAdmin" → should be denied (self role-escalation is blocked).
Simulate get on schools/{someOtherSchoolId} as your School Admin account → should be denied (cross-school reads are blocked).
Known limitations (Phase 1 scope)
Only the bootstrap path creates a School Admin. Creating teacher/student Auth accounts from the client is a Phase 2 problem (the Firebase client SDK signs in as whatever user it creates, which isn't acceptable for an Admin creating other people's accounts — Phase 2 will need either a Cloud Function or an Admin-SDK-backed endpoint for this).
No Firebase Storage — student/teacher photos and payment receipts will need a different storage approach, to be decided in Phase 2/5.
Email verification is not yet enforced (no open self-registration exists yet to verify).
All non-dashboard nav items are placeholders — Students, Teachers, Classes, Subjects, Fees, Announcements, Settings, Attendance, Results all arrive in later phases.
No pagination/query logic yet — nothing to paginate until Phase 2 adds collections with data.
Stop here and test the above before Phase 2 (Students / Teachers / Classes / Subjects), per the build plan.
