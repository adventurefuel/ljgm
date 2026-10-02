# LJGM — Love Jesus Get Money landing page

Production build of the LJGM landing page (Red Box Tee + 7-day planner), built from the approved
mockups in `docs/` and the original handoff README. Static site, no build step, hosted on Render
like the agency's other client sites. Analytics run through the agency's shared Supabase project.

## Structure

- `index.html`: the landing page. Inline CSS/JS, fully responsive (desktop → tablet → phone).
- `admin.html`: passcode-protected analytics dashboard.
- `assets/red-box-tee.jpg`: hero product image (cropped and cleaned from the approved mockup).
- `assets/ljgm-mark.svg`: heart / cross / dollar mark, redrawn as a vector from the mockup.
- `assets/fonts/`: self-hosted Anton (headlines), Inter (body) and Caveat (handwritten notes), all SIL OFL.
- `docs/`: approved mockups (both copy versions) and the original prototype handoff, kept for reference only.

## What's on the page

- **Hero:** "Love Jesus / Get Money" headline, Red Box Tee, and a CTA to the Shopify product page
  (`lovejesusgetmoney.com/products/the-red-box-tee-holy-remix`).
- **Planner:** four questions (goal, why, obstacle, time), then a full 7-day plan built around the three
  pillars (Keep the Faith / Know the Money / Make the Move). The plan always shows first. Copy, print/PDF,
  and email are all optional. The plan is a front-end template built from the visitor's answers.
  It is not AI and not financial advice, and the page says so.
- **WhatsApp:** opens a chat with **+1 (407) 435-1127** with a prefilled greeting. To change the number,
  edit the `wa.me/14074351127` link in `index.html` (one place).
- **Socials:** Instagram `@lovejesusgetmoney` and the LJGM Facebook page (header + footer).
- **Mobile:** hamburger menu, stacked hero, the planner card first, a full-screen planner dialog,
  and no horizontal scroll at 360px and up.

## Copy A/B test

The two approved copy versions run as a 50/50 split:

| | The CEO Box (`ceo`) | Keep the Goal at Hand (`goal`) |
|---|---|---|
| Headline | Make your next 7 days count. | Turn your vision into a plan. |
| Button | Build my 7-day plan | Build my plan |
| Email CTA | Email my plan | Send my plan |

- Each visitor keeps the same version on return visits (stored in `localStorage`).
- Preview either version with `?v=ceo` or `?v=goal`.
- To end the test, set `window.LJGM_AB_MODE = "ceo"` (or `"goal"`) near the top of `index.html`.
- The admin dashboard compares plan-start, plan-finish, and shop-click rates per version.

## Analytics & admin dashboard

Events go to `ljgm_analytics_events` in the shared Supabase project (`cjixvpcoivfipmgmvomi`), and
plan email requests go to `ljgm_plan_requests`. The page uses the public *publishable* key. Row-level security lets that
key **insert only**, with whitelisted event names and length limits, and it can never read data back.

Tracked events: `page_view`, `shop_click` (hero / nav / after plan), `plan_open`, `plan_step` (2–4),
`plan_complete`, `plan_copy`, `plan_print`, `email_submit`, `whatsapp_click`, `social_click`, `nav_click`.
Each event also records the A/B version, an anonymous visitor ID, device size, referrer, and UTM tags. Use
UTM links in ads and bios (e.g. `?utm_source=instagram&utm_campaign=launch`) to see them under Traffic sources.

**Admin:** `https://<render-url>/admin.html`. Enter the passcode and the page calls the Supabase Edge
Function `ljgm-admin-stats`, which checks the passcode server-side and aggregates the data with the private
service-role key. That key is never sent to the browser.

- **Current passcode:** `GetMoney2026`. To rotate it, change `ADMIN_PASSCODE` in the
  `ljgm-admin-stats` function and redeploy it. The passcode is not stored in this repo.
- The dashboard shows visitors, shop clicks, plans completed, emails captured, a daily trend, the planner
  step funnel, the A/B comparison, shop clicks by button, traffic sources, devices, and the email list with a CSV export.
- `admin.html?demo` renders sample data, for layout checks only.
- Local previews (`localhost`, `file://`) and `?notrack` don't send events.

## Before launch / next steps

1. **Email delivery:** visitors who ask for a copy are saved to `ljgm_plan_requests` (with their plan
   text), and the page says the LJGM team will send it. To send it automatically, add a Supabase Edge Function
   on insert using an email provider such as Resend or Postmark.
2. **Logo:** the mark is a vector redraw of the mockup. If the official file `LJGM_Logo_-_Ready_for_DTF.png`
   from the Shopify store should be used instead, drop it into `assets/` and update the three `<img>` tags.
3. **Product photo:** the hero uses the tee from the approved mockup. To swap in a studio shot, replace
   `assets/red-box-tee.jpg`. A dark or transparent background works best.
4. Confirm the "Shop" nav link (`/collections/all`) and the privacy/consent wording for email capture with the client.

## Deploy

This is a Render static site that points at this repo's `main` branch with publish path `.`. There is no
build command, and every commit auto-deploys.
