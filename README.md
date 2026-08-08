# getportside.com

Static marketing + sales site for Portside. No build step, no JavaScript, no
third-party requests — plain HTML/CSS designed from the app icon's palette
(`assets/icon.svg`, copied from `Portside/Specs/Portside Icon.svg`).

## Pages

- `index.html` — landing page (hero, features, terminal, safety, keyboard, pricing, FAQ)
- `privacy.html`, `terms.html`, `refunds.html` — the policy pages Paddle's
  account review requires before checkout can go live
- `styles.css` — all styling; palette tokens at the top

## Deploying

Any static host works. Intended setup: Cloudflare Pages with the domain's DNS
on Cloudflare — that also provides R2 (for hosting `Portside-x.y.zip` release
downloads + the update feed later) and Email Routing (to forward
`support@getportside.com`).

## Placeholders to resolve before launch

These are wired into the copy and need Ilie's decision or a real artifact:

1. ~~Price~~ — DECIDED (2026-08-08): **$14 one-time**, shown in the top
   bar, hero, and pricing card.
2. ~~Updates policy~~ — DECIDED (2026-08-08): **every update free, forever**;
   copy in the pricing card, FAQ, and terms says so.
3. ~~License scope~~ — DECIDED (2026-08-08): one license, active on **one Mac
   at a time**, self-serve transfer by activating on the new Mac; the
   displaced install lapses on its next license check. Copy in the FAQ,
   pricing card, terms, and privacy reflects this.
4. **Buy buttons** — currently inert (`href="#pricing"`); wire to Paddle
   checkout (Paddle.js overlay + price ID) once the Paddle account is approved.
5. **Hero app window** — a stylised HTML/CSS rendering; swap for a real
   screenshot (same for the terminal block if desired).
6. ~~Updater~~ — DECIDED (2026-08-08): **Sparkle**, in the first build sold
   (second third-party dependency; gets its decision record in the app repo
   when implemented). The update-check FAQ answer describes it.
7. **og:image** — points at the SVG icon; generate a PNG social card for
   platforms that don't render SVG.
8. ~~Trial~~ — DECIDED (2026-08-08): **7-day free trial**, full app; needs
   the trial mechanism in the app and a hosted build for the download links.

## Paddle account checklist (Ilie does these; the site satisfies the review)

1. Sign up at paddle.com (Paddle Billing). Verify the getportside.com domain.
2. Site review needs: product description (landing page), terms, privacy,
   refund policy — all present here once deployed.
3. Create the product + price; note the price ID for the Buy buttons.
4. License keys are **our** responsibility on Paddle Billing (their built-in
   licensing was Classic-only): a small Cloudflare Worker on the
   `transaction.completed` webhook generates an Ed25519-signed key, stores
   it, and emails it. Because the policy is one-active-Mac-at-a-time, the
   Worker also serves two endpoints: `activate` (key + hashed machine id →
   this Mac becomes the active seat, displacing the previous one) and
   `confirm` (piggybacked on the app's update check; a displaced install
   learns here and drops to unlicensed with a clear message). Signature
   verification stays offline; the server only arbitrates which Mac is
   active. Open knobs: confirmation cadence and offline grace period
   (e.g. check at launch, tolerate N days unreachable).
