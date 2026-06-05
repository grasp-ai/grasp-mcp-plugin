---
name: finding-contacts
user-invocable: false
description: "Use when the user explicitly asks for person-level outreach contacts for a selected set of Grasp companies or buyers: work emails, phone numbers, person LinkedIn profiles, CEO/CFO/founder/owner contact details, corporate development or M&A people, decision-makers, or outreach lists."
---

# Finding Contacts

Use the dedicated contact lookup only after the company or buyer targets are clear. It is the outreach-grade workflow and is different from table fields, research columns, and one-company profile contacts.

Do not infer contact lookup from ranking, shortlist, acquisition-target, PE add-on, or buyer-universe requests. Use this skill only when the user explicitly asks for people, emails, person LinkedIn profiles, outreach, or contact routes.

## Contact Source Choice

- Outreach contacts for selected companies: use the `grasp_search_contacts` tool. Reserve it for selected domains where the user is likely to act on the result.
- Broad contact-like table fields: use the `table-enrichment` skill. Research columns can cover many companies, but they are not contacts lookup results.
- Reported/source fields: use the `table-enrichment` skill for company-level phone, website, generic email, or company LinkedIn fields when Grasp exposes them as table columns. These are not person-level outreach contacts.
- One-company profile contacts: use the `company-lookup` skill only for a one-company profile/deep-dive context. Do not use profile contacts as the default response to outreach requests.

## Guardrails

- Do not run contact lookup across a full long list by default. Reserve it for a trimmed set of companies planned for outreach.
- If the user asks for contacts for many companies, first narrow to a shortlist. If they insist on broad coverage across hundreds of companies, use research columns from the `table-enrichment` skill instead and state that they are not contact lookup results.
- Pull domains from inspected table rows or validated company profiles. Do not guess domains when using `grasp_search_contacts` tool.
- Respect the `grasp_search_contacts` tool's domain, title, and result limits.
- Founder-owned, owner-managed, PE-backed, family-owned, or management-owned target criteria are ownership criteria, not contact requests. Use `finding-contacts` only when the user asks for people or outreach details.

## Workflow

1. Identify the target companies or buyers.
   - From a table: use the `working-with-tables` skill to inspect rows and URL/domain columns.
   - From names: use the `company-lookup` skill to validate canonical domains.
2. Translate the outreach goal into role filters.
   - For specific roles such as CEO, CFO, Founder, Owner, Partner, Head of M&A, or Corporate Development, pass explicit `titles`.
   - Use `senior_only` only as a broad seniority hint when the user asks for senior decision-makers without naming specific roles.
   - Use `seniorities` only when the user asks for seniority classes rather than titles.
3. Call the `grasp_search_contacts` tool with selected domains, explicit titles/seniority when useful, and a concise `usage_rationale`.
4. Present contacts with company/domain, person, title, available contact fields, and missing coverage.

Never invent emails, LinkedIn URLs, source links, or people. Treat absent results as coverage gaps, not proof that no relevant contact exists.
