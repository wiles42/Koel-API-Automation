# Koel API Automation Testing

API test automation project for the [Koel](https://koel.dev/) music streaming application, built with **Postman**, **JavaScript assertions**, **Newman**, and **Jenkins**. The collection tests song information, play-count interactions, and recently played songs, including positive, negative, and authorization scenarios.

## Tech Stack

- **Postman** — API requests, environments, and automated assertions
- **JavaScript** — response validation and collection variables
- **Newman** — command-line execution of the Postman collection
- **Jenkins** — CI pipeline that checks out the repository and executes Newman
- **Node.js / npm** — Newman installation and runtime
- **JUnit XML** — test result reporting in Jenkins
- **Git / GitHub** — source control and project hosting

## Test Coverage

| Area | Examples of scenarios |
| --- | --- |
| Authentication | Obtain authorization and validate the returned token |
| Song information | Retrieve song metadata, invalid song IDs, missing IDs, unsupported HTTP methods, missing/invalid tokens |
| Play count | Verify increments across plays, response fields, missing/nonexistent song IDs, unsupported methods, missing/invalid tokens |
| Recently played songs | Verify list structure and IDs, counts of 0, 1, 5, and 100, invalid counts, unsupported methods, missing/invalid tokens |

The tests use dynamic values where appropriate, including saving a played song ID and comparing the recently played list against a previously recorded count rather than hard-coding the total.

## Repository Structure

```text
Koel-API-Automation/
├── collections/
│   └── Koel-API.postman_collection.json
├── environments/
│   └── QA.postman_environment.json
├── Jenkinsfile
└── README.md
```

## Run the Tests Locally

**Prerequisites:** Node.js/npm and Newman, plus any valid credentials or configuration required by the test environment.

Install Newman:

```bash
npm install -g newman
```

Run the collection:

```bash
newman run collections/Koel-API.postman_collection.json \
  -e environments/QA.postman_environment.json \
  --env-var "baseUrl=https://qa.koel.app" \
  --env-var "baseURL=https://qa.koel.app" \
  --ignore-redirects \
  -r cli,junit \
  --reporter-junit-export newman-results.xml
```

The `--ignore-redirects` option preserves redirect status codes for assertions that validate unexpected redirects. Avoid committing real tokens, passwords, or other secrets to the repository.

## Jenkins CI Pipeline

The repository includes a declarative `Jenkinsfile` that:

1. Checks out the repository from GitHub.
2. Uses a Node.js installation configured in Jenkins as `NodeJS`.
3. Installs Newman using npm.
4. Runs the Postman collection against the QA environment.
5. Publishes the generated JUnit XML results in Jenkins, even when assertions fail.

To configure a Jenkins Pipeline job, choose **Pipeline script from SCM**, select **Git**, use this repository's HTTPS URL, set the branch to `*/main`, and set the script path to `Jenkinsfile`. The Jenkins **NodeJS** plugin and a Node.js tool installation named `NodeJS` are required for the current pipeline.

## Latest Observed Execution

The Jenkins console log from **October 13, 2026** reported:

| Metric | Result |
| --- | ---: |
| Requests executed | 39 |
| Request execution failures | 0 |
| Assertions | 99 |
| Assertions passed | 83 |
| Assertions failed | 16 |
| Collection runtime | 3.9 seconds |

**CI status:** The build completed the Newman execution and recorded JUnit results, but Jenkins marked the build **FAILURE** because Newman returned a nonzero exit code for failing assertions. This indicates that the automation pipeline ran; it does **not** mean all API behavior passed validation.

Examples of observed mismatches that require investigation against the API contract:

- Requests without authorization returned `200 OK` in several scenarios where the tests expected `401 Unauthorized`.
- Requests with invalid tokens returned `302 Found` rather than the expected `401 Unauthorized`.
- A play request with a nonexistent song ID returned `500 Internal Server Error` rather than the expected `404 Not Found`.
- Some invalid-input requests returned redirects or HTML responses instead of the expected validation errors.

These are **potential defects or specification mismatches**, not confirmed product bugs until expected behavior is verified against requirements.

## Project Focus

This project demonstrates API functional testing, negative and boundary testing, authorization checks, dynamic assertions, CI integration, and test-result reporting. It also documents failures transparently instead of masking them to force a green build.
