Continuous Integration is a practice, which requires discipline, particularly when the development environment contains a significant amount of complexity.  Best practices are methods or techniques that consistently bring superior results. Best practices evolve over time as new technology and techniques are introduced or improvements discovered.

Here we introduce some best practices for continuous integration that could be especially helpful for mobility teams based in Android.

- A code repository. All artifacts required to build the project should be placed in a unique repository.

- Automate the build and deployment. A single command should have the capability of building the project. 

<br/> With *Gradle* we can build by executing: 
<br/> $ ./gradlew build
<br/> or if we want to build a flavor:
<br/> $ ./gradlew assemble/<flavor/>


<br/> Within the build the following steps should be included:
<br/>	⋅⋅* Unittests.
<br/>	⋅⋅* Sonar or any tool for continuous inspection of code quality.
<br/>	⋅⋅* Functional tests.
<br/>	For these steps, it can do with:
<br/>	⋅⋅* *Bamboo*: every step can be configured as a task or job in Bamboo. In case any step fails, the builds should fail and automatically notify the team.
<br/>   ⋅⋅* *Jenkins*: every step can be configured as a stage within the pipeline of Jenkins. In case any step fails, the builds should fail and automatically notify the team.

- Enable Fast Builds (Typically <10 minutes). Builds should be fast. Anything beyond 10 minutes becomes a dysfunction in the process, because people won’t commit as frequently. Large builds can be broken into multiple jobs and executed in parallel. All tests should run automatically to validate that the software behaves as expected but in case that functional tests like Calabash, Appium, etc, take long time to run, then we can have:
<br/> ⋅⋅* One automated build process that runs only a basic set of tests.
<br/> ⋅⋅* Another automated process that runs all tests (It can be run every night).
<br/> ⋅⋅* Trigger additional tests manually.

- Commit Frequently (at least once a day). Developers should commit frequently – at least once per day – several times a day is recommended.  By doing so, developers will know the real time state and health of the software and will reduce the number of conflicting changes.

- Everyone can see the latest build. It should be easy to find out whether the build breaks and, if so, who made the relevant change, so the whole team should be able to easily see these results especially when the build breaks. Developers should be notified as soon as possible as to why it broke, so that they can fix it as quickly as possible. Every build should be posted in Bamboo or Jenkins and also commits since last build, changes, tests results, etc. 
