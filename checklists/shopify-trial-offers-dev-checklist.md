# Shopify Trial Offers: Pre-flight Checklist for Code and Docs

*Compiled by Alexandr M., Ecom Store Pro. Terms as read on Shopify's pages on November 15, 2026; pricing-page code as read on October 25, 2026.*

Shopify runs three trial routes that people constantly mix up. Use this list before you write any of them into an app, a tutorial, onboarding copy, a README or an AI system prompt.

## The three routes

| Route | Who it applies to | Term | Verify it at | Official source |
|---|---|---|---|---|
| Public signup | Anyone signing up at shopify.com | 3 days free, then $1/month for 3 months on most plans (US page; other countries show other amounts) | [shopify.com/free-trial](https://www.shopify.com/free-trial): enter an email, click Start free trial; after signup, Settings → Plan | [Pricing page](https://www.shopify.com/pricing), [Help Center: free trial](https://help.shopify.com/en/manual/intro-to-shopify/pricing-plans/free-trial) |
| AI-created store | A new store created with an AI tool (ChatGPT, Claude, Gemini, Grok, Muse, Perplexity, Microsoft Copilot), a vibe-code partner (Lovable, Manus, Replit, v0 by Vercel) or the Stripe Projects CLI (US only) | Free trial, "currently 4 months"; none if a CLI store is created on a paid plan | Help Center free-trial page, AI-tool section | [Setting up a store created with AI](https://help.shopify.com/en/manual/ai-powered-tools/connecting-ai-tools/setting-up-a-store-created-with-ai), [Stripe Projects CLI](https://help.shopify.com/en/manual/ai-powered-tools/connecting-ai-tools/stripe-projects), [Vibe-code partners](https://help.shopify.com/en/manual/ai-powered-tools/connecting-ai-tools/vibe-code-partners) |
| 1MBB | Black entrepreneurs in the US and Canada, registering through Operation HOPE | Basic plan at 1 unit of currency per month for the first 6 months (the page localizes the amount; we read €1): a paid discount, not a free trial | The program page | [shopify.com/1mbb](https://www.shopify.com/1mbb) |

## Before you write a term down

- [ ] Read the term from an official page today, and record the URL and the date next to it.
- [ ] Quote the Help Center's own boundary: trial and promotional duration "is determined by when you create your Shopify account and start your free trial", and "might vary by store location".
- [ ] Treat one page load as one sample. The pricing page ships an array named `banditAssignments` and templates such as `{{paidTrialLength}} months`, so the month count is a variable. That proves neither a longer nor a shorter term; Shopify doesn't publish the rules.
- [ ] Keep the 4-month figure hedged the way Shopify hedges it: "currently".
- [ ] Don't call 1MBB a "120-day free trial". That was earlier wording; the current offer is a paid rate of 1 unit of currency per month for 6 months.
- [ ] Don't present 1MBB as a public 6-month deal. It's the only confirmed 6-month route, and it is program-gated.

## In code and UI

- [ ] Store each term as data, `{ months, amount, currency, source, checkedOn }`, not as a string literal.
- [ ] Pluralize with `Intl.PluralRules` (CLDR categories). The pricing bundle carries `_one`, `_two`, `_few`, `_many` and `_other` keys, plus a separate one-month string.
- [ ] Test copy and layout with a count of 1.
- [ ] Localize the amount. On November 15, 2026, 52 country pages showed €1, £1, 150円 and ₹20 among others, and South Africa's showed US$1.
- [ ] Show the "as of" date wherever the term is displayed.
- [ ] Don't depend on minified identifiers. `useBanditRewardId` disappeared from the pricing page between September 29 and October 1, 2026.

## For a store created by an AI tool or the CLI

- [ ] For the Stripe Projects CLI, decide trial or paid plan at creation. A paid-plan store has no trial and can't move back to the trial plan.
- [ ] Log the event that started the clock: claim (partner), save (AI tool) or creation (CLI).
- [ ] The storefront starts password-protected. A paid plan chosen in the admin removes the password; a CLI store is then launched from the Online Store page.
- [ ] Charges for a plan chosen during the trial start after the trial ends.
- [ ] A trial that ends with no plan chosen leaves the store paused. Shopify doesn't extend or reset trials.

## Checking a live store's own term

- [ ] Open Settings → Plan. Shopify's [offers page](https://help.shopify.com/en/manual/your-account/manage-billing/offers) says that when a promotion is active, the plan details show the promotional rate and the date standard pricing begins.

## Longer write-ups

- [$1 for 3 months: the public offer, read against Shopify's help pages](https://shopify.ecom-store.pro/blog/shopify-3-month-trial/)
- [The 4-month free trial for AI-created stores](https://shopify.ecom-store.pro/blog/shopify-4-month-free-trial/)
- [The 6-month 1MBB offer and its dated status tracker](https://shopify.ecom-store.pro/blog/shopify-6-month-trial/)

*Disclosure: this checklist is AI-generated.*
