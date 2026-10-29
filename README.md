# FestPass — Jenkins assignment

**Aryan Gupta · Class `001` · Challenge `00284`**

A completed Java and Jenkins CI assignment demonstrating a real automatic failing build, diagnosis of a cancellation defect, and an automatic passing build. All 28 supplied tests and the build/evidence helpers are unchanged.

## Submission files

- [Download the complete submission ZIP](FestPass-Submission-Aryan-Gupta.zip)
- [Evidence and explanation](submission/EVIDENCE.md)
- [Automatic red evidence CSV — build 2](submission/build-2-failure/build/submission-evidence.csv)
- [Automatic green evidence CSV — build 3](submission/build-3-success/build/submission-evidence.csv)
- [Final runnable JAR downloaded from Jenkins](submission/build-3-success/build/festpass.jar)
- [JAR help output](submission/jar-help.txt) and [booking/cancellation demonstration](submission/jar-demo.txt)
- [Jenkins job configuration](submission/job-config.xml)
- [Original project guide](docs/PROJECT-GUIDE.md)

The final source is in this repository. Its Git history preserves the setup, personalization, and repair commits. `submission/FestPass-History.bundle` also preserves the exact three-commit history assessed by Jenkins.

## Recorded Jenkins builds

| Build | Trigger | Commit | Result | Tests | Failures |
|---|---|---|---|---|---|
| 1 | Manual bootstrap | `a08efd0` | FAILURE | 28 | 3 |
| 2 | SCM change | `7cfed4f` | FAILURE | 28 | 3 |
| 3 | SCM change | `4cdd10d` | SUCCESS | 28 | 0 |

Build 2 followed the personalized help-message commit. Build 3 followed the repair commit; both were triggered by actual Jenkins SCM polling. The failing builds skipped JAR creation, JAR smoke testing, and product publication. The passing build completed all seven real-JAR smoke checks. Each build folder includes downloaded evidence, console output, build metadata, and a test report.

## Diagnosis

`BookingService.cancel()` validated that a booking existed but did not remove the student from the roster. Consequently, cancellation did not free a seat and the unchanged booking was saved again. The repair adds `event.students.remove(studentId);` after the existing validation. No tests or pipeline checks were disabled.

## Run locally

Use a full JDK 21 with `java`, `javac`, and `jar` on PATH:

```text
java ci/Build.java verify
java ci/SmokeTest.java
java -jar build/festpass.jar help
```

Expected: **28 tests, 0 failures**, seven passing smoke checks, and a help introduction containing **Aryan Gupta**.

Assessment job: `festpass-ci-email-corrected`. All assessment commits use `aryangup17@gmail.com` directly as author and committer.

The Jenkins controller, agent, and Git server used for assessment run on the student's laptop. `127.0.0.1` links in the evidence refer to that local installation; the downloaded reports and artifacts here can be reviewed without access to it. Jenkins configuration secrets and runtime directories are excluded.
