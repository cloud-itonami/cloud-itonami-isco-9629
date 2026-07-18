# Security Policy

This project handles elementary-labour site operating workflows. Treat
vulnerabilities as potentially high impact even when the demo data is
synthetic — this domain's failure modes include physical worker-safety
risk (manual-lifting/strain injury) and varied-site-condition/
outdoor-indoor exposure risk (weather, uneven or unfamiliar terrain,
equipment condition).

## Do Not Disclose Publicly

Report privately before opening public issues for:

- credential exposure
- real worker, site or operator data exposure
- authorization bypass
- ElementaryWorkGovernor bypass
- audit-ledger tampering
- over-disclosure in reports or exports
- unsafe robot action dispatch
- any path that lets a proposal reach a labour-work-execution
  decision, a site-safety-clearance decision (e.g. declaring a site
  safety cleared), or a site-safety-supervisor override decision

## Reporting

Use GitHub private vulnerability reporting when available for the repository.
If that is unavailable, contact the repository maintainers through the
cloud-itonami organization before publishing details.

Include:

- affected commit or version
- reproduction steps
- expected and actual behavior
- impact on worker/site data, policy enforcement or audit logging
- suggested fix, if known

## Production Guidance

- Store secrets outside Git.
- Keep real worker/site/operator data outside this repository.
- Run policy tests before deployment.
- Export and review audit logs regularly.
- Use least privilege for operators and service accounts.
