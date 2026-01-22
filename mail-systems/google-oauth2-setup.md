**<h1>🛠️ Resolution: Implementing Google OAuth 2.0 for WordPress SMTP</h1>** </br>
<p>📋 Project Context
Website: smnumericconsult.com

Objective: Replace standard PHP mail() with the secure Gmail API to ensure 100% email deliverability for student registrations.

Stack: WordPress 6.9, WP Mail SMTP 4.7.1, PHP 8.0.30.
</p>

**<h3>🛑 The Challenge: Setup Roadblocks</h3>** </br>
During the initial connection between the WordPress server and the Google Cloud Project, two primary "Gatekeeper" errors were encountered that blocked the email "handshake."

<h3>Error 1: The "Testing" Barrier</h3> </br>
<p>Symptom: User redirected to a Google 404/403 page.

Error Message: "Access blocked: This app is in testing. It can only be accessed by the developer."

The Cause: Google Cloud projects default to a restricted "Testing" state where only explicitly invited users can authenticate.</p>

<h3>Error 2: The "Service Disabled" Failure</h3> </br>
<p>Symptom: Authentication completes, but the test email fails immediately.

Error Code: 403 PERMISSION_DENIED

Debug Log Snippet: ```json { "code": 403, "message": "Gmail API has not been used in project 424693953633 before or it is disabled.", "status": "PERMISSION_DENIED" }

The Cause: The Google Cloud Project was created, but the specific Gmail API service was not "turned on" within the project library.
</p>

**<h2>✅ Step-by-Step Implementation Guide</h2>**
<h3>Step 1: Google Cloud Project Initialisation</h3> </br>
<p>Navigate to the Google Cloud Console.

Create a New Project (e.g., SM Numeric Mailer).

CRITICAL: Go to APIs & Services > Library. Search for "Gmail API" and click ENABLE.

Note: Failure to do this results in the 403 Permission Denied error.
</p>

<h3>Step 2: Configure the OAuth Consent Screen</h3> </br>
<p>Go to APIs & Services > OAuth Consent Screen.

Select User Type: External.

Fill in the mandatory fields (App Name, Support Email).

Bypassing the Testing Block: * Under Test Users, add your Gmail address. This allows you to authenticate while the app is still in development.

Pro Tip: Click "Publish App" to move it to Production. This prevents your authorisation token from expiring every 7 days.</p>

<h3>Step 3: Generating Credentials</h3> </br>
<p>Go to APIs & Services > Credentials.

Click + Create Credentials > OAuth client ID.

Application Type: Web application.

Authorised Redirect URIs: Paste the URI provided by the WP Mail SMTP plugin (usually https://connect.wpmailsmtp.com/google/).

Warning: A character mismatch here will trigger a 400 Redirect URI error.

Copy your Client ID and Client Secret.
</p>

<h3>**Step 4: WordPress Integration**</h3> </br>
<p>In WordPress, navigate to WP Mail SMTP > Settings.

Select Google / Gmail mailer.

Input the Client ID and Client Secret.

Click Save Settings.

Click Allow plugin to send emails using your Google account.

Complete the Google login. If warned "This app is unverified," click Advanced > Go to wpmailsmtp.com (unsafe).
</p>

<h3>**🧪 Verification**</h3> </br>
<p>Navigate to WP Mail SMTP > Tools > Email Test.

Send a test email to the admin account.

Expected Result: "Success! Test email was sent successfully."</p>

**🧠 Lessons Learned** </br>
Project vs. Service: Creating a project does not mean the API is active. Always check the API Library.

State Management: An app in "Testing" mode is volatile. Move to "Production" for long-term stability.

Propagation Time: Google Cloud can take up to 5 minutes to recognize an "Enabled" API. Do not re-authenticate immediately after a fix; wait for the sync.
