---
description: Keeps a root AGENTS.md to principles - moves a release procedure out and drops a file listing the code already shows.
tags: [agent-instructions, skill-fired]
max_turns: 10
allowed_tools: [Read, Glob, Grep, Skill]
---

I want our coding agents to stop asking me how things work, so I'm adding this to the AGENTS.md at
the root of the repo. Can you tidy it up and tell me if anything's missing?

```markdown
## Conventions

- Use pnpm, never npm.
- Money is always integer cents.

## Releasing

1. Make sure main is green.
2. Run `pnpm changeset version` and commit the result.
3. Tag the commit `vX.Y.Z` and push the tag.
4. Wait for the publish workflow, then check the package page on npm.
5. Post the changelog in the release channel.
6. If publish fails, delete the tag, fix, and start again from step 2.

## Billing module

| File                       | Exports                                   |
| -------------------------- | ----------------------------------------- |
| src/billing/invoice.ts     | createInvoice, voidInvoice, InvoiceStatus |
| src/billing/tax.ts         | computeTax, TAX_RATES                     |
| src/billing/stripe-sync.ts | syncCustomer, syncInvoice                 |
| src/billing/webhooks.ts    | handleStripeEvent                         |
```

Reply in your message; you don't need to edit files.
