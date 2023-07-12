## reusable gh actions can be shared

Caller/Usage Code Snippet/examples can be found at the top of the respective yml files 


Code Scanning:
- Trivy
- Husky
- Gitleaks (needs "gitleaks.toml" in the root dir for custom rules)
- SonarCloud (needs "sonar-project.properties" in the root dir), UNTESTED


Testing
- Playwright

example caller: https://github.com/button-inc/climatetrax-frontend/tree/develop/.github/workflows