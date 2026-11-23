# IBM Tarapur / CRCW Roster — setup and free hosting

This is a mobile-friendly Firebase web app for the nine confirmed login email addresses. It supports employee-only monthly roster viewing, Excel/CSV roster import, C-Off/leave applications, and administrator approval/rejection with live status updates.

## Confirmed accounts and roles

| Email | Display name | Website role |
|---|---|---|
| rabindra@tatasteel.com | Rabindra Murmu | Administrator |
| mithilesh@tatastee.com | Mithilesh Prasad | Administrator |
| sandeep@tatasteel.com | Sandeep Giri | Employee |
| ranjan@tatasteel.com | Ranjan Nike | Employee |
| dinesh@tatasteel.com | Dinesh (full name not provided) | Employee |
| sameer@tatasteel.com | Sameer (full name not provided) | Employee |
| javed@tatasteel.com | Javed Shaikh | Employee |
| pratik@tatastee.com | Pratik Phatak | Employee |
| shoeb@tatasteel.com | Shoeb Khan | Employee |

The two `@tatastee.com` addresses above are included exactly as confirmed. Rabindra and Mithilesh are the only administrators. The other seven accounts are employees. Dinesh's and Sameer's full names were not supplied, so the site labels them by first name until you provide their full names.

## Password setup requested

Create the nine accounts in Firebase Authentication → Users → Add user. Assign the common employee password you supplied to the seven employee accounts, and assign the separate administrator password you supplied to Rabindra and Mithilesh. These passwords are deliberately **not embedded in this website source, ZIP, or seed file**. Firebase Authentication stores credentials securely; do not put passwords in JavaScript, HTML, Firestore profile documents, or an Excel roster. Since the passwords have been shared in chat, treat them as temporary and change them before operational use.

I cannot sign in to or create accounts inside your Firebase project from this generated package. A project owner must perform the Firebase Console setup below. The website is prepared, but it is not live until you deploy it.

## 1. Enable Firebase services

1. Open https://console.firebase.google.com/ and select project `ibm-tarapur-crcw-roster`.
2. In Authentication → Sign-in method, enable Email/Password.
3. In Firestore Database, create a database (production mode is fine; rules are provided in `firestore.rules`).
4. Verify Project settings → Your apps matches the Firebase configuration in `app.js`.

## 2. Create the nine Authentication users

In Authentication → Users → Add user, create each email in the table above and assign the passwords according to the password setup section. Firebase Authentication UIDs are generated automatically. Do not use passwords as profile fields.

## 3. Create Firestore user profiles

For each Authentication user, copy the UID from Authentication → Users. In Firestore create a collection named `users`; create a document whose document ID is that exact UID. Add two string fields:

- `name`: display name from the table above
- `role`: `admin` for Rabindra and Mithilesh; `employee` for everyone else

Example Rabindra document:

```json
{ "name": "Rabindra Murmu", "role": "admin" }
```

Example employee document:

```json
{ "name": "Sandeep Giri", "role": "employee" }
```

Only administrators can import rosters or review requests. Employees can read only roster rows and requests matching their signed-in email.

## 4. Publish Firestore security rules

In Firestore → Rules, paste the contents of `firestore.rules` and click Publish. Do not weaken these rules to make the app appear to work. The app expects roster queries filtered by the signed-in employee email.

## 5. Deploy free using Firebase Hosting

1. Install Node.js LTS from https://nodejs.org/.
2. Install Firebase CLI: `npm install -g firebase-tools`
3. Open a terminal in this project folder and run `firebase login`.
4. Run `firebase use --add` and select `ibm-tarapur-crcw-roster`.
5. Run `firebase deploy --only hosting,firestore:rules`.
6. Firebase prints the live `https://ibm-tarapur-crcw-roster.web.app` URL after successful deployment. Open it on a phone and use the browser's Add to Home screen option.

Firebase Hosting has a no-cost Spark plan allowance, subject to current usage limits and Firebase terms. If you do not have access to the Firebase project, its owner must add you as a project member before deployment.

## 6. Excel roster format

Use the in-app Download Excel template button. Required headers (case-insensitive; spaces are ignored):

- `Employee Email`
- `Employee Name`
- `Date` — `YYYY-MM-DD`
- `Shift` — e.g. General, Morning, Evening, Night, Off

Optional: `Start Time`, `End Time`, `Location`, `Remarks`. The importer reads the first worksheet and imports up to 400 rows per batch. Re-importing the same employee/date updates that roster row. Use only the nine confirmed email addresses.

## 7. Employee requests and approvals

Employees sign in → My requests → choose C-Off or Leave → choose date range → add reason → Submit. Both administrators can see pending applications in Admin panel and Approve or Reject them with an optional note. The employee's My requests panel updates live with the decision. Approval does not automatically modify the roster; an administrator must update/re-import the roster if the shift assignment changes.

## 8. Security and testing

- Firebase web config is a public client config, not an admin credential. Restrict the API key to your website domain after deployment.
- Test with dummy accounts and dummy roster rows before operational use.
- Firestore may prompt you to create a composite index for roster or request queries; use the index-creation link shown in the browser console.
- Use password reset and unique passwords after initial setup. Never share admin credentials with employees.
