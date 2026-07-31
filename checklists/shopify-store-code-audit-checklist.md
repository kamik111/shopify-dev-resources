# Shopify Store Code Audit Checklist

A working checklist for auditing a Shopify store you did not build — an inherited store, an acquisition, a client handover, or a theme a departed contractor left undocumented. Open it, run it top to bottom, tick items off as you go.

Every item is a check you can perform in the Shopify admin, the theme code editor, or the CLI. Reading changes nothing: the code editor only writes when you click **Save**.

Order matters. Back up first, work from the theme code outward, and stop immediately if anything in section 8 fires.

**Full write-up:** https://shopify.ecom-store.pro/blog/shopify-store-code-audit/

---

## 1. Before you touch anything

- [ ] Duplicated the live theme as a frozen backup — Shopify's own recommendation before customizing ([docs](https://help.shopify.com/en/manual/online-store/themes/managing-themes/duplicating-themes))
- [ ] Named the duplicate with today's date so it is identifiable six months from now
- [ ] Opened the duplicate in the editor to confirm it is restorable, not just listed
- [ ] Counted existing themes against the plan limit — up to 20 on Basic, Grow and Advanced; up to 100 on Shopify Plus — and pruned obsolete copies before adding more

## 2. Theme generation and architecture

- [ ] Identified the theme's generation: **vintage** (the original, pre–Online Store 2.0 architecture), **Online Store 2.0** (sections on all pages plus dynamic sources), or **theme blocks** (the latest generation, including the Horizon family) ([docs](https://help.shopify.com/en/manual/online-store/themes/managing-themes/versions))
- [ ] If vintage: noted that vintage themes are no longer sold in the Theme Store and that Shopify's free vintage themes receive security fixes only, not new features
- [ ] Recorded that upgrading to Online Store 2.0 is **not mandatory** — a vintage theme may legitimately stay if it meets the store's needs, so "vintage" is an input to the verdict, not the verdict
- [ ] Checked how app integrations hook in: ScriptTag is documented as a vintage-theme workaround only, and OS 2.0 themes must use theme app extensions with app blocks ([docs](https://shopify.dev/docs/apps/build/online-store/script-tag-legacy))
- [ ] On an acquisition: confirmed the source code is clean and modifiable, and that every paid or licensed element of the stack transfers with the business

## 3. Ghost code left behind by removed apps

Shopify's own wording: uninstalling apps often leaves the code edits, or **ghost code**, behind in the theme, which can cause quality and performance issues and takes merchants off their theme upgrade paths ([docs](https://shopify.dev/docs/apps/build/online-store/theme-app-extensions/migrate)).

- [ ] Listed every app the store has uninstalled (billing history and the admin's app list are the starting points)
- [ ] Searched `layout/theme.liquid` for each removed app's name and for unfamiliar `<script src>` references
- [ ] Searched `snippets/`, `sections/` and custom JS files for the same names
- [ ] Flagged orphaned, app-branded snippet files — the pattern looks like `loox-reviews.liquid` or `privy-popup.liquid`
- [ ] Flagged `{% render %}` / `{% include %}` tags pointing at snippet files that no longer exist (a guaranteed error — with the backup in place, removing the dangling reference only fixes things)
- [ ] Distinguished the two legacy patterns Shopify names: snippets added to the theme and wired in through layout/template/section edits, versus JavaScript injected into the theme that then injects HTML
- [ ] Verified the opposite case before deleting: app blocks from theme app extensions are removed automatically and entirely on uninstall, so a live app's block is not ghost code ([docs](https://shopify.dev/docs/apps/launch/built-for-shopify/requirements))
- [ ] Checked `config/settings_data.json` for app embed blocks that were disabled rather than removed — a disabled embed stays in that file with `disabled` set to `true` ([docs](https://shopify.dev/docs/apps/build/online-store/theme-app-extensions/configuration))
- [ ] Before uninstalling anything else: checked the App Store listing or contacted the developer via **Settings → Apps → Get support** for required uninstall steps, so you do not create the next generation of ghost code ([docs](https://help.shopify.com/en/manual/apps/uninstalling-apps))
- [ ] Noted whether any installed app edits theme files at all — Built for Shopify bars apps from adding, removing or editing theme files, with only three exceptions (page-builder apps, backup/restore apps, and SEO/content-locking/dev-tooling apps)

## 4. ScriptTags and the invisible tracking layer

- [ ] Set the boundary before hunting: Shopify automatically removes all script tags associated with an app when it is uninstalled, so a tracker still firing after uninstall is usually **theme-injected code**, not a live ScriptTag
- [ ] Confirmed there is no admin screen that lists active ScriptTags — the **Customer events** page shows only apps built on the Web Pixels API ([docs](https://help.shopify.com/en/manual/promoting-marketing/pixels/app-pixels)), and the `ScriptTag` resource is documented as Admin API only ([docs](https://shopify.dev/docs/api/admin-graphql/latest/objects/ScriptTag))
- [ ] Queried `scriptTags` through the Admin GraphQL API — or asked the app's developer — for anything that survived the theme search
- [ ] Cross-checked every tracking request firing on the storefront against the app or the theme code that is supposed to produce it
- [ ] Noted that apps installed **before February 1, 2025** were not affected by the ScriptTag creation ban itself, so an old install may legitimately own tags a new install could never create ([docs](https://shopify.dev/docs/apps/build/online-store/blocking-script-tags))

## 5. Dated liabilities: checkout, Scripts and ScriptTags

- [ ] Established the store's plan first — almost every date in this section splits Plus from non-Plus
- [ ] Checked whether the store still customizes the Thank you / Order status pages through additional scripts or script-tag apps; for non-Plus stores the deadline to upgrade those pages is **August 26, 2026**, after which they are auto-upgraded ([docs](https://help.shopify.com/en/manual/checkout-settings/customize-checkout-configurations/upgrade-thank-you-order-status/upgrade-guide))
- [ ] Checked for ScriptTags created before February 1, 2025: they continued working until August 28, 2025 for Plus shops and **August 26, 2026** for non-Plus shops
- [ ] For Plus stores: confirmed the August 28, 2025 Thank you / Order status deadline has passed and that customizations using additional scripts, script-tag apps or `checkout.liquid` on those pages were lost when automatic upgrades began in January 2026 ([docs](https://help.shopify.com/en/manual/checkout-settings/customize-checkout-configurations/upgrade-thank-you-order-status/plus-upgrade-guide))
- [ ] Verified scope before chasing `checkout.liquid`: it was always a Plus-only file, so on a non-Plus store the equivalent liability lives in additional scripts and script-tag apps
- [ ] Searched for logic that used to run on Shopify Scripts — discounts, shipping rules, payment rules. Scripts ceased to execute entirely on **June 30, 2026** and published Scripts were deactivated; editing and publishing had already ended April 15, 2026 ([docs](https://shopify.dev/docs/apps/build/functions/migrating-from-shopify-scripts))
- [ ] Where store behaviour changed silently, mapped the dead Script to its Functions replacement instead of debugging the theme

## 6. App inventory and spend

- [ ] Listed every installed app with its monthly cost, and totalled the spend
- [ ] Flagged duplicate-function pairs (two review apps, two email-capture apps) — the store is paying twice for one outcome
- [ ] Flagged apps nobody uses that still load scripts on every page
- [ ] Checked whether any installed app depends on a layer that is already gone or expiring (Scripts, legacy Thank you / Order status customizations)
- [ ] Recorded which apps use theme app extensions and which edit the theme directly — that split predicts your next cleanup

## 7. Access, staff and API scopes

- [ ] Reviewed staff accounts and removed everyone who has left
- [ ] Reviewed **collaborator** accounts separately — they are Shopify Partners granted access, and they do **not** count toward the store's user limit, which is exactly why they get forgotten ([docs](https://help.shopify.com/en/manual/your-account/users/security/collaborator-accounts))
- [ ] Confirmed two-step authentication is active on staff accounts (collaborators cannot log in without it at all)
- [ ] Reviewed admin-created custom apps and the store data their access tokens are allowed to reach
- [ ] Noted who can change those scopes: a staff or collaborator account needs **both** the "Manage and install apps and channels" and the "Develop apps" permissions ([docs](https://shopify.dev/docs/apps/build/authentication-authorization/access-tokens/generate-app-access-tokens-admin))
- [ ] Revoked or documented every access token, custom app and collaborator you cannot tie to current, active work

## 8. Tampering triage — stop the audit if any of these is true

If you find changes nobody can account for, this stops being a code-quality problem. Shopify's guidance is direct: if you think the account has been hacked or compromised, contact Shopify Support immediately ([docs](https://help.shopify.com/en/manual/privacy-and-security/account-security/account-security-best-practices)).

- [ ] Unknown redirects, template changes, ScriptTags, admin users or installed apps that nobody on the team added
- [ ] Contacted Shopify Support before touching any code
- [ ] Changed the password of the **email account** used to log in to Shopify, first
- [ ] Changed the Shopify user account password and activated two-step authentication
- [ ] Reviewed the Shopify user account for suspicious login activity, and reviewed financial accounts and banking details for unauthorized activity
- [ ] Restored the theme from a known-clean backup instead of hand-cleaning the live one

## 9. Liquid quality and tooling

- [ ] Ran Theme Check, the linter for the Liquid and JSON in a theme and its theme app extensions ([docs](https://shopify.dev/docs/storefronts/themes/tools/theme-check))
- [ ] Grepped for deprecated `{% include %}` tags and planned the migration to `{% render %}` with named parameters
- [ ] Checked whether a previous contractor silenced Theme Check warnings instead of fixing them — a clean lint run on a visibly messy theme is a finding, not a pass
- [ ] Grepped for hard-coded prices and currency symbols; they break the moment a new currency is enabled through Shopify Markets
- [ ] Pulled the theme locally with Shopify CLI so the audit runs against real files and real diffs, not a browser editor ([docs](https://shopify.dev/docs/storefronts/themes/tools/cli))
- [ ] Reviewed unbounded loops over large collections — rendering a big collection without a limit is a known drag on page generation, especially on a cache miss

## 10. Performance baseline

- [ ] Opened the Web Performance report and recorded the store's rank across all three Core Web Vitals ([docs](https://help.shopify.com/en/manual/online-store/web-performance/overview))
- [ ] Recorded LCP against the good threshold of 2.5 seconds and INP against the good threshold of 200 milliseconds
- [ ] Read the numbers for what they are: real user data from the past 30 days, not a lab score ([docs](https://help.shopify.com/en/manual/online-store/web-performance/web-performance-reports))
- [ ] Used the line chart to correlate app installs, theme updates and new code with the metric each one moved

## 11. The fix-or-rebuild verdict

- [ ] Wrote the verdict down with reasons: patch when the theme is a modern OS 2.0 or theme-blocks generation with shallow, documented edits; rebuild when it is vintage or buried in undocumented custom code
- [ ] Confirmed with the owner what a rebuild preserves — products, collections, menus, pages and blog posts are unaffected, because they are managed in the admin, separate from the theme ([docs](https://help.shopify.com/en/manual/online-store/themes/adding-themes))
- [ ] Confirmed what does **not** carry over, item by item: section and block settings, global theme settings such as colors and fonts, text and wording, code customizations including app-added code, and available theme templates
- [ ] Planned app re-activation on the new theme — app blocks and embeds have to be re-added and repositioned per theme
- [ ] Noted what happens on publish: the previously published theme is relocated rather than deleted, and its changes are preserved
- [ ] Delivered the audit as a written, prioritized fix list — each finding with a recommended fix, an effort estimate and an impact ranking, critical issues first — and stated explicitly whether implementation is included or quoted separately

## 12. Keep the store auditable afterwards

- [ ] Agreed one rule with the owner: one change, one backup — duplicate the theme before every edit
- [ ] Connected Shopify's GitHub integration so theme code syncs both ways and every change is tracked and reversible ([docs](https://shopify.dev/docs/storefronts/themes/tools/github))
- [ ] Started a change log: what was edited, when, and why
- [ ] Scoped and then revoked any outside access granted for the audit once the report has landed

---

**Full write-up, including the fix-versus-rebuild decision framework and what audits cost:** https://shopify.ecom-store.pro/blog/shopify-store-code-audit/

Every Shopify behaviour, date and limit above links to the official documentation it came from. Platform dates and behaviour change — re-verify anything load-bearing against the linked source before acting on it.

*Disclosure: this article was created with the help of AI.*
