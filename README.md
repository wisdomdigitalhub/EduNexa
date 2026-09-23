# EduNexa — Phase 2: School Sign-Up + Students, Teachers, Classes, Subjects

Smart School Management, Simplified.

## What changed this phase

**Open school sign-up.** Any school can now register itself — no more single
hardcoded admin email. The **"Register a school"** link on the login screen creates
a new school and its School Admin account together, in one step. The old fallback
"Finish setting up your school" screen still exists, but only as a recovery path if
a signup is interrupted partway (e.g. network drops right after the Auth account is
created but before the Firestore docs are written).

**Classes** — School Admin can add/edit/deactivate/reactivate classes (level, arm,
optional session label).

**Subjects** — add/edit/deactivate/reactivate subjects (code, name, applicable
levels via checkboxes).

**Teachers** — School Admin can add a teacher, which **creates a real sign-in
account for them** with a temporary password shown once on screen (share it with
them; they can change it via "Forgot password?"). List, search by name,
deactivate/reactivate.

**Students** — same pattern: add creates a real sign-in account, plus admission
number, class assignment, guardian contact. List, search by name or admission
number, deactivate/reactivate.

**Dashboard** — the School Admin dashboard now shows real counts (Students,
Teachers, Classes, Subjects) instead of placeholder zeros.

### The tricky part: creating other people's accounts without logging yourself out

Firebase's client SDK signs you in as whoever you just created — so naively calling
"create account" for a teacher would kick the Admin out of their own session. This
build works around that with a **second, temporary Firebase app instance**: the new
teacher/student account is created under that throwaway instance, which is then torn
down immediately, leaving the Admin's own session completely untouched. No Cloud
Function or backend needed — it's pure client-side JavaScript
(`createManagedAuthUser` in `index.html`).

### Files
```
index.html        (everything — HTML, CSS, JS)
firestore.rules
README.md
```

## Firestore rules — what's new

- `schools` and `users` creation is now open to **any first-time signed-in user**
  (no existing profile doc), not just one hardcoded email — but it's still a
  **self-closing door**: once your `users/{uid}` doc exists, this path can never
  fire again for your account, and it only ever lets you create a doc for
  *yourself*. It can't be used to grant a role on someone else's school or to
  escalate an existing account.
- New collections `classes`, `subjects`, `teachers`, `students` are now live —
  read/write scoped to `schoolId`, writes restricted to the School Admin of that
  school. `teachers`/`students` are keyed by the person's own Auth `uid`, so they
  can also read (not write) their own detail doc.
- Fixed a latent cross-school leak in the rules pattern itself: `list` rules now
  check `resource.data.schoolId` against the caller's own school, so a School Admin
  can never list another school's users/teachers/students even with a broad,
  unfiltered query — not just relying on the app's own `where` clause.
- Everything else (`fees`, `payments`, `announcements`, etc.) is still locked
  (`allow read, write: if false`) until its phase ships.

**MANUAL STEP REQUIRED**
Firebase Console → Firestore Database → **Rules** → replace the existing rules with
the contents of `firestore.rules` → **Publish**. No other Console changes needed —
Authentication and Firestore are already set up from Phase 1.

## How to test

1. **Sign up a new school** — log out if needed, click "Register a school," fill in
   school name / your name / email / password → you land straight on the School
   Admin dashboard.
2. **Add a class** — Classes → "+ Add class" → fill in level/arm → Save. It appears
   in the table immediately.
3. **Add a subject** — Subjects → "+ Add subject" → check a few applicable levels →
   Save.
4. **Add a teacher** — Teachers → "+ Add teacher" → fill in name/email → "Create
   teacher account." A yellow box shows the email + temporary password — note it
   down. Confirm you (the Admin) are still logged in throughout.
5. **Log out, log in as that teacher** using the temp password — confirm they land
   on the Teacher dashboard (not the Admin one), and that logging back in as
   yourself still works.
6. **Add a student** the same way, assigning them to the class you created — confirm
   the class shows up correctly in the students table.
7. **Deactivate** a teacher or student → confirm the badge flips to "Inactive." Try
   logging in as that deactivated account → should be rejected with "This account
   has been deactivated."
8. Try visiting **Classes/Subjects/Teachers/Students while logged in as a second,
   separate school** (sign up a second school in a private/incognito tab) → confirm
   you only ever see that school's own data, never the first school's.

## Known limitations (still ahead)

- No **edit** for teachers/students yet (only add + deactivate/reactivate) — full
  edit forms with all the fields from the master spec (photo, address, emergency
  contact, etc.) come with a later pass.
- No **class/subject assignment** UI for teachers yet (which teacher teaches which
  subject in which class) — that's `teacherAssignments`, still locked.
- Academic Sessions are just a free-text field on classes for now, not their own
  managed collection with "one active session per school" — that's Phase 3.
- Attendance, Results, Fees, Announcements, Reports, Audit logs, Super Admin
  school management — all still placeholders, per the original phase order.
- Temporary passwords are shown once on screen with no email/SMS delivery yet
  (Phase 6 Communication) — the Admin has to relay it manually for now.

---

Let me know when you're ready to keep going — next up would naturally be teacher↔
class↔subject assignments and/or Academic Sessions, or I can jump wherever you'd
rather focus.
