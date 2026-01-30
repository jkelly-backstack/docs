# Security Audit Report - Backstack.io v2
**Generated:** 2026-01-30
**Scope:** All frontend interfaces and backend services
**Tool:** npm audit

## Executive Summary

This security audit scanned all three frontend interfaces in the Backstack.io v2 project for known vulnerabilities using npm audit. The audit identified multiple security issues across the interfaces, with varying severity levels.

### Overall Statistics - Frontend Interfaces

| Interface | Critical | High | Moderate | Low | Total |
|-----------|----------|------|----------|-----|-------|
| Web       | 0        | 4    | 5        | 0   | 9     |
| Desktop   | 0        | 11   | 14       | 0   | 25    |
| Mobile    | 0        | 11   | 14       | 0   | 25    |
| **TOTAL** | **0**    | **26** | **33** | **0** | **59** |

---

## 1. Web Interface (/interfaces/web)

**Technology Stack:** Vite + Vue3 + TailwindCSS
**Total Dependencies:** 2,395 (828 prod, 1,543 dev, 125 optional)
**Total Vulnerabilities:** 9 (0 critical, 4 high, 5 moderate, 0 low)

### High Severity Vulnerabilities (4)

#### 1. glob - Command Injection (GHSA-5j98-mcp5-4vw2)
- **CVE Source:** 1109842
- **CVSS Score:** 7.5
- **CWE:** CWE-78 (OS Command Injection)
- **Affected Range:** 10.2.0 - 10.4.5
- **Description:** glob CLI allows command injection via -c/--cmd flag which executes matches with shell:true
- **Fix Available:** Yes

#### 2. qs - DoS via Memory Exhaustion (GHSA-6rw7-vpxm-498p)
- **CVE Source:** 1111755
- **CVSS Score:** 7.5
- **CWE:** CWE-20 (Improper Input Validation)
- **Affected Range:** <6.14.1
- **Description:** qs's arrayLimit bypass in bracket notation allows DoS via memory exhaustion
- **Fix Available:** Yes

#### 3. storybook - Environment Variable Exposure (GHSA-8452-54wp-rmv6)
- **CVE Source:** 1111900
- **CVSS Score:** 7.3
- **CWE:** CWE-200, CWE-538, CWE-541 (Information Exposure)
- **Affected Range:** 8.0.0 - 8.6.14
- **Description:** Storybook manager bundle may expose environment variables during build
- **Fix Available:** Yes
- **Impact:** Direct dependency, potential credential leakage

#### 4. tar - Path Traversal Vulnerabilities (Multiple CVEs)
- **CVE Sources:** 1112255, 1112329, 1112659
- **CVSS Score:** 8.8 (highest)
- **CWE:** CWE-22, CWE-176, CWE-59 (Path Traversal)
- **Affected Range:** <=7.5.6
- **Descriptions:**
  - Arbitrary file overwrite and symlink poisoning (GHSA-8qq5-rm4j-mr97)
  - Race condition via Unicode ligature collisions on macOS APFS (GHSA-r6q2-hw4h-h46w)
  - Hardlink path traversal (GHSA-34x7-hfp2-rc4v)
- **Fix Available:** Yes

### Moderate Severity Vulnerabilities (5)

#### 1. esbuild - CORS Bypass (GHSA-67mh-4wv8-2f99)
- **CVE Source:** 1102341
- **CVSS Score:** 5.3
- **CWE:** CWE-346 (Origin Validation Error)
- **Affected Range:** <=0.24.2
- **Description:** esbuild enables any website to send requests to development server and read responses
- **Fix Available:** Yes
- **Impact:** Development-time vulnerability

#### 2. js-yaml - Prototype Pollution (GHSA-mh29-5h37-fv8m)
- **CVE Source:** 1109802
- **CVSS Score:** 5.3
- **CWE:** CWE-1321 (Prototype Pollution)
- **Affected Range:** 4.0.0 - 4.1.0
- **Description:** Prototype pollution in merge (<<) operation
- **Fix Available:** Yes
- **Impact:** Direct dependency, potential for code injection

