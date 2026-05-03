What?:
    A web application to test the android application.

Why?: 
    1. To reduce manual effort.
    2. To avoid manual misses.
    3. To increase efficiency

How?:
    Develop a portal:
    1. Which loads an .apk file 
    2. Test the screen flows and report in either case if flow is broken or working as exptected.
    3. Test the functionalities on screens and report in either case if functionlity is broken or working as exptected.
    4. Document the audit for admin approval before test report generation.

What to use:
    Node, Python, PyTest, Docker

Responses:
First, my clarifying questions:

About the Android testing scope:

Will the app being tested require a real Android device, an emulator (like AVD), or both? This affects whether we can use Docker or need device farm integration. - Throw some light on this. I've been testing currently the app without building .apk using Expo on my device, I want you to answer how can an application load .apk file using upload button and then what would be needed to run the .apk without showing user what is being tested and then report. 
Are screen flows pre-recorded (like UI automator dumps), or should the tool intelligently navigate by itself? - Intelligent tool by triggering the action.
Example After loading the .apk wbe start test is trigerred.
A. tools to open the .apk in requird env.
B(1). if screen is login/create user. since no creds available, go to create user. if screen appears then log green signal else red signal with error, if green then fill dummy details (but remember tye creds) and submit. Success then try to login with same creds. then on home screen , trigger action and keep the audit updated. Ask queries if any
What does "test functionalities" mean specifically — form inputs, API responses visible on screen, or deeper logic? - Suggested above, ask if any question
About the APK loading:
4. Is the APK uploaded by a user via the web portal, or pulled from a CI pipeline/artifact store? - By user
5. Should multiple APK versions be compared (regression testing), or just pass/fail per run? - Phase 2.

About reporting and admin approval:
6. Who are the admins — QA leads, PMs, or developers? What format do they expect (PDF, web dashboard, email)? - Super admins
7. Is there a specific approval workflow needed (e.g., approve → trigger release), or is audit documentation enough? - Good to have, but we can skip in Milestone 1

About tech constraints:
8. Is this running on a local machine, on-prem server, or cloud (AWS/GCP/Azure)? - local machine as of now.
9. Any existing CI/CD system I should integrate with (GitHub Actions, Jenkins, Bitbucket Pipelines)? - Github only as of now
10. Do you have a timeline or MVP scope in mind? -  1 week?