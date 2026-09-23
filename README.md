# EduNexa — Phase 8 consolidated build (NO Storage rules)

This build completes the remaining school-management modules in the supplied application source while keeping the user's explicit **no Firebase Storage rules** requirement.

## Included
- Firebase Authentication + Firestore school isolation
- Super Admin platform school management, payment review, platform settings, audit access
- Owner/Principal/Vice Principal/Registrar/Teacher/Printer/Parent role navigation
- Students: create, list, search-ready data, edit, deactivate/reactivate, CSV import
- Teachers and staff assignment workflow
- Classes, subjects and academic sessions
- Teacher-to-class-to-subject assignments
- Attendance: Present / Absent / Late
- Result entry: CA + Exam, draft/submitted flow, review/approval, grading configuration
- Report preview/print and report verification collection support
- Fees and payments with pending/confirmed workflow
- Announcements and notifications
- School branding/settings without Storage
- Subscription payment submission/review
- Audit logs
- Firestore indexes
- Responsive UI inherited from the supplied source

## Important
1. Replace the deployed `index.html` with this build.
2. Deploy `firestore.rules` in Firestore Rules.
3. Deploy `firestore.indexes.json` if Firebase asks for indexes.
4. **Do not create or deploy a storage.rules file.** This package intentionally does not contain one.
5. Super Admin must be provisioned securely in Firebase/Firestore. Do not let an ordinary client create a Super Admin profile.
6. Test with at least two schools before production: verify cross-school reads/writes are denied.

## Authentication / account creation
The supplied source already contains its school registration and invitation/account flows. The Firestore rules allow school administrators to create role profile documents for staff/student roles in their own school while preventing role/school changes during ordinary profile edits.

## Recommended production hardening
For privileged Auth-user creation, use a trusted Admin SDK/Cloud Function in production. A browser-only app cannot safely hold Firebase Admin credentials. The client can manage normal Firestore records, but platform-level user provisioning should remain server-authorized.
