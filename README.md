# Compliance

The compliance attestations an API provider states on its own trust center or compliance page — which frameworks, which type (e.g. SOC 2 Type II), what scope, what audit period, who issued it, and whether the report is public, under NDA or on request. A claim, recorded verbatim; not a verification.

This is the schema behind the [Compliance property](https://apicommons.org/common/compliance/)
in [API Commons](https://apicommons.org).

## Why it matters

API Commons was started to make the business side of API onboarding machine-readable. Finding an API is
the easy part; deciding whether you are allowed to use it — for this purpose, in this country, with this
data, by this kind of client — still means a person reading legal pages. When the answer is a document a
crawler, a catalog or an agent can read, discovery and onboarding can be automated end to end, and a
consumer can compare providers on the terms rather than on the marketing.

## Artifacts

- **[compliance-json-schema.yml](compliance-json-schema.yml)** — the JSON Schema (2020-12).
- **[compliance-example-1.yml](compliance-example-1.yml)** — a standalone document, as written to `all/<slug>/compliance/<slug>-compliance.yml`.
- **[compliance-example-2.yml](compliance-example-2.yml)** — the same document inside the `Attestations` apis.yml property envelope.
- **[validate.py](validate.py)** — validates any document against the schema.

Both examples describe **Example Weather API**, a fictional provider. The clauses are illustrative and are
not the terms of any real company.

## Fields

Every document carries the common envelope:

```yaml
compliance: 0.1.0
provider: Example Weather API
aid: example-weather-api
url: <the page it was read from>
source_date: YYYY-MM-DD
x-apis-io-derivation:
  synthetic: false
  method: harvested
  read: YYYY-MM-DD
  tool: engine/business/harvest-business.py
  model: <model id>
```

and then:

| Field | Shape | Meaning |
|---|---|---|
| `trust_center` | `{url}` | The trust center |
| `attestations[]` | `{framework, type, scope, period, issuer, report_access, text}` | framework: SOC1, SOC2, SOC3, ISO27001, ISO27017, ISO27018, ISO27701, ISO42001, ISO9001, PCI-DSS, HIPAA, HITRUST, FedRAMP, StateRAMP, GDPR, CCPA, CSA-STAR, TISAX, C5, IRAP, CyberEssentials, NIST-800-53, NIST-CSF, DORA, other; report_access: public / nda / on_request / unstated |

## The stance enum

Wherever a permission is described: `allowed | prohibited | conditional | unstated`. Every stance other
than `unstated` must carry a verbatim `text` — the schema enforces it. `unstated` means the page says
nothing, which is a valid, recorded result and is never filled in with a guess. Periods ("30 days",
"12 months") are copied as written and never normalized.

## Provenance: verbatim text, never synthetic

Only documents read from the provider's own page are published. There is no template fill. The page is
fetched by code; a model only transcribes rows from it; every row's `text` is checked word for word
against the fetched page and dropped if it does not match. `x-apis-io-derivation.synthetic` is always
`false` and `method` is always `harvested` — the schema rejects anything else.

Sources (apis.yml property types): TrustCenter, Compliance, Security (trust pages) and existing security/*-trust-center.yml.

## Standard it profiles

[NIST OSCAL](https://pages.nist.gov/OSCAL/) and the [CSA STAR Registry](https://cloudsecurityalliance.org/star/registry). This is a discovery-level index of attestations, not an OSCAL assessment: it records which frameworks a provider claims, under what scope and period, and whether the report is public — so a consumer knows what to request.

## Using it

```
pip install jsonschema pyyaml
python3 validate.py compliance-example-1.yml compliance-example-2.yml
```

Exits non-zero on any failure, so it can gate a pipeline.

## License

Two licenses, by kind of thing:

- **Artifacts** — the schemas and examples — are **[CC BY-NC-SA 4.0](LICENSE)** (Attribution–NonCommercial–ShareAlike).
- **Code** — the validator — is **[Apache-2.0](LICENSE-CODE)**.

## Part of API Commons

A machine-readable building block from **[API Commons](https://apicommons.org)**. Related business-layer blocks:
[terms-of-service](https://github.com/api-commons/terms-of-service) ·
[privacy-policy](https://github.com/api-commons/privacy-policy) ·
[deprecation-policy](https://github.com/api-commons/deprecation-policy) ·
[compliance](https://github.com/api-commons/compliance) ·
[eligibility](https://github.com/api-commons/eligibility) ·
[ai-preferences](https://github.com/api-commons/ai-preferences) ·
[data-license](https://github.com/api-commons/data-license) ·
[service-level-agreement](https://github.com/api-commons/service-level-agreement) ·
[plans](https://github.com/api-commons/plans) ·
[rate-limits](https://github.com/api-commons/rate-limits)