#### 3. lodash - Prototype Pollution (GHSA-xxjr-mmjv-4gpg)
- **CVE Source:** 1112455
- **CVSS Score:** 6.5
- **CWE:** CWE-1321 (Prototype Pollution)
- **Affected Range:** 4.0.0 - 4.17.21
- **Description:** Prototype pollution in _.unset and _.omit functions
- **Fix Available:** Yes

#### 4. lodash-es - Prototype Pollution (GHSA-xxjr-mmjv-4gpg)
- **CVE Source:** 1112453
- **CVSS Score:** 6.5
- **CWE:** CWE-1321 (Prototype Pollution)
- **Affected Range:** 4.0.0 - 4.17.22
- **Description:** Prototype pollution in _.unset and _.omit functions
- **Fix Available:** Yes

#### 5. vite - Path Traversal on Windows (GHSA-93m4-6634-74q7)
- **CVE Source:** 1109137
- **CWE:** CWE-22 (Path Traversal)
- **Affected Range:** 7.1.0 - 7.1.10
- **Description:** Allows server.fs.deny bypass via backslash on Windows
- **Fix Available:** Yes
- **Impact:** Direct dependency, platform-specific (Windows only)

---

## 2. Desktop Interface (/interfaces/desktop)

**Technology Stack:** Electron + Vue3
**Total Dependencies:** 2,395 (828 prod, 1,543 dev, 125 optional)
**Total Vulnerabilities:** 25 (0 critical, 11 high, 14 moderate, 0 low)

### High Severity Vulnerabilities (11)

#### 1. @apollo/composition - Access Control Issues (Multiple CVEs)
- **CVE Sources:** 1109763, 1109770
- **CVSS Score:** 7.5
- **CWE:** CWE-284, CWE-288, CWE-863 (Access Control)
- **Affected Range:** <=2.9.4
- **Descriptions:**
  - Improper enforcement of access control on interface types and fields (GHSA-mx7m-j9xf-62hw)
  - Improper enforcement on transitive fields (GHSA-m8jr-fxqx-8xx6)
- **Fix Available:** Yes

#### 2. @apollo/gateway - Resource Exhaustion DoS (Multiple CVEs)
- **CVE Sources:** 1103870, 1103871
- **CVSS Score:** 7.5
- **CWE:** CWE-770 (Allocation without Limits)
- **Affected Range:** <2.10.1
- **Descriptions:**
  - Query planner optimization bypass (GHSA-p2q6-pwh5-m6jr)
  - Named fragment expansion DoS (GHSA-q2f9-x4p4-7xmh)
- **Fix Available:** Yes
- **Impact:** Direct dependency, affects GraphQL gateway functionality

#### 3. body-parser - Inherits from qs DoS
- **CVSS Score:** 7.5
- **Affected Range:** <=1.20.3 || 2.0.0-beta.1 - 2.0.2
- **Description:** Inherits vulnerability from qs dependency
- **Fix Available:** Yes

#### 4. cacache - Inherits from tar
- **Affected Range:** 14.0.0 - 18.0.4
- **Description:** Inherits path traversal vulnerabilities from tar
- **Fix Available:** Yes

#### 5. express - Multiple Issues
- **CVSS Score:** 7.5
- **Affected Range:** 4.0.0-rc1 - 4.21.2
- **Description:** Inherits vulnerabilities from body-parser and qs
- **Fix Available:** Yes

#### 6. glob - Command Injection (Same as Web)
- **CVE Sources:** 1109842, 1109843
- **CVSS Score:** 7.5
- **Affected Range:** 10.2.0 - 10.4.5 || 11.0.0 - 11.0.3
- **Fix Available:** Yes

#### 7. jws - HMAC Signature Verification Bypass (GHSA-869p-cjfg-cm3x)
- **CVE Source:** 1111244
- **CVSS Score:** 7.5
- **CWE:** CWE-347 (Improper Verification of Cryptographic Signature)
- **Affected Range:** <3.2.3
- **Description:** auth0/node-jws improperly verifies HMAC signatures
- **Fix Available:** Yes
- **Impact:** Critical authentication bypass vulnerability

#### 8. make-fetch-happen - Inherits from cacache
- **Affected Range:** 7.1.1 - 14.0.0
- **Fix Available:** Yes

