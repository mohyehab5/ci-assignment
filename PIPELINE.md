# Day 2 CI Pipeline Design

## 1. Workflow Events

- Push to main, excluding changes limited to Markdown files
  and files under docs/.
- Pull requests targeting main.
- Manual workflow dispatch with Python version and package
  creation inputs.
- Scheduled execution every Monday at 07:00 UTC.

## 2. Jobs and Dependencies

- test: Run pytest and upload the JUnit report.
- package: Build and upload a versioned application archive.
- verify: Download and test the packaged application.
- report: Summarize upstream job results even on failure.
- pr-comment: Comment on pull requests after successful packaging.

Job graph:

test -> package -> verify
test + package -> report (always)
package -> pr-comment (pull requests only)

The package job waits for successful tests.
The verify job waits for successful packaging.

## 3. Pipeline Artifact

The main artifact is a versioned .tar.gz archive containing
temperature.py and README.md.

A second artifact named test-report contains the JUnit XML report.

## 4. Pipeline Classification

This is Continuous Integration because it tests, packages,
and verifies changes without deploying the application.

## 5. Packaging on Test Failure

The package job must be skipped when tests fail because
a failing application should not produce a trusted package.
