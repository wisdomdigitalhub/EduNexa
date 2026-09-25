EduNexa — School Management Platform
EduNexa is the school-management SaaS/PWA build for the supplied EduNexa Firebase project.
Firebase project
Project ID: edunexa-70b24
Auth domain: edunexa-70b24.firebaseapp.com
Storage bucket: edunexa-70b24.firebasestorage.app
Included
EduNexa Platform Management
Multi-school management
School Admin management
Student management
Teacher management
Classes, subjects and teacher assignments
Existing academic, results, fees, payments, announcements, reports and audit modules from the supplied source
PWA manifest + service worker + install experience
Firestore security rules
Firestore indexes
Installable app
Deploy the files over HTTPS (GitHub Pages, Firebase Hosting, or another HTTPS host). On supported browsers, use the EduNexa Install App action or the browser's install/Add to Home Screen option.
This package is a PWA, not an Android APK. An APK requires a separate Android wrapper/build step.
Firebase setup
Firebase Console → Authentication → enable Email/Password.
Create/enable Firestore Database.
Deploy firestore.rules to the edunexa-70b24 project.
Deploy firestore.indexes.json if Firebase requests the indexes.
Storage is referenced by the app where receipt/photo features are used; enable Firebase Storage if those uploads are required.
Keep the secure first-admin bootstrap controlled; do not make arbitrary authenticated users Super Admins.
No storage.rules file is included, as requested.
