EduNexa — School Management Platform
EduNexa is a Firebase-backed school-management SaaS/PWA for the supplied edunexa-70b24 Firebase project.
Current build
EduNexa v11 — Phase 1–8 implementation
Included
Firebase Email/Password authentication with a mobile-safe startup path.
Firebase CDN fallback (gstatic → jsDelivr).
Super Admin platform dashboard, schools, school details, school-admin accounts and platform settings.
Secure school isolation and role-based Firestore rules.
School registration and school-code workflow.
School Admin / Owner dashboard.
Full student profiles and real Firebase student accounts.
Full teacher profiles and real Firebase teacher/registrar accounts.
Classes, subjects and deterministic teacher assignments.
Student-to-class assignments.
Academic sessions with one active session per school and First/Second/Third terms.
Attendance: Present / Absent / Late, by date/class/session/term, with statistics.
Configurable assessment components, maximums, weightings and grade boundaries.
Teacher result entry: Draft → Submitted.
Principal/School Admin result workflow: Submitted → Approved/Rejected → Published.
Student result visibility only after publication/locking.
Report-card preview, average, optional position/ranking and browser printing.
Fees configured by class/term and confirmed-payment balances.
Student payment submission with date, amount, method, reference, note and optional compressed receipt image stored in Firestore.
Payment review: Confirm / Reject.
Announcements with school/teacher/student/class audience fields and notifications.
Notification center with read state.
School profile and branding settings.
Academic/result/settings controls.
Audit logs for important administrative, academic and payment actions.
CSV preview/import for students and teachers.
Responsive PWA install experience for Android/desktop browsers.
No storage.rules file and no Firebase Storage dependency in this build.
Exact Firebase project
The browser app uses the Firebase configuration supplied for:
Project ID: edunexa-70b24
Auth domain: edunexa-70b24.firebaseapp.com
Storage bucket value in the Firebase config: edunexa-70b24.firebasestorage.app
App ID: 1:845157830674:web:b8a83619da9249a38fc8cb
The old Report X Firebase project is not used.
Firebase setup
Firebase Console → Authentication → Sign-in method → enable Email/Password.
Create/enable the Cloud Firestore database.
Deploy firestore.rules to this exact edunexa-70b24 project.
Deploy firestore.indexes.json.
Do not add a Storage rules file for this build.
Secure first Super Admin bootstrap
Do not let an arbitrary authenticated browser user create a Super Admin.
Create the first platform admin from a trusted Firebase Admin environment / Firebase Console Firestore data view:
Collection: platformAdmins
Document ID: <Firebase Auth UID of the intended Super Admin>
Fields:
  level: "super"
The Firebase Auth account itself must already exist. The Firestore rules intentionally do not allow a normal authenticated user to create their own platformAdmins document.
GitHub Pages deployment
Upload these files to the GitHub Pages repository root:
index.html
manifest.json
sw.js
firestore.rules
firestore.indexes.json
README.md
icons/icon-192.png
icons/icon-512.png
Then:
GitHub → Settings → Pages.
Select the branch/folder containing the files.
Wait for the HTTPS Pages URL to publish.
Open the HTTPS GitHub Pages URL, not file:// or content://.
Open the site once online after every new deployment so the versioned service worker can refresh the HTML shell.
PWA
EduNexa is an installable PWA, not an APK.
On Android Chrome/Edge, use Install app / Add to Home screen. On supported desktop browsers, use the browser install icon.
Firestore collections used
schools
users
schoolUsers
schoolInvitations
platformAdmins
platformSettings
students
teachers
classes
subjects
teacherAssignments
studentClassAssignments
academicSessions
attendance
assessments
results
reportCards
fees
payments
announcements
notifications
auditLogs
settings
parentLinks
reportVerification
The app does not require pre-creating empty collections. Firestore creates collections when the first document is written.
Important data/security notes
Firestore rules are the actual security boundary; UI role checks are only convenience.
School documents are isolated through schoolId and active school membership.
Users cannot change their own Firestore role or school through the normal users document update path.
School-admin membership updates cannot move a member to another school or change their membership role.
Teacher attendance/result writes are tied to deterministic teacher assignment IDs.
Student result reads are restricted to published/locked results.
Soft deactivation uses active:false / status:"inactive" instead of deleting operational records.
Payment balances use only status:"confirmed" payments.
Receipt images are compressed client-side and stored as data URLs in Firestore; very large images are rejected to stay within Firestore document limits.
If the browser still shows “EduNexa could not start”
The page now exposes the actual Firebase/module startup error rather than hiding it behind an indefinite spinner. The most common deployment causes are:
GitHub Pages is serving an old index.html from an old service-worker cache.
Firebase Authentication Email/Password is disabled.
The browser/network cannot reach the Firebase JavaScript CDN.
The deployed rules/indexes belong to a different Firebase project.
The current build uses a versioned service-worker cache (edunexa-shell-v11) and network-first navigation so a new GitHub Pages index.html can replace an old cached copy.