#### 9. qs - DoS (Same as Web)
- **CVE Source:** 1111755
- **CVSS Score:** 7.5
- **Fix Available:** Yes

#### 10. storybook - Environment Variables (Same as Web)
- **CVE Source:** 1111900
- **CVSS Score:** 7.3
- **Fix Available:** Yes

#### 11. tar - Path Traversal (Same as Web)
- **CVE Sources:** 1112255, 1112329, 1112659
- **CVSS Score:** 8.8 (highest)
- **Fix Available:** Yes

### Moderate Severity Vulnerabilities (14)

#### 1. @typescript-eslint/eslint-plugin - Depends on eslint
- **Affected Range:** <=8.0.0-alpha.62
- **Fix Available:** Yes (major version 8.54.0)

#### 2. @typescript-eslint/parser - Depends on eslint
- **Affected Range:** 1.1.1-alpha.0 - 8.0.0-alpha.62
- **Fix Available:** Yes (major version 8.54.0)

#### 3. @typescript-eslint/type-utils - Depends on eslint
- **Affected Range:** 5.9.2-alpha.0 - 8.0.0-alpha.62
- **Fix Available:** Yes (major version 8.54.0)

#### 4. @typescript-eslint/utils - Depends on eslint
- **Affected Range:** <=8.0.0-alpha.62
- **Fix Available:** Yes (major version 8.54.0)

#### 5. @vitest/coverage-v8 - Depends on vitest
- **Affected Range:** <=2.2.0-beta.2
- **Fix Available:** Yes (major version 4.0.18)

#### 6. @vitest/mocker - Depends on vite
- **Affected Range:** <=3.0.0-beta.4
- **Fix Available:** Yes (vitest 4.0.18)

#### 7. esbuild - CORS Bypass (Same as Web)
- **CVE Source:** 1102341
- **CVSS Score:** 5.3
- **Fix Available:** Yes (vitest 4.0.18)

#### 8. eslint - Stack Overflow (GHSA-p5wg-g6qr-c7cg)
- **CVE Source:** 1112686
- **CVSS Score:** 5.5
- **CWE:** CWE-674 (Uncontrolled Recursion)
- **Affected Range:** <9.26.0
- **Description:** Stack overflow when serializing objects with circular references
- **Fix Available:** Yes (major version 9.39.2)

#### 9. js-yaml - Prototype Pollution (Same as Web)
- **CVE Source:** 1109802
- **CVSS Score:** 5.3
- **Fix Available:** Yes

#### 10. lodash - Prototype Pollution (Same as Web)
- **CVE Source:** 1112455
- **CVSS Score:** 6.5
- **Fix Available:** Yes

#### 11. lodash-es - Prototype Pollution (Same as Web)
- **CVE Source:** 1112453
- **CVSS Score:** 6.5
- **Fix Available:** Yes

#### 12. vite - Path Traversal (Same as Web)
- **CVE Source:** 1109137
- **Fix Available:** Yes (vitest 4.0.18)

#### 13. vite-node - Depends on vite
- **Affected Range:** <=2.2.0-beta.2
- **Fix Available:** Yes (vitest 4.0.18)

#### 14. vitest - Multiple Dependencies
- **Affected Range:** 0.3.3 - 3.0.0-beta.4
- **Fix Available:** Yes (version 4.0.18)

---

## 3. Mobile Interface (/interfaces/mobile)

**Technology Stack:** Vue3 + Mobile Framework
**Total Dependencies:** 2,395 (828 prod, 1,543 dev, 125 optional)
**Total Vulnerabilities:** 25 (0 critical, 11 high, 14 moderate, 0 low)

### Vulnerability Summary

The mobile interface has an identical vulnerability profile to the desktop interface, with 25 total vulnerabilities distributed across the same packages.

**Note:** The identical results suggest the desktop and mobile interfaces share the same dependency tree or the mobile interface is built on the same Electron/desktop foundation.

### High Severity Vulnerabilities (11)
Same as Desktop interface - see Desktop section above.

### Moderate Severity Vulnerabilities (14)
Same as Desktop interface - see Desktop section above.

---

## Key Findings and Risk Assessment

### Critical Client-Side Security Issues

