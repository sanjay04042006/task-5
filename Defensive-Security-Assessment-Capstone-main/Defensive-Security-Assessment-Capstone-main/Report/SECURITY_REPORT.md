Defensive Security Assessment Report

Target Application: Local Task Management Web Application (http://localhost:3000)

Assessment Date: October 31, 2026

Assessor: Student Security Practitioner

Status: Completed & Remediated

1. Scope, Authorization Statement, and Rules of Engagement

Authorization Statement

"I, the system owner and sole developer of this local application, hereby authorize this security assessment. I grant full permission to conduct configuration reviews, dependency checks, input validation testing, and authentication checks against the designated local target environment (http://localhost:3000). No third-party services or unauthorized systems are included in this assessment."

Scope

In-Scope:

Local application source code (app.js, index.html)

Dependency configuration (package.json)

Local authentication handling and routing

Input fields and output rendering mechanisms

Out-of-Scope:

Production servers or external hosting providers

Third-party APIs not controlled by the application repository

Rules of Engagement

Testing must be restricted to the local development environment (localhost).

No automated denial-of-service (DoS) or destructive flooding attacks may be executed.

All findings must be documented using sanitized, dummy data (no real credentials, personal identifiable information, or production secrets).

2. Methodology & Testing Results

We evaluated the application across five core defensive categories:

Configuration: Checked for debug flags, hardcoded secrets, and secure headers.

Authentication: Examined session management, password hashing, and login bypass risks.

Validation: Tested user-supplied input fields for Cross-Site Scripting (XSS) and injection vulnerabilities.

Dependencies: Audited project packages using dependency analysis tools (npm audit).

Logging: Verified whether security-relevant events (failed logins, privilege escalations) are logged securely without exposing sensitive payloads.

3. Findings & Remediation

Finding 1: Reflected Cross-Site Scripting (XSS) in Search / Task Display

Severity: High

Category: Validation / Input Sanitization

Description: User input submitted via the task creation or search parameter was rendered directly into the DOM using innerHTML without prior sanitization, allowing arbitrary script execution.

Reproduction Steps:

Navigate to http://localhost:3000/tasks.

Create a new task with the title: <script>alert('XSS_TEST')</script>.

Observe that the script executes in the browser context upon loading the task list.

Remediation & Before/After Evidence:

Before: taskItem.innerHTML = ${task.title};

After: Replaced with safe DOM node creation and text assignment (textContent), or applied a sanitization utility:

const textNode = document.createElement('span');
textNode.textContent = task.title; // Safely escapes HTML entities
taskItem.appendChild(textNode);


Retest Result: Retesting with <script>alert('XSS_TEST')</script> now renders as literal text on the page (<script>alert('XSS_TEST')</script>) without triggering any script execution.

Finding 2: Outdated and Vulnerable Dependencies

Severity: Moderate

Category: Dependencies

Description: An audit of package.json revealed an outdated version of a package with known prototype pollution vulnerabilities.

Reproduction Steps:

Run npm audit inside the project root folder.

Observe high-severity vulnerability warnings associated with transitive dependencies.

Remediation & Before/After Evidence:

Before: Dependency pinned to vulnerable version or missing lockfile updates.

After: Updated packages and locked clean dependencies using npm audit fix.

Retest Result: Running npm audit returned 0 vulnerabilities.

Finding 3: Hardcoded API Secret in Configuration File

Severity: High

Category: Configuration

Description: A test API secret key was found hardcoded directly in app.js.

Remediation:

Moved the secret key to a local .env file (added to .gitignore) and loaded it securely via process.env.API_SECRET.

Retest Result: Repository scanner and manual inspection confirm no credentials or secrets exist in the source code.

4. Conclusion

All high-risk findings identified during the defensive security assessment have been successfully remediated and verified through retesting. The application code is now prepared for secure deployment.
