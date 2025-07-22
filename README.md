# Vulnerable Test Application

This is a deliberately vulnerable Node.js application designed for testing container vulnerability scanning tools like Trivy.

## Vulnerabilities Included

### Application Dependencies
- **Express 4.16.4** - Contains known security vulnerabilities
- **Lodash 4.17.4** - Vulnerable to prototype pollution and template injection
- **Moment 2.19.3** - Contains regular expression DoS vulnerabilities
- **Axios 0.18.0** - Has known security issues
- **JSONWebToken 8.3.0** - Vulnerable to various JWT-related attacks
- **Mongoose 5.0.0** - Contains security vulnerabilities

### Base Image Vulnerabilities
- Uses Node.js 14.15.0 base image which contains known OS-level vulnerabilities

### Application-Level Vulnerabilities
- **Template Injection** - `/template` endpoint vulnerable to lodash template injection
- **Weak JWT Secret** - Uses predictable JWT signing secret
- **SSRF** - `/fetch` endpoint allows arbitrary URL fetching without validation
- **No Input Validation** - Various endpoints lack proper input sanitization

## Building and Running

### Build the Docker Image
```bash
docker build -t vulnerable-app:latest .
```

### Run the Container
```bash
docker run -p 3000:3000 vulnerable-app:latest
```

### Scan with Trivy
```bash
# Scan the built image
trivy image vulnerable-app:latest

# Scan for high and critical vulnerabilities only
trivy image --severity HIGH,CRITICAL vulnerable-app:latest

# Generate JSON report
trivy image --format json --output results.json vulnerable-app:latest
```

## API Endpoints

- `GET /` - Welcome message
- `GET /health` - Health check
- `POST /template` - Vulnerable template rendering
- `POST /jwt` - JWT token generation with weak secret
- `POST /fetch` - External URL fetching (SSRF vulnerable)

## GitHub Actions Workflows

This repository includes several GitHub Actions workflows for automated building and security scanning:

### 🔧 Workflows

1. **`security-scan.yml`** - Main security scanning workflow
   - Triggers on push to main branches, PRs, and weekly schedule
   - Builds Docker image with caching
   - Runs comprehensive Trivy scans (SARIF, JSON, table formats)
   - Uploads results to GitHub Security tab
   - Includes container security, config, filesystem, and secret scans

2. **`pr-security-check.yml`** - Pull request security validation
   - Runs on all pull requests
   - Provides detailed vulnerability summary in PR comments
   - Counts vulnerabilities by severity level
   - Uploads scan artifacts for review

3. **`reusable-build.yml`** - Reusable Docker build workflow
   - Used by other workflows to eliminate duplicate builds
   - Configurable image tagging and optional container testing
   - Efficient Docker layer caching
   - Outputs built image name for downstream jobs

4. **`build.yml`** - Container build and basic testing
   - Runs on feature branches (not main/master)
   - Uses reusable build workflow with container testing enabled
   - Health check validation

5. **`msdo-scan.yml`** - Microsoft Security DevOps scanning
   - Runs on feature branches or manual dispatch
   - Uses reusable build workflow + Microsoft Security DevOps Action
   - Integrates with Microsoft security tooling

6. **`quick-trivy-scan.yml`** - Quick vulnerability assessment
   - Runs on feature branches or manual dispatch
   - Uses reusable build workflow + focused Trivy scan
   - Fast feedback for development iterations

7. **`defender-cli-scan.yml`** - Microsoft Defender CLI scanning
   - Runs on feature branches or manual dispatch
   - Uses reusable build workflow + auto-detecting CLI download
   - Requires secret credentials configuration for full functionality

### 📊 Security Integration

- **SARIF Upload**: Results appear in GitHub's Security tab
- **PR Comments**: Automatic vulnerability summaries on pull requests
- **Artifacts**: Detailed scan results stored for 30 days
- **Scheduled Scans**: Weekly security checks to catch new vulnerabilities

### 🚀 Usage

The workflows run automatically, but you can also trigger them manually:
- Go to Actions tab in your GitHub repository
- Select the workflow you want to run
- Click "Run workflow"

## Warning

⚠️ **This application contains intentional security vulnerabilities and should NEVER be deployed in a production environment or exposed to the internet.**

Use only for security testing and educational purposes in isolated environments.
