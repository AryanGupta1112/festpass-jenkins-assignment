# FestPass Jenkins assignment — Aryan Gupta

Class: `001` · Challenge: `00284` · Completed: 29 October 2026

## Configuration and build trace

Job: [festpass-ci-email-corrected](http://127.0.0.1:8081/job/festpass-ci-email-corrected/). SCM: `git://127.0.0.1:9418/festpass-java`; branch: `*/main`; script: `Jenkinsfile`. The controller has zero executors. The separate `student-laptop` agent has one executor and label `student-laptop`. JDK 21 runs both tests and builds. The committed `pollSCM('* * * * *')` trigger checks for changes every minute. Only build 1 was started manually.

| Build | Cause | Commit | Result | Tests / failures |
|---|---|---|---|---|
| [1](http://127.0.0.1:8081/job/festpass-ci-email-corrected/1/) | Started by user admin | `a08efd0c88c24bdd889d5158d95db4b92c7de5c0` | FAILURE | 28 / 3 |
| [2](http://127.0.0.1:8081/job/festpass-ci-email-corrected/2/) | Started by an SCM change | `7cfed4fc57fa24aad80cfb2f85e0e13a38289ab8` | FAILURE | 28 / 3 |
| [3](http://127.0.0.1:8081/job/festpass-ci-email-corrected/3/) | Started by an SCM change | `4cdd10d5dd95028dc985a2d3c4c1016a7a07a821` | SUCCESS | 28 / 0 |

Build 2 followed the help-message commit. Its automatic failure finished before the repair was committed. Build 3 followed the repair. Each build folder contains its downloaded `build/submission-evidence.csv`, console log, Jenkins build metadata, and test report. Polling logs independently show the changed commits.

## Diagnosis and repair

Business rule: cancelling an existing booking must remove that student from the event roster, free one seat, and persist the updated roster. Cancelling a nonexistent booking must still fail.

The failing `cancellationFreesSeat` test books S1 into a one-seat event, cancels S1, then books S2. Expected: S2 is the only registered student. Actual: booking S2 throws `SOLD OUT. Even the developer needs a ticket.` Two more tests report `Cancellation was lost` and `List does not reflect persisted cancellation`.

`BookingService.cancel()` checked that the booking existed but never removed it. The minimal repair adds this line after the existing validation:

```java
event.students.remove(studentId);
```

The supplied 28 tests, build helpers, evidence code, and pipeline stages are unchanged. The Jenkinsfile change only adds the polling trigger. The help introduction now includes Aryan Gupta. Local `java ci/Build.java verify` and `java ci/SmokeTest.java` pass; Jenkins independently repeats these checks.

## Products and executable check

Both failed builds skipped **Build JAR**, **Smoke test JAR**, and **Save artifact**. They archived only the diagnostic CSV and published no JAR (`JarSha256=NOT_PUBLISHED`). Build 3 archived the JAR, build information, seven-command smoke result, and evidence CSV. The downloaded JAR's SHA-256 matches its successful evidence record.

Running the downloaded JAR with `help` prints:

```text
FestPass - campus event registration by Aryan Gupta
```

The downloaded JAR also completed a real booking/cancellation walkthrough: S1 fills a one-seat event; S2 is rejected with exit 2; cancelling S1 permits S2 to book. The final output is `Events: 1 | Booked: 1 | Available: 0`. Full executable output is saved in `jar-help.txt` and `jar-demo.txt`.

## Submission contents

- `FestPass-Source.zip`: final source and assignment files.
- `FestPass-History.bundle`: all three commits; clone with `git clone FestPass-History.bundle festpass-java`.
- `build-2-failure/build/submission-evidence.csv`: automatic red evidence.
- `build-3-success/build/submission-evidence.csv`: automatic green evidence.
- `build-3-success/build/festpass.jar`: the final artifact downloaded from Jenkins.
- `job-config.xml`, polling logs, per-build console/test reports, and this evidence page.

The class code was confirmed as `001` because the supplied validator rejects `1`. The challenge retains its leading zeros. All evidence uses the same installation ID, test-source hash, and pipeline hash. All assessment commits use Aryan Gupta and aryangup17@gmail.com directly as both author and committer; no email mapping is needed. Jenkins home, credentials, agent secrets, and runtime directories are excluded from this submission.
