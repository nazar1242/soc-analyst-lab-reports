# Responsible Disclosure — Broken Access Control in a Mobile Health App (Welmi, Android)

> **Status:** Resolved & fixed by vendor · Published with vendor's written permission
> **Evidence:** [Vendor acknowledgement letter (PDF)](Welmi_Responsible_Disclosure_Acknowledgement.pdf)
> **Researcher:** Nazar Vlasiuk
> **Report class:** Broken Access Control / client-side trust for entitlement (CWE-284, CWE-862)

---

## Summary

While using the Welmi Android app (a nutrition/health tracker), I found that a
developer/QA debug surface was reachable inside a production build. Because of how
premium entitlement was being validated, this made it possible for a user to access
paid features without a valid subscription.

I reported this privately to the vendor, who confirmed the issue, deployed a fix, and
provided a written acknowledgement. At the vendor's request, this write-up intentionally
omits the specific reproduction steps, API endpoint paths, and internal-tooling
screenshots. The goal here is to document the **process and the class of issue**, not to
provide anything actionable against the (now-patched) app.

---

## The finding (high level)

The core issue was **broken access control**: the decision about whether a user was
entitled to premium functionality was effectively trusted from the client side rather
than being independently enforced by the backend for every protected action.

A developer/QA configuration surface — intended only for internal testing — was also
present and reachable in the shipping build. Exposed QA/debug tooling in production is a
finding in its own right, independent of the entitlement issue, because it widens the
surface a build exposes to an ordinary user.

Taken together, this meant a user could reach premium features that should have sat
behind a paywall and an active subscription.

> No attempt was made to access data belonging to any other user, and no high-volume or
> destructive actions were taken. Testing was limited to my own account and device.

---

## Why it matters

- **Revenue / entitlement integrity** — premium functionality that is enforced only in
  the UI can be reached without paying for it.
- **Production hygiene** — internal QA tooling should never ship in a release build; its
  presence suggests build-configuration gaps that can surface other issues.
- **Defense-in-depth principle** — the underlying lesson is a classic one: *never trust
  the client*. Authorization has to be re-checked server-side on every protected request.

---

## Remediation themes (high level)

The fix direction here is standard secure-design practice, and nothing below is specific
enough to aid an attacker:

1. **Enforce entitlement server-side.** Every protected action should validate the
   authenticated user's subscription against a trusted source (store receipt / billing),
   never a client-reported flag.
2. **Strip debug/QA tooling from release builds.** Use build flavors / conditional
   compilation and verify it as part of the release checklist, rather than relying on a
   hidden entry point.
3. **Confirm object-level authorization.** Ensure protected resources can only be read or
   modified by their authenticated owner.
4. **Add release regression tests.** Fail the build if any debug-only surface remains
   reachable in a production configuration.

---

## Disclosure timeline

| Date | Event |
|------|-------|
| 2026-09-18 | Issue discovered during normal use; reported privately to the vendor's support channel. |
| 2026-09 – 2026-10 | Coordination with the Welmi team. |
| 2026-10-05 | Vendor confirmed the issue was **resolved and fixed**, and issued a written acknowledgement (signed by the Head of Engineering). |
| 2026-10-05 | Vendor confirmed the acknowledgement's authenticity by email and granted permission to publish a high-level write-up. |

---

## Acknowledgement

The Welmi team acknowledged this report in writing and confirmed the fix. They were
responsive and professional throughout, and I appreciate how they handled the process.

The vendor's acknowledgement letter is included in this repository as
[`Welmi_Responsible_Disclosure_Acknowledgement.pdf`](Welmi_Responsible_Disclosure_Acknowledgement.pdf).
Its authenticity can be verified with the vendor at `support@welmi.ai`.

Per their request, the technical specifics (reproduction steps, endpoint paths, and
internal-tooling screenshots) were shared only privately with the vendor and are **not**
included in this public write-up.

---

## What I took away from this

- Responsible disclosure is as much about **process and communication** as it is about
  the finding itself: report privately, give the vendor time, and coordinate on what goes
  public.
- The most common real-world access-control bugs aren't exotic — they come down to a
  server trusting something it should have re-verified.
- Keeping a public write-up high-level, with the vendor's sign-off, lets you show the work
  in a portfolio without handing anyone a recipe.

*This disclosure is shared for educational and portfolio purposes, with the vendor's
permission, after the issue was remediated.*