#### 1. Prototype Pollution (Moderate-High Risk)
- **Affected Packages:** js-yaml, lodash, lodash-es
- **Client-Side Impact:**
  - Can lead to XSS attacks if user input reaches vulnerable functions
  - Potential for property injection attacks
  - May bypass security controls that rely on object properties
- **Recommendation:** Immediate upgrade required, especially for js-yaml as it's a direct dependency

#### 2. Storybook Environment Variable Exposure (High Risk)
- **Affected:** All three interfaces
- **Client-Side Impact:**
  - API keys, tokens, and credentials may be exposed in production builds
  - Secrets embedded in Storybook builds could be extracted from client-side bundles
- **Recommendation:** Audit environment variables, upgrade storybook, verify no secrets in client bundles

#### 3. Authentication Bypass in jws (High Risk - Desktop/Mobile Only)
- **Affected:** Desktop and Mobile interfaces
- **Client-Side Impact:**
  - HMAC signature verification bypass could allow token forgery
  - May compromise JWT authentication if used in desktop app
- **Recommendation:** Critical upgrade if JWT/HMAC authentication is used

#### 4. Path Traversal Vulnerabilities (Medium Risk)
- **Affected Packages:** tar, vite
- **Client-Side Impact:**
  - Primarily development-time risk
  - vite vulnerability affects Windows development environments
  - tar vulnerabilities could affect build/deployment processes
- **Recommendation:** Upgrade dependencies, review file handling code

#### 5. DoS Vulnerabilities (Medium Risk)
- **Affected Packages:** qs, @apollo/gateway
- **Client-Side Impact:**
  - qs DoS affects request parsing (if used client-side)
  - Apollo Gateway DoS only relevant if GraphQL gateway runs client-side (unlikely)
- **Recommendation:** Upgrade dependencies, implement rate limiting

### Dependency Update Strategy

#### Immediate Actions (High Priority)
1. Upgrade storybook to >=8.6.15 (environment variable exposure)
2. Upgrade js-yaml to >=4.1.1 (prototype pollution, direct dependency)
3. Upgrade jws to >=3.2.3 (authentication bypass - desktop/mobile)
4. Upgrade qs to >=6.14.1 (DoS vulnerability)

#### Short-Term Actions (Medium Priority)
1. Upgrade lodash to latest version (prototype pollution)
2. Upgrade tar to >=7.5.7 (path traversal)
3. Upgrade glob to >=10.5.0 or >=11.1.0 (command injection)
4. Upgrade vite to latest stable version (path traversal)
5. Upgrade @apollo/gateway to >=2.10.1 (DoS - desktop/mobile)

#### Long-Term Actions (Maintenance)
1. Upgrade vitest to 4.0.18 (resolves cascading vite-related issues)
2. Upgrade eslint to >=9.26.0 (stack overflow fix)
3. Upgrade @typescript-eslint packages to 8.54.0 (requires major version bump)
4. Implement automated dependency scanning in CI/CD

### Testing Recommendations

After applying updates, perform:
1. **Unit Tests:** Verify all existing tests pass
2. **Integration Tests:** Test authentication flows (especially desktop/mobile)
3. **Security Tests:**
   - Test YAML parsing with malicious inputs (js-yaml)
   - Verify environment variables are not exposed in production builds
   - Test JWT/HMAC signature verification (if applicable)
4. **Build Tests:** Verify Storybook builds don't leak secrets
5. **Cross-Platform Tests:** Test on Windows (vite backslash bypass)

---

## Remediation Plan

### Phase 1: Critical Security Updates (Week 1)
- [ ] Update storybook in all interfaces
- [ ] Update js-yaml in all interfaces
- [ ] Update jws in desktop and mobile
- [ ] Update qs across all interfaces
- [ ] Run full test suite
- [ ] Security validation tests

### Phase 2: High-Priority Updates (Week 2)
- [ ] Update lodash/lodash-es
- [ ] Update tar
- [ ] Update glob
- [ ] Update vite
- [ ] Update @apollo/gateway
- [ ] Run integration tests
- [ ] Performance regression tests

### Phase 3: Development Tool Updates (Week 3)
- [ ] Update vitest to 4.x
- [ ] Update eslint to 9.x
- [ ] Update @typescript-eslint packages to 8.x
- [ ] Update development dependencies
- [ ] Verify development environment

