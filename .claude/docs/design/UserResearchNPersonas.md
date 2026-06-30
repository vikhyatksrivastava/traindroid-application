-----------------------QA Persona------------------------

Persona: QA Engineer
User is a solo developer who also does QA. Every time he pushes a build, he manually scans the QR/reploads the ap on Expo go on his device to test the android app. He needs to manually visit each scree to confirm screen is visible. He needs to mnaually test each functionlaity for it operations. He needs to manuly observe that functionlaity is as per business requirement. It takes many hours/iterations and he often misses things because it's repetitive. He wants to upload an APK and get a report without touching his phone.

Questions and answers (QA persona):

1. What do you do before traindroid exists? (upload build, open app, tap through screens manually...)
Ans: I had to use expo build. After resolving the dependency issues of expo, i run expo start command from the project path from where expo must be executed. After execution and preparing the environment. I scan the QR on my cell device from Expo Go App. I might need to resolve other dependcy issues and version challenges before able to see the initial page of app.
2. What's the most annoying part?
Ans: Setting up expo Go, reload the app every time major change is amde to app.
3. What would "perfect" look like? 
Ans: App screen flow without any break, should report performance issue, ui elements which are not working. broken flows.
4. How technical are you? (comfortable with terminals, CI, etc.)
And: comfirtable with terminals, design, develop and test the programs and projects.
5. How often do you do this task?
And: Started recently.

Expected Journey of Persona (QA):
1. User opens the traindroid portal on browser.
2. User logs in to the portal.
3. Portal identifies the QA using their user id created by master admin.
4. User is presented with a dashboard/home page.
5. Portal's dashboard/home page
    1. welcomes the user, displays there last login time, there team/group details if any.
    2. displays the list apps they/their team worked on with the status, date of testing, remarks etc.
    3. option to start fresh testing.
6. For fresh testing, requests to upload the .apk file.
7. Test .apk file for the screen flows, functionalities, generate proper report for the issues, flow break any found with evidences.
8. Later scope to integrate with incident/Bug management tools to raise tickets for each BUG.
9. User to review the issues with evidences.
10. User to start workflow for approval if all is well.

What could go wrong wrt persona (QA):
