# Bug Tracking

## Bug #1: Frontend CI Pipeline Test Failure

### Description
The frontend CI pipeline failed during the testing stage because the project did not have a configured test script.

### Environment
- CI/CD: GitHub Actions
- Runtime: Node.js v20.20.2
- Package Manager: npm 10.8.2

### Error Message
npm error Missing script: "test"
Error: Process completed with exit code 1.

### Impact
The CI workflow failed and prevented successful validation of the frontend changes.

### Root Cause
The GitHub Actions workflow attempted to execute `npm test`, but the frontend `package.json` file did not contain a test script.

### Resolution
The CI workflow was reviewed and updated to match the available project scripts. Testing commands were aligned with the project's actual configuration.

### Additional Notes
The pipeline also reported non-blocking warnings:
- Node.js version compatibility warning.
- Deprecated npm packages.
- Dependency vulnerabilities requiring future maintenance.



## Bug #2: Backend CI Test Failure Due to File Name Casing Conflict

### Description
The backend CI pipeline failed during Jest test execution because TypeScript detected a file naming conflict caused by inconsistent capitalization in the middleware file name.

### Environment
- CI/CD: GitHub Actions
- Runtime: Node.js
- Testing Framework: Jest + TypeScript
### Error Message
TS1261: Already included file name differs from file name only in casing.

### Impact
The backend test suite could not complete successfully, preventing the CI pipeline from passing.

### Root Cause
The middleware file was referenced using different casing:
- `authMiddleware.ts`
- `AuthMiddleware.ts`

Although the imports worked in some local environments, the CI environment detected the inconsistency due to case-sensitive file system handling.

### Resolution
The file name and all import statements were standardized to use the same casing across the backend project.

### Verification
After fixing the naming inconsistency, the Jest test suite was executed again to verify that the issue was resolved.



---

## Bug #3: Frontend Component Test Failures

### Description
Frontend automated tests failed in two React components during CI execution.

### Failed Tests

#### EnrollmentCard Component
Error:
Unable to find an element with the text:
هل أنت متأكد؟

### Cause
The test expected a confirmation dialog text that was not rendered in the component output.

---

#### ContinueCard Component
Error:
Unable to find an element with the text:
40%


### Cause
The component rendered the progress percentage through accessibility attributes instead of visible text, causing the test query to fail.

### Impact
The CI frontend test pipeline failed even though the application functionality was not affected.

### Resolution
The test cases and component rendering behavior were reviewed and adjusted to match the expected UI behavior.

### Status
Resolved