### Phase 4: Process Improvements (Ongoing)
- [ ] Implement automated npm audit in CI/CD pipeline
- [ ] Set up Dependabot or Renovate for automatic dependency updates
- [ ] Establish monthly security review cadence
- [ ] Create security champions within development team
- [ ] Document secure coding practices for Vue3/TypeScript

---

## Appendix: Detailed CVE References

### CVE-to-Package Mapping

| CVE Source | Package | Severity | CVSS | Advisory |
|------------|---------|----------|------|----------|
| 1102341 | esbuild | Moderate | 5.3 | GHSA-67mh-4wv8-2f99 |
| 1103870 | @apollo/gateway | High | 7.5 | GHSA-p2q6-pwh5-m6jr |
| 1103871 | @apollo/gateway | High | 7.5 | GHSA-q2f9-x4p4-7xmh |
| 1109137 | vite | Moderate | 0.0 | GHSA-93m4-6634-74q7 |
| 1109763 | @apollo/composition | High | 7.5 | GHSA-mx7m-j9xf-62hw |
| 1109770 | @apollo/composition | High | 7.5 | GHSA-m8jr-fxqx-8xx6 |
| 1109802 | js-yaml | Moderate | 5.3 | GHSA-mh29-5h37-fv8m |
| 1109842 | glob | High | 7.5 | GHSA-5j98-mcp5-4vw2 |
| 1109843 | glob | High | 7.5 | GHSA-5j98-mcp5-4vw2 |
| 1111244 | jws | High | 7.5 | GHSA-869p-cjfg-cm3x |
| 1111755 | qs | High | 7.5 | GHSA-6rw7-vpxm-498p |
| 1111900 | storybook | High | 7.3 | GHSA-8452-54wp-rmv6 |
| 1112255 | tar | High | 0.0 | GHSA-8qq5-rm4j-mr97 |
| 1112329 | tar | High | 8.8 | GHSA-r6q2-hw4h-h46w |
| 1112453 | lodash-es | Moderate | 6.5 | GHSA-xxjr-mmjv-4gpg |
| 1112455 | lodash | Moderate | 6.5 | GHSA-xxjr-mmjv-4gpg |
| 1112659 | tar | High | 8.2 | GHSA-34x7-hfp2-rc4v |
| 1112686 | eslint | Moderate | 5.5 | GHSA-p5wg-g6qr-c7cg |

### CWE Categories Identified

| CWE | Category | Packages Affected | Risk Level |
|-----|----------|-------------------|------------|
| CWE-22 | Path Traversal | tar, vite | High |
| CWE-78 | OS Command Injection | glob | High |
| CWE-176 | Race Condition | tar | High |
| CWE-200 | Information Exposure | storybook | High |
| CWE-284 | Access Control | @apollo/composition | High |
| CWE-288 | Authentication Bypass | @apollo/composition | High |
| CWE-346 | Origin Validation | esbuild | Moderate |
| CWE-347 | Signature Verification | jws | High |
| CWE-674 | Uncontrolled Recursion | eslint | Moderate |
| CWE-770 | Resource Exhaustion | @apollo/gateway, qs | High |
| CWE-863 | Authorization | @apollo/composition | High |
| CWE-1321 | Prototype Pollution | js-yaml, lodash, lodash-es | Moderate-High |

---

## Conclusion

The security audit has identified 59 total vulnerabilities across the three frontend interfaces (web, desktop, mobile), with no critical vulnerabilities but a significant number of high and moderate severity issues. The most concerning findings are:

1. **Prototype pollution vulnerabilities** in widely-used libraries that could enable XSS attacks
2. **Environment variable exposure** in Storybook that could leak credentials
3. **Authentication bypass** in jws affecting desktop and mobile interfaces
4. **Multiple path traversal** vulnerabilities that could affect build processes

All identified vulnerabilities have fixes available, and the remediation plan provides a phased approach to address them systematically. Immediate action is recommended for the high-severity issues, particularly those affecting authentication and data protection.

**Report Status:** Complete
**Next Review:** 2026-02-28 (30 days)
