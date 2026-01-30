# Security Vulnerability Scanning Process

## Overview

This document describes the standardized process for conducting security vulnerability scans across the Backstack.io platform. It serves as a guide for both automated and manual security assessments.

## Table of Contents

1. [Scanning Tools](#scanning-tools)
2. [Scanning Process](#scanning-process)
3. [Interpreting Results](#interpreting-results)
4. [Creating Remediation Tickets](#creating-remediation-tickets)
5. [Best Practices](#best-practices)
6. [Continuous Monitoring](#continuous-monitoring)
7. [Lessons Learned](#lessons-learned)

## Scanning Tools

### npm audit (Primary Tool)

**Why npm audit:**
- Built into npm, no authentication required
- Covers the npm registry vulnerability database
- Provides CVE IDs, severity levels, and remediation guidance
- Supports JSON output for structured analysis

**Snyk CLI (Alternative):**
- Requires authentication (API token or browser login)
- More comprehensive vulnerability database
- Provides detailed exploitability analysis
- Supports code scanning beyond dependencies

**Current Recommendation:** Use npm audit for dependency scanning due to zero-configuration requirements.

### Registry Consideration

**Important:** AWS CodeArtifact does not support the npm security advisories API. When scanning, use the public npm registry:

```bash
npm audit --json --registry=https://registry.npmjs.org/
```

## Scanning Process

### Step 1: Prepare Environment

Ensure you're in the project root:
```bash
cd /home/jkelly/projects/backstack-io-v2
```

### Step 2: Scan Backend Services

Scan all 16 backend services located in `services/`:

```bash
# Example: Scan a single service
cd services/user-service
npm audit --json --registry=https://registry.npmjs.org/ > audit-results.json
cd ../..

# For comprehensive scanning, iterate through all services:
# - activity-log-service
# - agent-service
# - chat-service
# - gateway-service
# - local-tools-service
# - mcp-client-service
# - mcp-server-service
# - memory-service
# - metrics-service
# - notification-service
# - oauth-service
# - organization-service
# - secrets-service
# - security-policy-service
# - user-service
# - workspace-service
```

### Step 3: Scan Frontend Interfaces

Scan all 3 frontend interfaces located in `interfaces/`:

```bash
# Scan each interface:
cd interfaces/web
npm audit --json --registry=https://registry.npmjs.org/ > audit-results.json
cd ../..

cd interfaces/desktop
npm audit --json --registry=https://registry.npmjs.org/ > audit-results.json
cd ../..

cd interfaces/mobile
npm audit --json --registry=https://registry.npmjs.org/ > audit-results.json
cd ../..
```

### Step 4: Aggregate and Analyze Results

Collect all scan results into a consolidated report:
- Parse JSON outputs
- Group vulnerabilities by severity
- Identify common patterns
- Calculate totals and statistics

## Interpreting Results

### npm audit JSON Structure

```json
{
  "auditReportVersion": 2,
  "vulnerabilities": {
    "package-name": {
      "name": "package-name",
      "severity": "high",
      "via": [
        {
          "source": 123456,
          "name": "package-name",
          "dependency": "package-name",
          "title": "Vulnerability Title",
          "url": "https://github.com/advisories/GHSA-xxxx-xxxx-xxxx",
          "severity": "high",
          "cwe": ["CWE-79"],
          "cvss": {
            "score": 7.5,
            "vectorString": "CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:H"
          },
          "range": ">=0.0.0"
        }
      ]
    }
  },
  "metadata": {
    "vulnerabilities": {
      "total": 100,
      "critical": 0,
      "high": 50,
      "moderate": 45,
      "low": 5,
      "info": 0
    }
  }
}
```

### Severity Classification

**Critical (CVSS 9.0-10.0):**
- Remote code execution
- Authentication bypass in production systems
- Data breach potential
- **Action:** Immediate remediation required

**High (CVSS 7.0-8.9):**
- Significant security impact
- Potential for privilege escalation
- Authentication bypass in non-production
- DoS vulnerabilities affecting availability
- **Action:** Remediate within 1-2 weeks

**Moderate/Medium (CVSS 4.0-6.9):**
- Moderate security impact
- Requires specific conditions to exploit
- Development dependency vulnerabilities
- **Action:** Remediate within 1-2 months

**Low (CVSS 0.1-3.9):**
- Minimal security impact
- Difficult to exploit
- Informational vulnerabilities
- **Action:** Address during regular maintenance

### Key Metrics to Track

1. **Total Vulnerabilities:** Across all severities
2. **Unique Vulnerable Packages:** Number of distinct packages with issues
3. **Vulnerability Instances:** Total occurrences (same package in multiple services)
4. **Severity Distribution:** Breakdown by critical/high/moderate/low
5. **Service Coverage:** Percentage of services scanned
6. **Remediation Rate:** Percentage of vulnerabilities fixed over time

## Creating Remediation Tickets

### Ticket Structure

Each remediation ticket should include:

**Title Pattern:**
```
Fix [SEVERITY]: [Vulnerability Name] in [service/interface]
```

**Description Sections:**
1. **Vulnerability Summary:** Brief description and impact
2. **Affected Components:** List of services/interfaces
3. **CVE Details:** CVE IDs, CVSS scores, CWE categories
4. **Current State:** Vulnerable package versions
5. **Remediation:** Specific fix instructions
6. **Acceptance Criteria:** How to verify the fix
7. **Testing Notes:** Critical paths to test
8. **References:** Links to CVE advisories and documentation

### Grouping Strategy

**Group vulnerabilities when:**
- Same package affects multiple services (3+)
- Related vulnerabilities can be fixed together
- Coordinated deployment is beneficial

**Separate vulnerabilities when:**
- Different severity levels
- Different fix complexity
- Independent deployment required

### Priority and Estimation

**Priority Levels:**
- Priority 1 (Urgent): Critical severity
- Priority 2 (High): High severity
- Priority 3 (Normal): Moderate severity
- Priority 4 (Low): Low severity

**Estimation Guidelines:**
- 1 point: Simple package update, no breaking changes
- 2 points: Package update with minor config changes
- 3 points: Major version upgrade or multiple services
- 5 points: Library migration or significant refactoring
- Never exceed 8 points per Sprint Rules

### Labels

- **Bug:** For exploitable vulnerabilities
- **Improvement:** For low-risk or dev dependency issues
- **Security:** Tag all security-related tickets
- **Planned:** Indicates tickets are ready for sprint planning

## Best Practices

### Dependency Updates

1. **Review Release Notes:** Check for breaking changes before updating
2. **Update Dev Environment First:** Test in non-production
3. **Run Full Test Suite:** Ensure no regressions
4. **Test Authentication Flows:** Critical for auth-related vulnerabilities
5. **Verify Multi-Tenant Isolation:** Essential for SaaS applications
6. **Check Bundle Sizes:** Frontend updates can affect performance
7. **Review Environment Variables:** Ensure no secrets exposed

### Testing After Updates

**Functional Tests:**
- Core user workflows
- Authentication and authorization
- Data operations (CRUD)
- Integration points

**Security Tests:**
- Authentication flows
- Authorization checks
- Input validation
- API security
- Client-side security (XSS, prototype pollution)

**Performance Tests:**
- Bundle size (frontend)
- Memory usage
- Response times
- Resource consumption

### Deployment Strategy

**Phased Rollout:**
1. Update development environment
2. Run comprehensive tests
3. Deploy to staging
4. Monitor for 24-48 hours
5. Deploy to production during low-traffic window
6. Monitor production metrics

**Rollback Plan:**
- Document previous package versions
- Keep rollback scripts ready
- Monitor error rates and performance
- Have on-call engineer available

## Continuous Monitoring

### Automated Scanning

**Recommended Setup:**
- **GitHub Dependabot:** Automatic PRs for dependency updates
- **npm audit in CI/CD:** Fail builds on critical/high vulnerabilities
- **Scheduled Scans:** Weekly automated security scans
- **Slack/Email Alerts:** Notify team of new vulnerabilities

### Regular Reviews

**Monthly Security Review:**
- Run comprehensive vulnerability scan
- Review remediation progress
- Update risk assessments
- Plan next sprint security work

**Quarterly Security Audit:**
- Deep-dive security assessment
- Review access controls
- Audit environment variables
- Test disaster recovery procedures

### Metrics Dashboard

Track these metrics over time:
- Total vulnerabilities by severity
- Mean time to remediation (MTTR)
- Vulnerability trends
- Package freshness (days since update)
- Security score (custom metric)

## Lessons Learned

### From January 2026 Scan

**Key Findings:**

1. **Widespread Dependencies:**
   - `qs` package affected 14 services
   - `lodash` affected 15+ components
   - Consider consolidating dependency versions

2. **Development Dependencies:**
   - Many moderate vulnerabilities in dev tools (vitest, esbuild, vite)
   - Don't impact production but affect development security
   - Should still be addressed for secure development environment

3. **Multi-Tenant Security:**
   - Apollo Gateway access control issues critical for SaaS
   - Requires thorough testing of organization filtering
   - Field-level authorization must be validated

4. **Environment Variables:**
   - Storybook can expose secrets in builds
   - Audit all frontend builds for leaked credentials
   - Implement secret scanning in CI/CD

5. **No Fix Available:**
   - `xlsx` package requires library migration (BS2-764)
   - Plan for alternatives when no patch exists
   - Risk acceptance vs. mitigation decision required

### Process Improvements

1. **Use npm audit instead of requiring Snyk authentication**
   - Simpler workflow, no configuration needed
   - Adequate for dependency scanning

2. **Always specify --registry for CodeArtifact users**
   - AWS CodeArtifact doesn't support npm security advisories
   - Use public registry for scanning

3. **Group vulnerabilities intelligently**
   - Balance efficiency with deployability
   - Don't create tickets > 8 points

4. **Document migration paths for major upgrades**
   - eslint 8.x → 9.x
   - vitest 3.x → 4.x
   - Include breaking changes and migration guides

### Common Patterns Observed

**Vulnerable Packages:**
- `qs` (DoS): Very common, used by Express/body-parser
- `lodash` (Prototype pollution): Widespread usage
- `tar` (Path traversal): Build tool dependency
- `glob` (Command injection): Build tool dependency
- `esbuild`, `vite` (Various): Development tool vulnerabilities

**Vulnerability Categories:**
- Prototype Pollution (highest frequency)
- Denial of Service (second highest)
- Path Traversal (build-time risks)
- Command Injection (build-time risks)
- Authentication Bypass (runtime critical)

## Playbook Reference

A security scanning playbook has been created at:
`.opencode/skill/playbook/library/security-vulnerability-scanning/`

This playbook automates the scanning process and can be invoked using:
```
/playbook security-vulnerability-scanning
```

## Summary

This process ensures comprehensive, repeatable security vulnerability scanning across the Backstack.io platform. Regular execution of this process, combined with automated monitoring, provides a strong security posture and reduces risk of exploitation.

**Key Takeaways:**
1. Use npm audit with public registry for dependency scanning
2. Scan all services and interfaces comprehensively
3. Create well-structured, actionable remediation tickets
4. Group intelligently to balance efficiency and deployability
5. Test thoroughly, especially authentication and multi-tenancy
6. Monitor continuously with automated tools
7. Learn from each scan to improve the process

---

**Document Version:** 1.0
**Last Updated:** 2026-01-30
**Next Review:** 2026-02-28
