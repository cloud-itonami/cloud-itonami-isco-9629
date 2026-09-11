# Contributing

`cloud-itonami-isco-9629` accepts contributions to the OSS actor, policy tests,
documentation, examples and open occupation blueprint.

## Development

```bash
kbb -M:test
```

Keep changes small and include tests for policy, audit, store or disclosure
behavior.

## Rules

- Do not commit real worker, site or operator data, credentials or operating
  documents.
- Keep production writes and disclosures behind ElementaryWorkGovernor.
- Treat this occupation's workflows as high-risk: add tests for permission,
  scope-exclusion, safety-escalation and audit logging.
- Never widen the closed op-allowlist to include a labour-work-execution
  op, a site-safety-clearance op (e.g. one that would declare a site
  safety cleared), or a site-safety-supervisor-override op, without a
  dedicated ADR and explicit human review.
- Document any new business-model or operator assumption in `docs/`.

## Pull Requests

PRs should describe:

- what behavior changed
- which policy invariant is affected
- how it was tested
- whether operator or certification docs need updates
