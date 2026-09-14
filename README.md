# tapp cookie CMP – Google Tag Manager tag template

Tag template for [tapp cookie](https://tappcookie.cz), a consent management platform (CMP) for
websites in the EU. The template connects the tapp cookie banner to Google Consent Mode v2 inside
Google Tag Manager.

Fire it on **Consent Initialization – All Pages**. It then:

1. Sets the Consent Mode v2 **default** state. For a returning visitor with a valid stored
   choice (first-party cookie `tapp_consent`) the default mirrors that choice; for a new visitor
   everything is `denied` (`security_storage` is always `granted`). Both cases include
   `wait_for_update`, so tags wait until the banner confirms the choice. Optional regional
   defaults (`region`) let you grant by default where the banner is not shown.
2. Sets `ads_data_redaction` and, optionally, `url_passthrough`.
3. Loads the banner script `tappcookie.js` from the tapp cookie CDN unless the page already
   loaded it in `<head>` (recommended, see below).
4. After every decision in the banner calls `updateConsentState` with all seven signals and
   pushes `tapp_consent_update` to the data layer with
   `tapp_consent_analytics|marketing|preferences` booleans for your own triggers.

Category → signal mapping: necessary → `security_storage`; analytics → `analytics_storage`;
marketing → `ad_storage`, `ad_user_data`, `ad_personalization`; preferences →
`functionality_storage`, `personalization_storage`.

## Setup

1. In Google Tag Manager open **Templates → Tag Templates → Search Gallery**, find
   „tapp cookie CMP“ and add it to the workspace (or import `template.tpl` from this repository).
2. **Tags → New → tapp cookie CMP.** Fill in **Site ID** and **Tenant ID** – both are shown in the
   tapp cookie admin under _Installation_ (the `data-site` and `data-tenant` values). Keep the
   default CMP URL.
3. Trigger: **Consent Initialization – All Pages**.
4. Leave Google tags (GA4, Google Ads, Floodlight) on their built-in consent checks. For tags
   without built-in support set **Consent Settings → Require additional consent** to
   `analytics_storage` or `ad_storage`.
5. Verify in Tag Assistant: `Consent Initialization` → `consent default` (all denied) → after
   „Accept all“ a `consent update` with `gcs=G111`.

### Recommended: load the banner in `<head>` and use the template for the signal

Google Tag Manager injects scripts asynchronously, so a banner loaded only through this template
cannot block scripts that the HTML parser starts before it. The recommended setup is to put the
snippet from the tapp cookie admin (_Installation_) as the first script in `<head>` and keep this
template for the Consent Mode signal; the template detects the already loaded banner and does not
load it twice.

## Parameters

| Parameter                           | Meaning                                                                    |
| ----------------------------------- | -------------------------------------------------------------------------- |
| Site ID, Tenant ID                  | Identify the site in tapp cookie (admin → Installation).                   |
| CMP URL                             | Where `tappcookie.js` is loaded from. Default `https://cmp.tappcookie.cz`. |
| Banner language                     | Empty = follow `<html lang>`; otherwise `cs`, `en`, `sk`, `de`.            |
| Automatic script blocking           | On (recommended) or off when the site marks its own scripts.               |
| ads_data_redaction, url_passthrough | Google Consent Mode options, set via `gtagSet`.                            |
| wait_for_update (ms)                | Default 500.                                                               |
| Regional default state              | Optional per-region `granted` defaults (ISO 3166-1 / 3166-2).              |

## Permissions

Reads and writes all seven consent types, injects scripts only from
`https://cmp.tappcookie.cz/` (and the tapp cookie development CDN), reads the `tapp_consent`
cookie, accesses `dataLayer`, `TappCookie`, `TappCookie.on` and `__tappCookieConfig` on
`window`, writes `ads_data_redaction`, `url_passthrough`, `developer_id.*` and the
`tapp_consent_*` data layer keys, and logs in debug mode only.

## Versions

Versions are commits in this repository; each is listed in `metadata.yaml`. The template
source of truth lives in the tapp cookie monorepo and is published here by a script, so please
open issues rather than pull requests.

## Support

TAPP Studio s.r.o., info@tapp-studio.cz, https://tappcookie.cz. Licensed under the Apache
License 2.0 (see `LICENSE`).
